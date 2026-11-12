---
title: "Architecture & Data Flow"
date: 2026-11-11
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

<img src="/fcj-workshop-sieg/images/5-Workshop/5.3-Architecture/architecture.png" alt="AUREL architecture on AWS" style="max-width:100%; width:800px;">

**Data flow**
1. The guest opens the Next.js website hosted on AWS Amplify Hosting.
2. Public requests (menu, reservations, contact) go directly from the browser to API Gateway through `NEXT_PUBLIC_API_BASE_URL`, then to Lambda and DynamoDB. After saving a new reservation, Lambda tries to send a confirmation-request email through SES.
3. Admin sign-in is done by the Next.js server against the Cognito user pool (`USER_PASSWORD_AUTH`). The backend-for-frontend (BFF) stores the access token in an `HttpOnly`, `SameSite=Strict` cookie (`Secure` in production), not in localStorage.
4. Admin requests go through the BFF `/api/admin/*` to API Gateway with the access token. API Gateway verifies the JWT; Lambda also requires `token_use=access` and membership of the `admin` group.
5. For media upload, Lambda returns a signed URL: PUT valid for 2 minutes and preview valid for 15 minutes. Images are stored in a private S3 bucket.

**Data model:** one DynamoDB table with keys `pk` and `sk`, holding `menu`, `reservations`, `contacts`, `slots` (seat counters per time slot, maximum 24 guests per slot) and `limits` (rate limiting with TTL).
