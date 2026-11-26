---
title: "Logs, metrics, alarms and runbook"
date: 2026-11-26
weight: 9
chapter: false
pre: " <b> 5.9. </b> "
---

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
