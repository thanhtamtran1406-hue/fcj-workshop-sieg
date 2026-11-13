---
title: "Architecture & Data Flow"
date: 2026-11-11
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

<a href="/fcj-workshop-sieg/images/5-Workshop/5.3-Architecture/architecture-en.png" target="_blank"><img src="/fcj-workshop-sieg/images/5-Workshop/5.3-Architecture/architecture-en.png" alt="AUREL architecture on AWS" style="max-width:100%; width:1200px;"></a>

**Data flow** (numbers follow the diagram)
1. The visitor opens the website over HTTPS. AWS Amplify Hosting serves the Next.js static and SSR pages through its CDN.
2. Public requests (`GET /menu`, `POST /reservations`, `/contact`) go from the browser to API Gateway, then to Lambda.
3. The administrator opens the dashboard over HTTPS. The Next.js Admin BFF (`/api/auth/*` and `/api/admin/*`) signs in with Cognito and keeps the session in an `HttpOnly`, `Secure`, `SameSite` cookie. The access token is valid for 1 hour.
4. The BFF sends the Bearer access token to API Gateway. `/admin/*` routes use a JWT authorizer (JWKS validation with Cognito); public routes need no auth. API Gateway applies CORS for the exact origin and throttling.
5. Lambda (Node.js 22, arm64, 256 MB) validates input, rate limits, and queries DynamoDB. Admin routes require an access token and membership of the `admin` group. The Lambda execution role is limited by ARN to the DynamoDB table, `uploads/*` in S3 and the SES identity.
6. For media, Lambda returns a signed URL, and the admin uploads (PUT, 120 s) or views (GET, 900 s) the file directly in S3; the file does not pass through Lambda. The bucket is private with Block Public Access and SSE-S3.
7. After a reservation is saved, Lambda calls SES `SendEmail` (best effort, SES is in the sandbox). There is no queue to retry a failed email.
8. Deployment: the operator pushes code to the source repository and Amplify builds and publishes the frontend automatically. The backend is deployed separately with AWS CLI and SAM through CloudFormation, using a Lambda ZIP stored in a separate S3 artifacts bucket.

**Data model:** one DynamoDB table with keys `pk` and `sk`, holding `menu`, `reservations`, `contacts`, `slots` (seat counters per time slot, maximum 24 guests per slot) and `limits` (rate limiting with TTL).
