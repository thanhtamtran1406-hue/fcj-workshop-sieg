---
title: "Cost estimate and optimization"
date: 2026-11-26
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

### Assumptions
Per month: 10,000 visits, 100,000 API requests and 100,000 SSR requests, 20 GB of delivered content, Lambda 256 MB at 200 ms, 1,000 emails and 5 admin users; Singapore Region, USD, before tax and before free tier.

### Estimates
| Item | Monthly cost (USD) |
| --- | --- |
| AWS Pricing Calculator workload estimate (20 usage lines, 8 services) | about 6.00 |
| Independent budget model, including allowances for transfer, dashboard and CodeCommit | 12.17 |
| Independent budget with a 25% reserve | 15.21 |

The independent model adds allowances (dashboard, data transfer, CodeCommit, report build) and uses conservative rates, so it is higher than the Calculator figure, which covers only the 20 entered lines. Neither is a bill. Not included: WAF, NAT, SQS, a custom domain, paid support and DynamoDB restore or export.

### Optimization measures
- Use pay-per-use services (Lambda, API Gateway, on-demand DynamoDB) so an idle site costs little.
- Run Lambda on arm64 with a small memory size and keep log retention at 14 days.
- Tag resources and set a monthly budget with an email alert before running labs.
- Clean up unused stacks and the retained resources after each lab (see the clean-up page).
