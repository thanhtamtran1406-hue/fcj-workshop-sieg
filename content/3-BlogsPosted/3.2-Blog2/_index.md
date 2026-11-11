---
title: "Learning AWS without burning your wallet: easy-to-forget charges when doing labs"
date: 2026-11-11
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---

When I started doing labs, what worried me most was not a wrong configuration but the bill at the end of the month. My question was: which services can cost money unexpectedly, and how do I spot it early?

The charges that are easy to forget usually come from resources that are "idle but still billed". An EC2 instance left running is the most obvious example. A NAT Gateway is billed for every hour it exists and for every GB of data that passes through it, partial hours are rounded up to a full hour, and to stop being charged you must delete the NAT Gateway entirely. An EBS volume is still charged for storage while its instance is stopped, and a volume detached from an instance is still charged until it is deleted. A public IPv4 address can also incur hourly charges, even when it is not in use. Exact prices vary by Region and over time, so I always check the official AWS pricing page before drawing conclusions.

To see where the money goes, I use AWS Cost Explorer and group costs by tag. To do that, tags must be applied when the resources are created, for example project=lab, and then activated as cost allocation tags in Billing and Cost Management. According to the AWS documentation, a tag can take up to 24 hours to appear and up to another 24 hours to activate. Cost data for the current period can also be updated with a delay of more than 24 hours, so Cost Explorer is not a real-time alerting tool.

To be warned in advance, I use AWS Budgets. I set a small monthly budget and configure email alerts for when actual or forecasted cost reaches a certain threshold. The trade-off is that, as AWS notes, costs or usage can exceed the threshold before Budgets manages to notify, and Budgets only alerts and does not stop resources by itself.

I will verify this with a small experiment:

1. Build a lab with a few resources and apply consistent tags.
2. Record the daily cost in Cost Explorer.
3. Compare two scenarios: cleaning up right after the lab versus leaving everything running for a few days.
4. Note the difference and the time from when a charge occurs to when the alert arrives.

For operations, I keep a cleanup checklist after every session: stop or delete EC2, delete NAT Gateways, delete unused volumes and snapshots, release Elastic IPs. I also check other Regions, because resources created by mistake in an unfamiliar Region are very easy to forget.

My takeaway: monitoring tools help detect problems early, but the habit of cleaning up is what keeps the bill low. The actual savings can only be concluded once there is data from my own account.

<img src="/fcj-workshop-sieg/images/3-BlogsPosted/3.2-Blog2/cost-optimization-learning-aws.png" alt="Diagram of easy-to-forget charges when doing AWS labs" style="max-width:100%; width:700px;">

References:

1. Analyzing your costs and usage with AWS Cost Explorer: <https://docs.aws.amazon.com/cost-management/latest/userguide/ce-what-is.html>
2. Exploring your data using Cost Explorer (data latency): <https://docs.aws.amazon.com/cost-management/latest/userguide/ce-exploring-data.html>
3. Managing your costs with AWS Budgets: <https://docs.aws.amazon.com/cost-management/latest/userguide/budgets-managing-costs.html>
4. Activating user-defined cost allocation tags: <https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/activating-tags.html>
5. Amazon VPC pricing (NAT Gateway, public IPv4): <https://aws.amazon.com/vpc/pricing/>
6. Why am I charged for Amazon EBS when all my instances are stopped?: <https://www.repost.aws/knowledge-center/ebs-charge-stopped-instance>
