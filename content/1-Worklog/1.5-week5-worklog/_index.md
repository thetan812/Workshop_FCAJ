---
title: "Week 5 Worklog"
date: 2026-09-19
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---
**Internship Period:** 12/09/2026 - 19/09/2026

### Week 5 Objectives: Storage & Database

- Practice EBS backup.
- Deploy RDS and connect it with EC2.

### Tasks Completed During the Week:

| Date | Task | Reference Documentation |
|------|-----------|--------------------|
| 31/08/2026 | Create an EBS Volume, mount it to EC2. Create a Snapshot for backup. | [https://cloudjourney.awsstudygroup.com/](https://cloudjourney.awsstudygroup.com/) |
| 01/09/2026 | Learn about Amazon RDS. Create a MySQL DB (select Free Tier). | [https://cloudjourney.awsstudygroup.com/](https://cloudjourney.awsstudygroup.com/) |
| 02/09/2026 | Try connecting from EC2 to RDS. Encountered a "Connection Refused" error because the RDS Security Group did not allow EC2. | [https://cloudjourney.awsstudygroup.com/](https://cloudjourney.awsstudygroup.com/) |
| 03/09/2026 | Fix the RDS Security Group (point Source to the EC2 SG). Connection successful. | [https://cloudjourney.awsstudygroup.com/](https://cloudjourney.awsstudygroup.com/) |

### Achievements

- Know how to back up hard drive data. Understand the security flow when allowing two services (EC2 & RDS) to communicate with each other.