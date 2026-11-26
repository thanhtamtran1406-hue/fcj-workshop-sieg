---
title: "Testing and validation"
date: 2026-11-26
weight: 8
chapter: false
pre: " <b> 5.8. </b> "
---

- **Local:** 30 automated tests, TypeScript check and builds pass; the smoke suite on a local build passes 41 HTTP checks (reservation, resubmission, admin authorization, image upload). The DynamoDB transaction is tested with a mock.
- **Backend on AWS:** 14 checks pass, covering Cognito access token, rejection of an ID token, saving and reading reservations, status change, idempotency, real S3 PUT/GET and contact messages.
- **Release checks:** the public and admin APIs both return 100 dishes, the 7 pages return HTTP 200, and images match byte for byte. These are HTTP checks, not browser automation.

Negative requests that anyone can repeat (expected results in brackets):
```sh
curl -i "$API_URL/admin/reservations"                              # 401
curl -i -X POST "$API_URL/reservations" -H 'Content-Type: application/json' --data '{}'         # 400
curl -i -X POST "$API_URL/reservations" -H 'Content-Type: application/json' --data '{bad-json'  # 400
```
Not done yet: a real concurrency test on DynamoDB, a load test (target p95 under 2 s at 5 requests/s for 5 minutes) and a point-in-time-recovery drill. They are planned, not claimed as passed.
