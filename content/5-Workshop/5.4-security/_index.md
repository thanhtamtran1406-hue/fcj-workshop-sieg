---
title: "Security and Well-Architected"
date: 2026-11-26
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

### Security controls in the design
| Area | Control |
| --- | --- |
| Identity | Cognito user pool with an admin group and no self-registration; the admin user is created by a script that asks for the password |
| Authorization | JWT authorizer on /admin/* (signature, issuer, audience, expiry); Lambda also checks token_use=access and the admin group, so an ID token is rejected |
| Session | HttpOnly, SameSite=Strict and Secure cookie handled by the Next.js BFF; no token in localStorage |
| Least privilege | Lambda execution role limited by ARN to the DynamoDB table, the image prefixes in the private S3 bucket and the SES identity |
| Data at rest | DynamoDB encryption and point-in-time recovery; private S3 bucket with Block Public Access and SSE-S3 |
| Data in transit | HTTPS through Amplify Hosting and API Gateway |
| Input and abuse | Schema validation, consent check, honeypot field, IP and email limits with TTL counters, API Gateway throttling, CORS for the exact frontend origin |
| Log privacy | The access log keeps no cookies, authorization headers or request bodies |

### Well-Architected view
| Pillar | What the project does | Remaining gap |
| --- | --- | --- |
| Operational excellence | CloudFormation templates, access log, dashboard, four alarms and a runbook | Alarm actions are not connected to SNS yet |
| Security | The controls above | No WAF, no admin MFA screen, SES still in the sandbox |
| Reliability | On-demand DynamoDB with point-in-time recovery, idempotent reservations, transaction for seat capacity | No restore drill, no email queue, one Region |
| Performance efficiency | arm64 Lambda with 256 MB, CDN on Amplify Hosting, on-demand capacity | No load test yet (target: p95 under 2 s) |
| Cost optimization | Pay-per-use services, 14-day log retention, two cost estimates | Estimate not yet compared with a real bill |
| Sustainability | No idle servers; arm64 functions | Not measured |
