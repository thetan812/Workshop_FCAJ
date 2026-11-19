---
title: "BlogPost"
date: 2026-11-14
weight: 1
chapter: false
pre: " <b> 3.1 </b> "
---
## BUILDING A RESILIENT ORDER PROCESSING SYSTEM ON AWS WITH SQS, LAMBDA, AND CLOUDWATCH
When shopping on an e-commerce website, we usually expect orders to be processed quickly and accurately. However, behind a simple checkout action, there can be many components operating simultaneously.

So, what happens if the system encounters an error, such as the order processing service being interrupted or receiving too many requests at once?

While exploring AWS, I found that Amazon SQS, AWS Lambda, and Amazon CloudWatch are services that can be combined to build a more flexible and fault-tolerant order processing system.

# I. WHY DOES THE SYSTEM NEED A QUEUE?
Imagine an online store receiving thousands of orders in a short period. If every request had to wait for the system to complete processing immediately, the server could become overloaded.

To solve this problem, we can use Amazon Simple Queue Service (SQS).
SQS is AWS's message queuing service, which allows messages to be stored for other components to process at an appropriate time.

Some benefits of SQS:
- Helps decouple the components sending requests from the processing components.
- Reduces direct dependencies between services.
- Can retain messages when the processing system temporarily experiences issues.
- Supports processing requests asynchronously.
For example: When a customer places an order, the order information is sent to SQS. Instead of having to process all the work immediately, the system can accept the order and put it in the queue for later processing.

# II. COMBINING SQS WITH AWS LAMBDA
If SQS is responsible for storing messages, AWS Lambda handles the execution of the processing code.
Lambda is AWS's serverless computing service. Developers only need to write the processing code without having to manage servers themselves.

When combining these two services, the workflow can proceed as follows:
- The customer submits an order request.
- The system puts the order information into SQS.
- AWS Lambda receives the message from the queue.
- Lambda performs tasks such as validating information and saving the order to the database.
- After successful processing, the message is deleted from the queue.
This combination makes the system's task processing more flexible, especially when the volume of requests spikes.

# III. RETRY AND DEAD LETTER QUEUE (DLQ)
An important issue in distributed systems is that errors can occur at any time.

For example, Lambda might fail to connect to the database or encounter an error while processing an order. In this case, the unsuccessfully processed message will not be deleted from SQS and can be received again after the Visibility Timeout expires.
This is the Retry mechanism.

However, if a message consistently fails, the system should not retry infinitely. AWS provides the Dead Letter Queue (DLQ) mechanism to address this.

A DLQ is a separate queue used to store messages that have failed multiple times.
Example:
- An order is put into SQS.
- Lambda receives and processes it but encounters an error.
- The message is retried.
- After exceeding the configured maximum receive count, the message is moved to the DLQ.
Some benefits of DLQ:
- Prevents continuous failing messages from disrupting the overall processing flow.
- Helps identify requests that cannot be processed successfully.
- Allows investigating the cause and reprocessing messages once the issue is resolved.
![AWS](/images/blogpicture/blog1.jpg)
# IV. IDEMPOTENCY – AVOIDING DUPLICATE ORDER PROCESSING
One interesting thing I found while researching systems using SQS and Lambda is that: a message can be processed multiple times.

This can happen when Lambda has completed part of the work but fails before the message is successfully acknowledged.

Without control measures, customers might end up with multiple identical orders or duplicated payment records.

To mitigate this issue, we use the concept of Idempotency.
Simply put, even if a request is sent or processed multiple times, the business outcome is not duplicated.
Example:
- Each order has a unique identifier like ORDER_001.
- Before creating the order, the system checks this identifier.
- If the order has already been recorded, the system does not repeat the order creation action.
- If it does not exist, the system proceeds with the processing.
- DynamoDB Conditional Write can be used to control record creation conditionally, preventing two simultaneous requests from creating the same order.

# V. MONITORING THE SYSTEM WITH AMAZON CLOUDWATCH
Building a system is not enough. We also need to know how the system is operating and whether it encounters errors.

This is where Amazon CloudWatch comes into play.
CloudWatch is AWS's monitoring service, supporting the collection and tracking of resource activity data.

Some important components:
1. CloudWatch Logs
Stores Lambda activity logs, helping to inspect the processing flow and find the root cause when errors occur.
2. CloudWatch Metrics
Tracks metrics such as:
- Lambda Errors: The number of times the Lambda function encounters errors.
- Lambda Duration: The execution time of Lambda.
- ApproximateNumberOfMessagesVisible: The number of messages waiting in SQS.
- ApproximateAgeOfOldestMessage: The age of the oldest message in the queue.
3. CloudWatch Alarms
Allows setting up alerts when a metric exceeds a desired threshold.
For example, if the number of messages in the DLQ is greater than 0, the system can trigger an alert for administrators to investigate.
As a result, we can detect issues early instead of only finding out the system has a problem when customers complain.

# VI. CONCLUSION
Through researching these services, I found that each component plays a distinct role in the order processing system:
- Amazon SQS: Stores and orchestrates messages.
- AWS Lambda: Performs order processing.
- Retry: Assists in re-processing when errors occur.
- Dead Letter Queue: Stores messages that have failed multiple times.
- Idempotency: Mitigates duplicate processing.
- Amazon CloudWatch: Monitors and alerts on system status.

The takeaway is that a good system doesn't just need to process requests quickly; it must also have the ability to control errors, avoid duplicate data, and support monitoring during operation.

This is also one of the reasons why Serverless services and Event-Driven architectures are widely used when building applications on AWS.

# REFERENCES
- Amazon SQS https://docs.aws.amazon.com/.../SQSDeveloper.../welcome.html
- AWS Lambda with Amazon SQS https://docs.aws.amazon.com/lambda/latest/dg/with-sqs.html
- Amazon SQS Dead Letter Queues https://docs.aws.amazon.com/.../sqs-dead-letter-queues.html
- Amazon CloudWatch https://docs.aws.amazon.com/.../mon.../WhatIsCloudWatch.html

#AWS #AmazonSQS #AWSLambda #AmazonCloudWatch #Serverless #EventDrivenArchitecture #AWSStudyGroup

Link to the blog: https://www.facebook.com/groups/660548818043427/user/100074583099174