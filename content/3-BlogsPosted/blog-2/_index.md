---
title: "BlogPost"
date: 2026-11-14
weight: 2
chapter: false
pre: " <b> 3.2 </b> "
---

# AMAZON DYNAMODB OR AMAZON RDS: WHICH IS THE BETTER CHOICE FOR AN ORDER SYSTEM?

When building an e-commerce system, choosing a database is one of the important decisions. Two popular services on AWS that are often mentioned are Amazon DynamoDB and Amazon RDS.
Both can be used to store order, customer, and product information. However, how they operate and their intended use are quite different.
So, if we have to choose one of the two to build an order processing system, which one is the suitable option?
# I. AMAZON RDS – RELATIONAL DATABASE

Amazon RDS (Relational Database Service) is a managed relational database service by AWS.
RDS supports many database management systems such as MySQL, PostgreSQL, MariaDB, Oracle, and SQL Server.
Some outstanding features:
- Stores data in tables, rows, and columns.
- Supports SQL for querying data.
- Can establish relationships between multiple tables.
- Supports transactions, suitable for business logic that needs to ensure data consistency.
* Example: In a sales system, we can create Customers, Orders, and Products tables. The tables are linked together through primary keys and foreign keys.
RDS is suitable when the application needs complex data queries or manages many relationships between tables.
![AWS RDS](/images/blogpicture/blog2a.jpg)
# II. AMAZON DYNAMODB – NoSQL DATABASE

Unlike RDS, Amazon DynamoDB is a fully managed NoSQL database service by AWS.
DynamoDB stores data as Key-Value and Document, and does not require a fixed table structure like relational databases.
Some outstanding features:
- Has the ability to scale automatically based on usage demand.
- Supports low-latency data access.
- No need to manage database servers.
- Suitable for applications with high request volumes and clear data access patterns.
* Example: With an order system, each order can be stored with a unique OrderID. When information is needed, the system can retrieve it directly through this key.
DynamoDB is often suitable for applications that need fast access speeds and flexible scalability.

![AWS DynamoBD](/images/blogpicture/blog2b.jpg)
# III. SO, WHICH TYPE SHOULD AN ORDER SYSTEM USE?
In my opinion, the choice depends on the specific requirements of the system.
* Should choose RDS when:
- There are many interconnected data tables.
- Complex queries and statistics are needed.
- Business logic requires SQL transactions and clear data relationships.
* Should choose DynamoDB when:
    - Fast data access by key is needed.
    - Request volumes may spike suddenly.
    - The application has a predetermined data access pattern.
In addition, we can also combine both services if the system has different types of data and processing requirements.
# IV. Conclusion

Through researching the two services, I realize that there is no best database for all cases.
RDS stands out with relational data and SQL query capabilities.
DynamoDB stands out with scalability and key-based data access.
The choice needs to be based on business requirements, query methods, and operational costs.


 # References:
- https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Welcome.html
- https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Introduction.html
- https://www.linkedin.com/posts/thedevikofficial_aws-day12-dynamodb-activity-7395468191302479872-KZkJ
- https://www.linkedin.com/posts/thedeviksharma_aws-day12-dynamodb-share-7395279832416358400-s4Rl/
#AWS #AmazonRDS #AmazonDynamoDB #Database #NoSQL #AWSStudyGroup

Link to the blog: https://www.facebook.com/groups/660548818043427/user/100074583099174