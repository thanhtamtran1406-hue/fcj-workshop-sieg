---
title: "Architecture and data flow"
date: 2026-11-26
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

<a href="/fcj-workshop-sieg/images/5-Workshop/5.3-Architecture/architecture-en.png" target="_blank"><img src="/fcj-workshop-sieg/images/5-Workshop/5.3-Architecture/architecture-en.png" alt="AUREL website architecture on AWS" style="max-width:100%; width:900px;"></a>

**Visitor flow**
1. The browser loads pages, scripts, images and fonts from Amplify Hosting over HTTPS.
2. `GET /menu` goes straight to API Gateway; Lambda queries the `menu` partition and returns the dishes that are available.
3. `POST /reservations` is validated for schema, consent, a honeypot field, IP and email limits, and the Vietnam date and time.
4. DynamoDB saves the request and increases the seat counter in one transaction (maximum 24 seats per slot). Sending the same UUID again returns the stored request without adding seats.
5. After a new request is saved, Lambda tries to send an acknowledgement email through SES. If the email fails, the request is still stored. SES is in the sandbox, so only verified addresses or the mailbox simulator receive mail.
6. `POST /contact` stores the message. Visitor data cannot be read through the public API.

**Admin and media flow**
- The browser sends the sign-in to the Next.js BFF, which authenticates with Cognito and keeps the access token in an `HttpOnly`, `SameSite=Strict` cookie (`Secure` when deployed). The BFF forwards it in the `Authorization` header. The API Gateway JWT authorizer checks signature, issuer, audience and expiry; Lambda also checks `token_use=access` and the `admin` group.
- Dish photos (JPEG, PNG or WebP, up to 2 MB) go through the BFF and API Gateway to Lambda, which stores them under `menu-images/` in a private S3 bucket and saves the path in DynamoDB. When a visitor views the menu, Lambda returns a signed GET URL for the current image version.
- In the diagram, arrow 7 links Cognito to S3 Media. The real flow is: Cognito provides the admin access token, Lambda writes to S3 with its IAM execution role, and the browser reads images with the signed GET URL. A separate test page `/admin/media` uses signed PUT (120 s) and GET (900 s) URLs, and files under `uploads/` expire after 30 days.

**DynamoDB model:** one on-demand table with encryption and point-in-time recovery.

| pk | sk | Content |
| --- | --- | --- |
| menu | dish ID | Menu items, public query |
| reservations | UUID | Reservation data and status, admin only |
| contacts | UUID | Messages, admin only |
| slots | date#time | Seat counter with conditional update |
| limits | hash + time bucket | Request counters with TTL |

**Deployment flow:** a Git push to the `main` branch of the private CodeCommit repository triggers an Amplify build that publishes the frontend and the BFF. The backend is deployed separately with CloudFormation; a separate S3 bucket holds the Lambda package. CloudWatch collects logs and metrics.
