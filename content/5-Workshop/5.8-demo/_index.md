---
title: "Demo, lessons and next steps"
date: 2026-11-26
weight: 8
chapter: false
pre: " <b> 5.8. </b> "
---

### Live product
- **Website (Amplify):** <https://main.d2rkhtb7c0k5r7.amplifyapp.com/>
- **Admin page:** <https://main.d2rkhtb7c0k5r7.amplifyapp.com/admin>
- **Source code:** <https://github.com/ndthuy21/aurel-restaurant>

The admin password is not published in this report; the presenter signs in on their own device. Dish names, prices, brand and address are sample data, so do not enter real customer information when trying the demo.

### Demo video
<iframe width="720" height="405" src="https://www.youtube.com/embed/_Dpp0RJAG_w" title="AUREL demo video" allowfullscreen style="max-width:100%; border:0;"></iframe>

[Watch on YouTube](https://www.youtube.com/watch?v=_Dpp0RJAG_w) · Team FantasticFive video "Dự án AWS Nhóm FantasticFive", posted by Huỳnh Trí Nhân on 24/11/2026 (about 4 minutes 21 seconds according to the group's report). It shows the customer pages, the contact and reservation forms, and the admin screen for managing the menu.

### Suggested demo script
1. Introduce the problem and open the architecture diagram.
2. Menu: search, filter and page through the 100 dishes loaded from the API.
3. Reservation form: send a valid request, then a wrong body to show the backend validation.
4. Admin: sign in, open the pending request and change its status.
5. Create a test dish with an image upload and check it on the menu page.
6. Show CloudWatch: request count, 4xx and 5xx, logs and alarms.
7. Questions: SES sandbox, admin MFA, quotas and the proposed email queue.

### More evidence
Photo evidence: the AUREL home page running on Amplify Hosting.

<img src="/fcj-workshop-sieg/images/5-Workshop/5.8-demo/home-screenshot.jpg" alt="AUREL home page running on Amplify Hosting" style="max-width:100%; width:900px;">

### Common problems and fixes
| Symptom | Likely cause | What to do |
| --- | --- | --- |
| Browser shows a CORS error after deployment | FrontendOrigin is still https://pending.invalid | Redeploy the backend with the real frontend origin |
| Menu is empty or the app cannot reach the API | API_BASE_URL or NEXT_PUBLIC_API_BASE_URL is missing or ends with a slash; NEXT_PUBLIC_* values are fixed at build time | Correct the variables and start a new Amplify job |
| /admin/* returns 401 after signing in | An ID token was sent, or the access token (valid for 1 hour) expired | Sign in again; the BFF must forward the access token |
| A reservation is saved but no email arrives | SES is in the sandbox and the recipient is not verified | Verify the address or use the mailbox simulator |
| The Amplify job fails | Wrong Node version or missing variables | Read the build log, use Node 22 and the variable table |
| Resources remain after deleting stacks | Retain policy on the table, buckets and repository | Follow the clean-up page |
| SES sandbox | Being able to send does not mean sending to every address | Verify the sender, try the mailbox simulator, save the request first and state the sandbox limit |
| Documentation commits still trigger a build | The pipeline follows the branch and may not honor skip markers | Stop jobs that change only documentation; keep the report repository separate from the app |

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
