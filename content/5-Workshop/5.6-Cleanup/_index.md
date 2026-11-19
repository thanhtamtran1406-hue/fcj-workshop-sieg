---
title: "Clean-up"
date: 2026-11-17
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

Run this only in your own lab account and only after you decide to stop the service. Deleting a stack does not remove everything: the DynamoDB table, the media and artifacts buckets and the CodeCommit repository use the Retain policy.

1. List the physical resources of each stack (`aurel-frontend`, `aurel-backend`, `aurel-artifacts`, `aurel-observability`) and note who owns them. Back up data you need and check that the copy can be read.
2. Stop new builds and remove the separate report branch stack if you created one; do not delete a shared Amplify app.
3. Delete the stacks in this order, waiting for `DELETE_COMPLETE` each time: frontend, backend, observability, then artifacts.
```sh
aws cloudformation delete-stack --stack-name aurel-frontend
aws cloudformation wait stack-delete-complete --stack-name aurel-frontend
```
4. Handle the Retain resources by hand: for versioned buckets remove all versions, delete markers and multipart uploads before deleting the bucket (a plain recursive remove is not enough), then delete the table and the repository from your inventory.
5. Check Amplify logs, any SNS subscription or Budget you added, and other Regions. Review Billing after the data updates.

Do not use wildcards to delete Retain resources and do not touch resources of other projects.
