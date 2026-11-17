---
title: "Week 2 Worklog"
date: 2026-11-17
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---

### Week 2 Objectives:

* Understand identity and credential types in IAM
* Understand the principle of least privilege

### Tasks to be carried out this week:
| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - Study IAM: identity types<br>&emsp;+ The root user is used only for tasks that require it, with MFA enabled<br>&emsp;+ Day-to-day work uses an identity with just enough permissions | 14/09/2026 | 14/09/2026 | [https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html) |
| 3 | - Study credentials<br>&emsp;+ Long-term access keys: never expire and can linger in source code and commit history<br>&emsp;+ User sign-in through IAM Identity Center | 15/09/2026 | 15/09/2026 | [https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html)<br>[https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html) |
| 4 | - Study IAM roles for applications running on AWS (EC2, Lambda)<br>&emsp;+ Receive temporary credentials | 16/09/2026 | 16/09/2026 | [https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html) |
| 5 | - Study the principle of least privilege<br>&emsp;+ Allow only the exact actions on the exact resources | 17/09/2026 | 17/09/2026 | [https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_evaluation-logic_policy-eval-denyallow.html](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_evaluation-logic_policy-eval-denyallow.html) |
| 6 | - Study policy evaluation logic<br>&emsp;+ An explicit deny always wins over an allow<br>- Summarize notes | 18/09/2026 | 18/09/2026 | [https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_evaluation-logic_policy-eval-denyallow.html](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_evaluation-logic_policy-eval-denyallow.html) |

### Week 2 Achievements:

* Can tell identity types apart and understand access key risks
* Understood least privilege and explicit deny
