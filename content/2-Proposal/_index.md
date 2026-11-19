---
title : "Proposal"
date : 2026-11-11
weight : 2
chapter : false
pre : " <b> 2. </b> "
---

# Design and Implementation of a Fault-Tolerant, Monitored Event-Driven Order Processing Architecture on AWS, with Experimental Evaluation

### 1. Background and Problem

When a customer places an order in an online store, the system has to do several things: record the order, send a confirmation email, update its status, and alert the operator if something goes wrong. The simplest approach is to cram all of this into **a single request** (synchronous processing). It is easy to write, but it shows four weaknesses once the system runs for real:

| Weakness | Symptom |
|---|---|
| Slow responses | The customer waits for the entire processing chain, especially when a secondary service (such as email) is slow or rate-limited |
| Lost or stuck orders | A failing secondary step fails the whole request, and the order may stop in a half-finished state |
| Duplicate processing | The customer clicks submit several times, or the system retries on its own, so one order is processed and emailed multiple times |
| Undetected failures | Without monitoring, the operator only finds out when customers complain |

This project proposes an **event-driven** architecture on AWS to address these weaknesses, then uses **experiments** to measure where it is better than the synchronous approach and what trade-offs it brings.

### 2. Objectives and Scope

**Objectives**

1. Design and deploy an asynchronous order-processing flow that is fault tolerant, free of duplicate processing, and able to raise alerts.
2. Build a **synchronous** approach as a baseline, running under the same conditions.
3. Measure and compare the two approaches using quantitative metrics.
4. Write workshop documentation so that others can reproduce it step by step.

**Scope:** the system runs in the Asia Pacific (Singapore) `ap-southeast-1` region, is deployed with AWS SAM, and is tested at loads of 20 and 50 orders. The project does **not** build a customer-facing web interface, does not handle payments, and does not aim to measure production-scale performance.

### 3. Proposed Architecture

#### 3.1. Processing flow

1. The customer sends an order to **API Gateway (HTTP API)**; a **Lambda** function validates the API key and the order format, puts the order into an **SQS** queue, and immediately returns `202 Accepted`.
2. Another Lambda function reads the message from SQS, **records the order in DynamoDB** (`RECEIVED`) using a condition that only writes if the order ID does not yet exist, then starts a **Step Functions execution** named after the order ID.
3. The state machine sends a confirmation email to the customer through **Amazon SES**, with exponential-backoff retries.
4. On success: update the status to `DONE`. On failure after retries are exhausted: update the status to `FAILED`, send an alert through **Amazon SNS**, and end in a failed state.
5. **CloudWatch** monitors the entire flow and raises alerts when something abnormal happens.

#### 3.2. Role of each component

| Component | Service | Role |
|---|---|---|
| Entry point | API Gateway, Lambda | Receive orders, validate them, respond quickly |
| Queue | Amazon SQS | Decouple order intake from processing, buffer when order volume rises |
| Dead-letter queue | SQS DLQ | Hold messages that cannot be processed after the maximum receive count |
| Order ledger | Amazon DynamoDB | Record the status of each order, prevent duplicate processing |
| Orchestration | AWS Step Functions | Manage each step, with explicit retries and error catching |
| Notification | Amazon SES, Amazon SNS | Send email to customers and alerts to administrators |
| Monitoring | Amazon CloudWatch | Alarms and dashboard |
| Deployment | AWS SAM (CloudFormation) | Describe the whole infrastructure in a single template |

### 4. Fault-Tolerant Design

Each type of failure has its own handling mechanism, rather than relying on one generic mechanism:

| Failure | Handling mechanism | Result |
|---|---|---|
| Malformed message, Lambda error | SQS redelivers the message; after 3 receives it moves to the DLQ | No message is lost, and it can be inspected and reprocessed |
| The same order submitted several times | Conditional write to DynamoDB; execution name equals the order ID | An order runs only once, with no second email |
| Temporary email failure (for example SES rate limiting) | Step Functions `Retry`: 6 attempts, backoff rate 2, with jitter | Recovers automatically without intervention |
| Permanent email failure | `Catch` routes to `MarkFailed` and then `NotifyAdminFailure` | The order is marked `FAILED` and the administrator is notified immediately |

### 5. Monitoring and Alerting

The system has three CloudWatch Alarms: messages in the DLQ, a failed Step Functions execution, and Lambda errors. All three send alerts through the same SNS topic. In addition, a dashboard shows queue length, age of the oldest message, Lambda invocations and errors, and Step Functions executions on a single screen, so the operator can see the state of the system at a glance.

### 6. Experiment Plan

Both approaches are deployed in the same stack and use the same API key so that the comparison is fair:

| | Synchronous (baseline) | Asynchronous (proposed) |
|---|---|---|
| Endpoint | `POST /sync` | `POST /async` |
| How it processes | Writes to DynamoDB and then sends via SES within the request | Puts the order into SQS, processed in the background by Lambda and Step Functions |
| Duplicate protection, retries, DLQ, alerts | No | Yes |

**Three experiments**

1. **Latency and load:** send 20 and 50 orders with several concurrent requests, and measure API response time (average, p95), the number of completed, failed, stuck, or lost orders, and processing completion time.
2. **Duplicates:** send the same order ID 5 times to each endpoint and count the extra emails.
3. **Failures and alerts:** use an unverified email so SES rejects it, measure the time from the failure to the alert (directly through SNS and through the CloudWatch Alarm), and check that malformed messages move to the DLQ.

**Evaluation metrics:** average and p95 API response; rate of lost or stuck orders when failures occur; number of duplicate emails; time until an alert is raised on failure; processing completion time; and the actual cost of the whole experiment.

### 7. Constraints and Risks

| Constraint or risk | Mitigation |
|---|---|
| The SES sandbox limits sending to 1 email/second and only to verified addresses | Use the `success@simulator.amazonses.com` mailbox simulator, cap the load at 20 and 50 orders, and record this as a bottleneck shared by both approaches |
| Two API endpoints exposed to the Internet | Protect them with an `x-api-key` key, keep the key and `samconfig.toml` out of the repository, and delete the whole stack when finished |
| Measurements fluctuate because of Lambda cold starts and time of day | Warm up first, repeat each load level at least 3 times, and take the average |
| Unexpected costs | Set up AWS Budgets from the start, clean up resources at the end, and check Cost Explorer after 1 to 2 days |

### 8. Expected Results

- An order-processing system running on AWS, repeatably deployable with a single `sam deploy` command.
- No order is lost when a step fails; failed orders are retained and trigger an alert.
- An order is processed and emailed only once, even if it is submitted multiple times.
- A table of real measurements comparing the two approaches, with an honest discussion of strengths and limitations, including the metrics where the asynchronous architecture has no advantage (for example, completion time is usually longer because processing runs in the background).
- A step-by-step workshop guide covering preparation, SQS and DLQ, duplicate protection, Step Functions, monitoring, experiments, and cleanup.

**Future work:** add a mechanism to reprocess `FAILED` orders (currently, resubmitting the same ID is treated as a duplicate), and request that SES be moved out of the sandbox to test at larger loads.

### 9. Duration and Cost

The workshop takes about 2 to 3 hours. At test scale, most services fall within the free tier or cost very little; the actual cost will be recorded from Cost Explorer after the run. Set up a cost alert (AWS Budgets) from the start and perform the resource cleanup step at the end to avoid unexpected charges.