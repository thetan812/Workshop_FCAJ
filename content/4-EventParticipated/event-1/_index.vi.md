---
title: "4.1 Company Visit: Amazon Web Services – 14 tháng 11, 2026"
date: 2026-11-14
weight: 1
chapter: false

---

# Riot Games Journey
Vào ngày 14/11/2026 tôi đã có mặt tại tầng 7 tòa nhà Grand Teera, Cát Linh, Hà Nội để tham gia 1 session được phát biểu bởi anh Nhân-một thành viên FCAJ . Tôi đã được nghe về hành trình phát triển của Riot Games-từ những ngày đầu tiên là một studio nhỏ với vài ý tưởng táo bạo, cho đến khi trở thành một trong những công ty giải trí game hàng đầu thế giới.

![Ảnh minh chứng đi event](/images/event1/event1a.jpg)

Một trong những điểm khiến câu chuyện phát triển của Riot trở nên đặc biệt chính là cách họ xây dựng hạ tầng kỹ thuật để vận hành các tựa game quy mô toàn cầu. Riot gần như đặt toàn bộ "gánh nặng" vận hành lên Amazon Web Services (AWS) và bắt đầu quá trình chuyển dịch lên cloud từ năm 2016.

Điểm cốt lõi trong kiến trúc của Riot là sự phân tách rõ ràng giữa hai loại máy chủ: máy chủ nền tảng (platform servers) phụ trách các tính năng ngoài trận đấu (giao diện, cửa hàng, thông tin người chơi...) và máy chủ game (game servers) trực tiếp mô phỏng trận đấu. Các máy chủ game có yêu cầu khắt khe về độ trễ, thường xuyên phải xử lý hàng nghìn trận đấu đồng thời với các gói tin UDP nhỏ và tần suất cao.

![Ảnh minh chứng đi event](/images/event1/event1b.jpg)

Để quản lý khối lượng công việc khổng lồ này, Riot đã phát triển Game Provisioning Platform (GPP) – một nền tảng cấp phát game chung cho mọi tựa game, có khả năng mở rộng quy mô toàn cầu. Họ cũng xây dựng một lớp trừu tượng nội bộ có tên R-Cluster để tối ưu hóa việc phân bổ tài nguyên, giúp giảm độ trễ xuống dưới 35ms cho Valorant và dưới 70ms cho Liên Minh Huyền Thoại ở nhiều khu vực.

link(https://greenwich.edu.vn/event/hn-company-visit-amazon-web-services/)