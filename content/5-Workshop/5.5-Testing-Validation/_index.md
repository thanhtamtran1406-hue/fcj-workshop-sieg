---
title: "Testing & Validation"
date: 2026-11-17
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

### Test results
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

### Logs
- API Gateway access log with `requestId`, `requestTime`, `routeKey`, `status`, `responseLength` and `integrationLatency`; no cookies, authorization headers or request bodies. Retention is 14 days.
- Lambda log group `/aws/lambda/aurel-backend-api`, plus Amplify build and SSR logs.
```sh
aws logs tail /aws/lambda/aurel-backend-api --since 10m
```

### Metrics and alarms
A separate `aurel-observability` stack creates a dashboard and four CloudWatch alarms:

| Alarm | Metric | Condition |
| --- | --- | --- |
| LambdaErrors | Lambda `Errors`, Sum | at least 1 in 5 minutes |
| LambdaThrottles | Lambda `Throttles`, Sum | at least 1 in 5 minutes |
| Api5xx | API Gateway `5xx`, Sum | at least 1 in 5 minutes |
| ApiLatency | API Gateway `Latency`, Average | 2000 ms or more in 2 consecutive 5-minute windows |

### Alerts
Missing data is treated as not breaching. Alarm actions are still empty and there is no SNS subscription, so the alarms change state in CloudWatch but do not send email yet. A `SetAlarmState` test moved `Api5xx` to ALARM and back to OK; this is a simulation, not a real incident.

### When an alarm fires
| Alarm | First thing to check |
| --- | --- |
| LambdaErrors | Open the Lambda log group, find the failing call by its request ID, and check whether a deployment just happened |
| LambdaThrottles | Check concurrent executions and the account quota, and look for a burst of requests in the access log |
| Api5xx | Filter the access log for 5xx rows (`routeKey`, `status`), then follow the `requestId` into the Lambda log |
| ApiLatency | Compare `integrationLatency` with the total latency in the access log, then check Lambda duration and DynamoDB calls |
