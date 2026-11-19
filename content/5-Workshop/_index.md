---
title : "Workshop"
date : "2026-10-03"
weight : 5
chapter : true
pre : " <b> 5. </b> "
---

# Workshop
In an e-commerce application, placing an order triggers several downstream tasks: recording the order, sending a confirmation email, and notifying administrators. If all of these are handled within the same request (synchronously), the system slows down easily as order volume grows, and a failure in a secondary service can cause lost orders or duplicate emails.

This workshop builds an order-processing flow that is **event-driven, fault-tolerant, and observable** on AWS, then runs measurements to compare it against a synchronous approach.

# Report Content

1.  [Architecture Introduction](5.1-Introduction/)
2.  [Prerequisites](5.2-Prerequisite/)
3.  [Queue and DLQ](5.3-sqs-dlq/)
4.  [Idempotency](5.4-Idempotency/)
5.  [Orchestration and Notification](5.5-Orchestration/)
6.  [Monitoring](5.6-Monitoring/)
7.  [Failure Experimentation and Recovery](5.7-Experiment/)
8.  [Clean up](5.8-Clean-Up/)