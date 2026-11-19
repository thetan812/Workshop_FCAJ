---
title: "BlogPost"
date: 2026-11-14
weight: 3
chapter: false
pre: " <b> 3.3 </b> "
---
# Không phải cứ bật Auto Scaling là hệ thống có khả năng chịu tải tốt!
Mình từng nghĩ rằng khi traffic tăng, chỉ cần bật EC2 Auto Scaling để tăng số lượng instance là có thể giải quyết vấn đề. Nhưng thực tế, câu chuyện không đơn giản như vậy.

* Tình huống thực tế:
   Giả sử một hệ thống E-commerce trên AWS có kiến trúc:
  - ALB tiếp nhận request.
  - EC2 Auto Scaling xử lý ứng dụng.
  - RDS MySQL lưu trữ dữ liệu.
  - ElastiCache hỗ trợ caching.
Khi traffic tăng gấp 10 lần, EC2 tự động scale từ 2 lên 10 instances. Tuy nhiên, RDS lại tăng CPU lên 95–100%, số lượng connections tăng cao và API bắt đầu phản hồi chậm.
Lúc này, dù EC2 đã scale out, hệ thống vẫn có thể gặp tình trạng quá tải.

![Auto-Scaling](/images/blogpicture/blog3.jpg)
# Vì sao auto scaling chưa đủ ?
Theo mình, vấn đề nằm ở chỗ Auto Scaling thường chỉ mở rộng một thành phần cụ thể, trong khi hiệu năng của hệ thống phụ thuộc vào nhiều thành phần khác nhau.
* Ví dụ:
  - EC2 tăng số lượng instance nhưng Database không xử lý kịp truy vấn.
  - Nhiều request cùng truy cập một tài nguyên khiến hệ thống bị nghẽn.
  - Cache chưa hiệu quả dẫn đến quá nhiều request đổ về Database.
  - Các tác vụ xử lý đồng bộ khiến ứng dụng phải chờ lâu.
Vì vậy, mở rộng Compute không đồng nghĩa với việc toàn bộ hệ thống được mở rộng tương ứng.
* Khi xây dựng một hệ thống có khả năng chịu tải, mình nghĩ cần quan tâm đến cả kiến trúc tổng thể:
   - Thiết kế Database phù hợp với lượng truy cập dự kiến.
   - Sử dụng Cache để giảm tải cho Database.
   - Cân nhắc SQS để xử lý các tác vụ bất đồng bộ.
   - Theo dõi CloudWatch Metrics để phát hiện bottleneck.
   - Kiểm thử tải để đánh giá khả năng đáp ứng trước khi đưa vào production.


# Kết Luận
Auto Scaling là một thành phần quan trọng trong kiến trúc AWS, nhưng không phải giải pháp cho mọi vấn đề về hiệu năng.
Theo mình, một hệ thống có khả năng chịu tải tốt không chỉ cần mở rộng tài nguyên linh hoạt mà còn phải có kiến trúc phù hợp, khả năng giám sát và xử lý bottleneck ở từng tầng.


Nguồn tham khảo:
- https://docs.aws.amazon.com/.../as-scale-based-on-demand...
- https://docs.aws.amazon.com/AmazonE.../latest/dg/WhatIs.html
- https://docs.aws.amazon.com/.../UserGuide/rds-proxy.html
- https://docs.aws.amazon.com/.../SQSDeveloper.../welcome.html
- https://docs.aws.amazon.com/.../mon.../WhatIsCloudWatch.html
- https://docs.aws.amazon.com/.../elasticache-for-memcached...
- 
#AWS #AmazonWebServices #CloudComputing #CloudArchitecture #AutoScaling #EC2 #AmazonRDS #ElastiCache #CloudWatch #PerformanceOptimization #Scalability #AWSStudyGroupVN

Link bài viết: https://www.facebook.com/groups/awsstudygroupfcj/permalink/2341577316607227/