---
title: "IAM for beginners: if an access key leaks, what is the maximum damage?"
date: 2026-11-11
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

When I first started learning AWS, I usually did everything with a single account that had full permissions. That is convenient until I asked myself: if an access key on my machine or on GitHub leaks, what can whoever finds it do? That question led me to AWS Identity and Access Management (IAM).

The first point is telling the types of identities apart. The root user has the highest level of access in the account, and AWS recommends using it only for a few tasks that require it, with MFA enabled. Day-to-day work should use another identity that is granted just enough permissions.

The second point is the type of credentials. Long-term access keys are convenient but risky, because they never expire on their own and can linger in source code, configuration files, or commit history. For people, AWS recommends signing in through IAM Identity Center. For applications running on AWS (EC2, Lambda), use an IAM role to get temporary credentials. The trade-off is that the initial setup is slightly more complex, but in return there is no fixed key to leak.

The third point is the principle of least privilege. Instead of attaching a policy that allows every action on every resource, we allow only the exact actions on the exact resources that are needed. The figure below illustrates this: an IAM user and an IAM role share one policy that allows s3:GetObject but denies s3:DeleteObject on an S3 bucket. According to AWS policy evaluation logic, an explicit deny always wins over an allow, so it can be used to block sensitive actions.

<!-- TODO: add the iam-least-privilege diagram (IAM user and IAM role with a policy that allows s3:GetObject and denies s3:DeleteObject on an S3 bucket) -->

I will verify this with a small lab rather than rely on conclusions from theory:

1. Create two IAM identities, one with broad permissions and one with narrow permissions.
2. Run the same set of AWS CLI commands with both: list buckets, read an object, delete an object, create an EC2 instance.
3. Record which commands succeed and which are denied (AccessDenied).
4. Use IAM Access Analyzer to see which permissions or access keys were granted but never used. Note: analyzing unused access requires creating a separate unused access analyzer, and it is charged by the number of IAM users and roles analyzed each month, so I check the pricing page first and delete the analyzer when I finish.

The result of step 3 shows the real "blast radius" if each identity's key were leaked.

For operations, I will enable MFA, rotate or delete unused access keys, keep keys out of source code, and regularly review overly broad policies. When the lab ends, temporary users, roles, and keys must be deleted.

My takeaway: least privilege does not prevent a key from leaking, but it limits the consequences when an incident happens. The actual limit depends on the specific policy, so it needs to be tested as above before drawing a conclusion.

References:

1. Security best practices in IAM: <https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html>
2. IAM roles: <https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html>
3. Policy evaluation logic (explicit deny): <https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_evaluation-logic_policy-eval-denyallow.html>
4. IAM Access Analyzer: <https://docs.aws.amazon.com/IAM/latest/UserGuide/what-is-access-analyzer.html>

#AWS #AWSStudyGroup #FCAJ #FirstCloudAIJourney #IAM #CloudSecurity #LeastPrivilege
