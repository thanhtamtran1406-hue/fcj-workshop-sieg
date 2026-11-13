---
title: "Testing & Validation"
date: 2026-11-11
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

### Local checks
```sh
pnpm check          # TypeScript, unit/integration tests, bundle Lambda + seed
pnpm build
pnpm test:smoke     # separate server on port 3100, temporary data
```

### Checks on AWS after deployment
1. `/menu` returns 100 dishes after seeding, and the browser Network tab shows no CORS error.
2. Book a table with an email verified in the SES sandbox; check the DynamoDB record, the SES email and the `pending` status.
3. Sign in as an admin in the group, confirm a reservation, reload and compare with the database.
4. A user outside the group and an expired token must be rejected; the public cannot read the reservation or message lists.
5. Add, edit, hide and delete a dish; the menu page reflects the API data.
6. Upload a test image in the S3 tab; the PUT succeeds and the preview shows. The bucket has no public read access.
7. Check the Lambda logs in CloudWatch and the Amplify build and SSR logs, not only the frontend messages.

### Logs
- Lambda logs in CloudWatch, retention 14 days (email delivery errors are logged by request code, without email content).
- Amplify build and SSR logs.

### Metrics
- Basic metrics in CloudWatch for Lambda and Amplify.

### Alerts
- No dedicated AUREL alarm has been configured yet.
