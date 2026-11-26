---
title: "Demo and evidence"
date: 2026-11-26
weight: 12
chapter: false
pre: " <b> 5.12. </b> "
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
Detailed HTTP check results and the demo evidence files are kept on the group's report site: <https://report.d2rkhtb7c0k5r7.amplifyapp.com/vi/5-workshop/5.12-demo/>
