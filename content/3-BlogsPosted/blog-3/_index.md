---
title: "BlogPost"
date: 2026-11-14
weight: 3
chapter: false
pre: " <b> 3.3 </b> "
---
# Just because you enable Auto Scaling doesn't mean the system can handle the load well!
I used to think that when traffic increases, just enabling EC2 Auto Scaling to increase the number of instances could solve the problem. But in reality, it's not that simple.

* Actual situation:
   Suppose an E-commerce system on AWS has the following architecture:
  - ALB receives requests.
  - EC2 Auto Scaling handles the application.
  - RDS MySQL stores data.
  - ElastiCache supports caching.
When traffic increases 10 times, EC2 automatically scales from 2 to 10 instances. However, RDS CPU spikes to 95–100%, the number of connections increases highly, and the API starts responding slowly.
At this point, even though EC2 has scaled out, the system can still experience overload.
![Auto-Scaling](/images/blogpicture/blog3.jpg)
# Why is auto scaling not enough?
In my opinion, the problem lies in the fact that Auto Scaling usually only scales a specific component, while system performance depends on many different components.
* Examples:
  - EC2 increases the number of instances but the Database cannot process queries in time.
  - Many requests accessing the same resource at the same time cause the system to bottleneck.
  - Ineffective caching leads to too many requests hitting the Database.
  - Synchronous processing tasks cause the application to wait for a long time.
Therefore, scaling Compute does not mean the entire system is scaled accordingly.
* When building a system with good load capacity, I think it's necessary to care about the overall architecture:
   - Design the Database to match the expected traffic.
   - Use Cache to reduce the load on the Database.
   - Consider SQS to handle asynchronous tasks.
   - Monitor CloudWatch Metrics to detect bottlenecks.
   - Load testing to evaluate the response capacity before going to production.
# Conclusion
Auto Scaling is an important component in AWS architecture, but it is not the solution to all performance problems.
In my opinion, a system with good load capacity not only needs to scale resources flexibly but also needs an appropriate architecture, monitoring capabilities, and the ability to handle bottlenecks at each layer.

References:
- https://docs.aws.amazon.com/.../as-scale-based-on-demand...
- https://docs.aws.amazon.com/AmazonE.../latest/dg/WhatIs.html
- https://docs.aws.amazon.com/.../UserGuide/rds-proxy.html
- https://docs.aws.amazon.com/.../SQSDeveloper.../welcome.html
- https://docs.aws.amazon.com/.../mon.../WhatIsCloudWatch.html
- https://docs.aws.amazon.com/.../elasticache-for-memcached...
#AWS #AmazonWebServices #CloudComputing #CloudArchitecture #AutoScaling #EC2 #AmazonRDS #ElastiCache #CloudWatch #PerformanceOptimization #Scalability #AWSStudyGroupVN

Link to blog: https://www.facebook.com/groups/awsstudygroupfcj/permalink/2341577316607227/