---
title : "Workshop"
date : "2026-10-03"
weight : 5
chapter : true
pre : " <b> 5. </b> "
---

# Workshop
Trong một ứng dụng thương mại điện tử, bước đặt hàng kéo theo nhiều tác vụ phía sau: ghi nhận đơn, gửi email xác nhận, thông báo cho quản trị viên. Nếu xử lý tất cả trong cùng một yêu cầu (đồng bộ), hệ thống dễ chậm khi lượng đơn tăng, và một dịch vụ phụ bị lỗi có thể làm mất đơn hoặc gửi email trùng.

Workshop này xây dựng luồng xử lý đơn hàng theo hướng event-driven, chịu lỗi và quan sát được trên AWS, sau đó đo đạc để so sánh với phương án xử lý đồng bộ.

# Nội dung báo cáo
1.  [Giới thiệu kiến trúc](5.1-Introduction/)
2.  [Chuẩn bị](5.2-Prerequisite/)
3.  [Hàng đợi và DQL](5.3-sqs-dlq//)
4.  [Chống xử lý trùng](5.4-Idempotency/)
5.  [Điều phối và thông báo](5.5-Orchestration/)
6.  [Giám sát](5.6-Monitoring/)
7.  [Thực nghiệm lỗi và ](5.7-Experiment/)
8.  [Clean up](5.8-Clean-Up/)