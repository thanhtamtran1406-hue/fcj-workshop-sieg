---
title: "Overview and completion criteria"
date: 2026-11-26
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

AUREL is a demo restaurant website with a dark and gold look. Visitors browse a 100-dish menu, view venue photos with credited sources, and send a table request or a message. Staff sign in with Cognito accounts in the `admin` group to manage the data. This workshop is a team project of our FCAJ group.

**Technology**
- Frontend: Next.js 15.5.28, React 19, TypeScript, Tailwind CSS, Zod, Node.js 22, pnpm 11.25.0.
- Backend in Singapore (`ap-southeast-1`): API Gateway HTTP API, Lambda (arm64, 256 MB, 20 s timeout), DynamoDB on-demand, Cognito, S3 and SES. Amplify Hosting serves the Next.js app with server-side rendering.

**Business rule:** a new reservation is only a request with status `pending`. Staff confirm or cancel it later, so a successful form submission does not mean the table is confirmed.

**Completion criteria (expected outputs)**
- A 7-page website whose forms work against the backend.
- A 100-dish menu with self-hosted WebP images.
- Public and admin APIs with clear access boundaries.
- A transaction that protects seat capacity per time slot and a UUID that prevents duplicate requests.
- Infrastructure templates, deployment steps, tests, monitoring and clean-up guidance.

**Known limits:** SES is still in the sandbox, there is no outbox or queue for emails, the admin MFA screen is not built, capacity is counted per start time only, and the venue photos are reference material rather than photos of a real AUREL restaurant.

Source code: <https://github.com/ndthuy21/aurel-restaurant> · Live site: <https://main.d2rkhtb7c0k5r7.amplifyapp.com/>
