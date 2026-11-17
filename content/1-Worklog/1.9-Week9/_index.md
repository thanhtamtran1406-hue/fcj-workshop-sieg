---
title: "Week 9 Worklog"
date: 2026-11-17
weight: 9
chapter: false
pre: " <b> 1.9. </b> "
---

### Week 9 Objectives:

* Test the workshop
* Create the report website from the FCAJ template

### Tasks to be carried out this week:
| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - Run local checks<br>&emsp;+ pnpm check, pnpm build, pnpm test:smoke<br>&emsp;+ Check that /menu returns 100 dishes with no CORS error | 02/11/2026 | 02/11/2026 | [https://github.com/ndthuy21/aurel-restaurant](https://github.com/ndthuy21/aurel-restaurant) |
| 3 | - Create the repository and report website from the FCAJ template<br>&emsp;+ Install Hugo and clone the template<br>&emsp;+ Enable GitHub Pages with the deploy workflow | 03/11/2026 | 03/11/2026 | [https://github.com/thienluhoan/fcj-workshop-template](https://github.com/thienluhoan/fcj-workshop-template)<br>[https://gohugo.io/documentation/](https://gohugo.io/documentation/)<br>[https://docs.github.com/en/pages](https://docs.github.com/en/pages) |
| 4 | - Configure the report website<br>&emsp;+ baseURL, profile picture, languages | 04/11/2026 | 04/11/2026 | [https://gohugo.io/documentation/](https://gohugo.io/documentation/) |
| 5 | - Test reservations and the admin on AWS<br>&emsp;+ Book a table, check DynamoDB and the SES email<br>&emsp;+ Admin signs in and confirms the reservation | 05/11/2026 | 05/11/2026 | [https://github.com/ndthuy21/aurel-restaurant](https://github.com/ndthuy21/aurel-restaurant) |
| 6 | - Test the S3 image upload and check logs<br>&emsp;+ PUT with a signed URL and preview the image<br>&emsp;+ Lambda logs in CloudWatch and Amplify build and SSR logs | 06/11/2026 | 06/11/2026 | [https://github.com/ndthuy21/aurel-restaurant](https://github.com/ndthuy21/aurel-restaurant) |
| 7 | - Add the 3 attended events to the report website<br>&emsp;+ Main content and check-in photos | 07/11/2026 | 07/11/2026 | [https://hcm-rules.awsfcaj.com/project/content](https://hcm-rules.awsfcaj.com/project/content) |

### Week 9 Achievements:

* Completed local and AWS testing
* The report website runs on GitHub Pages
