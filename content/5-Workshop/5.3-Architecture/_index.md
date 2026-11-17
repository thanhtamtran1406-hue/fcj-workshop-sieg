---
title: "Architecture & Data Flow"
date: 2026-11-15
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

<a href="/fcj-workshop-sieg/images/5-Workshop/5.3-Architecture/architecture-en.png" target="_blank"><img src="/fcj-workshop-sieg/images/5-Workshop/5.3-Architecture/architecture-en.png" alt="AUREL website architecture on AWS" style="max-width:100%; width:900px;"></a>

**Data flow** (numbers follow the diagram)
1. The customer or admin opens the website over HTTPS. AWS Amplify Hosting serves the Next.js pages (static and SSR) and the Admin BFF.
2. For admin sign-in, the Next.js server authenticates with Amazon Cognito and keeps the session in an `HttpOnly`, `Secure`, `SameSite` cookie. The access token is valid for 1 hour.
3. Requests go to Amazon API Gateway (HTTP API). Public routes need no authentication; `/admin/*` routes use a JWT authorizer with the token issued by Cognito. API Gateway applies CORS for the exact origin and throttling.
4. API Gateway invokes AWS Lambda (Node.js 22), which validates input and applies rate limits. Admin routes require an access token and membership of the `admin` group.
5. Lambda reads and writes DynamoDB (menu, reservations, contacts, slots and rate limits).
6. After a reservation is saved, Lambda calls Amazon SES to send an email (best effort; SES is in the sandbox).
7. For media, Lambda returns signed URLs and the admin uploads or views images directly in the private S3 media bucket (PUT valid for 120 s, GET valid for 900 s).

**Supporting components**
- **IAM execution role:** limits Lambda by ARN to the DynamoDB table, `uploads/*` in S3 and the SES identity.
- **CloudWatch:** collects logs and metrics from API Gateway and Lambda.
- **Deployment:** the operator pushes code to the source repository and Amplify Build builds and publishes the frontend. The backend is deployed separately with AWS CloudFormation and SAM, using a Lambda ZIP stored in a separate S3 artifacts bucket.

**Data model:** one DynamoDB table with keys `pk` and `sk`, holding `menu`, `reservations`, `contacts`, `slots` (seat counters per time slot, maximum 24 guests per slot) and `limits` (rate limiting with TTL).
