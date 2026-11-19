---
title: "Proposal"
date: 2026-11-17
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

### 1. Overview of the problem and solution
Small restaurants often show their dishes on social media but take bookings through scattered channels. Staff struggle to collect requests, guests do not know whether a request arrived, and menu changes are made by hand in several places. AUREL brings these flows into one web application with centralized data, built on AWS serverless services (Next.js, API Gateway, Lambda, DynamoDB, Cognito, S3 and SES). It is a team project with source code and a working deployment: https://main.d2rkhtb7c0k5r7.amplifyapp.com/

### 2. Objectives and scope
**Objectives:** show the restaurant and its menu, receive table requests with a clear status, let staff manage the menu and requests, and give operators logs, metrics and alarms.
**In scope:** home, menu, venue photos, about, contact, reservation and admin pages; menu CRUD; reservation status updates; dish photo upload to S3; acknowledgement email within the SES sandbox.
**Out of scope:** payments, POS, physical table maps, overlapping dining durations, customer accounts, multiple branches and commercial SLA.

### 3. Problem to solve
Reservations are spread over several channels, guests get no clear acknowledgement, and staff edit the menu manually. The solution gives one place to receive requests, protect seat capacity, update the menu and observe the system.

### 4. Preliminary solution architecture
<a href="/fcj-workshop-sieg/images/5-Workshop/5.3-Architecture/architecture-en.png" target="_blank"><img src="/fcj-workshop-sieg/images/5-Workshop/5.3-Architecture/architecture-en.png" alt="AUREL website architecture on AWS" style="max-width:100%; width:900px;"></a>

Visitors load the site from Amplify Hosting and call API Gateway for the menu, reservations and contact. Admin sign-in goes through the Next.js BFF to Cognito, and admin requests pass a JWT authorizer. Lambda stores data in DynamoDB, sends acknowledgement emails through SES and keeps dish photos in a private S3 bucket. CloudWatch collects logs and metrics. See section 5.3 for details.

### 5. Plan and timeline
| Phase | Output | Milestone |
| --- | --- | --- |
| Design and local development | Pages, service layer, input validation, tests | Code and test results, 02–03/11 |
| AWS deployment | Three application stacks, Cognito admin, seeded data | Evidence on 03/11 |
| Content completion | Vietnamese fonts, 100 dishes, venue photos | Amplify jobs 3–5 succeeded |
| Documentation and operations | Bilingual report, diagram, monitoring, cost estimate | CloudWatch and Pricing Calculator evidence |
| Personal review and submission | Own diagram, event photos, blog links | In progress |

### 6. Cost estimation
Assumptions: 10,000 visits, 100,000 API requests and 100,000 SSR requests per month, 20 GB of delivered content, Lambda 256 MB at 200 ms, 1,000 emails and 5 admin users, Singapore Region, USD, before tax and free tier.

| Item | Monthly cost (USD) |
| --- | --- |
| AWS Pricing Calculator workload estimate (20 usage lines, 8 services) | about 6.00 |
| Independent budget model, including allowances for transfer, dashboard and CodeCommit | 12.17 |
| Independent budget with a 25% reserve | 15.21 |

The two figures differ because the independent model adds allowances (dashboard, data transfer, CodeCommit, report build) and uses conservative rates. Neither is a bill, and the Calculator figure covers only the 20 entered lines. Not yet included: WAF, NAT, SQS, custom domain, paid support and restore or export of DynamoDB.

### 7. Risks and mitigation
| Risk | Impact | Mitigation |
| --- | --- | --- |
| SES sandbox | Unverified guests get no email | Save the request first, state the limit, request production access when appropriate |
| Email fails after the database write | Request exists without an email | Staff still see it; an outbox with SQS and a DLQ is proposed |
| Seat contention | Over-booking | Transaction with conditional counter update |
| API abuse | Higher load and cost | Validation, honeypot, limiter and throttling; no WAF yet |
| Broad account permissions | Larger blast radius | Resource-scoped runtime roles; operate outside the root user |
| Retained resources | Charges continue after stack deletion | Inventory, backup and controlled manual clean-up |
