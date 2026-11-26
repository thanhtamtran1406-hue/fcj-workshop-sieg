---
title: "Troubleshooting, lessons and next steps"
date: 2026-11-26
weight: 13
chapter: false
pre: " <b> 5.13. </b> "
---

### Common problems and fixes
| Symptom | Likely cause | What to do |
| --- | --- | --- |
| Browser shows a CORS error after deployment | `FrontendOrigin` is still `https://pending.invalid` | Redeploy the backend with the real frontend origin |
| Menu is empty or the app cannot reach the API | `API_BASE_URL` or `NEXT_PUBLIC_API_BASE_URL` is missing or ends with a slash; `NEXT_PUBLIC_*` values are fixed at build time | Correct the variables and start a new Amplify job |
| `/admin/*` returns 401 after signing in | An ID token was sent, or the access token (valid for 1 hour) expired | Sign in again; the BFF must forward the access token |
| A reservation is saved but no email arrives | SES is in the sandbox and the recipient is not verified | Verify the address or use the mailbox simulator |
| The Amplify job fails | Wrong Node version or missing variables | Read the build log, use Node 22 and the variable table |
| Resources remain after deleting stacks | Retain policy on the table, buckets and repository | Follow the clean-up page |

### Lessons learned
- Write down every output of a stack right after creating it; later steps depend on them.
- Keep the deployment package bucket separate from the media bucket.
- Test the failure paths (401, 400) as well as the happy path.
- State the limits honestly (SES sandbox, no WAF, no load test) instead of hiding them.

### Next steps
- Email outbox with SQS and a dead-letter queue, then SES production access.
- MFA for the admin, and AWS WAF in front of the API.
- A load test and a point-in-time recovery drill.
- SNS notifications for the four alarms.
