---
title: "4.2 buổi setup nhỏ – 17 tháng 10, 2026"
date: 2026-11-14
weight: 2
chapter: false

---
(buổi ngày hôm nay tôi đi nhưng không chụp hình để minh chứng)

# Mini setup FCAJ 
Buổi  này cực kỳ đặc biệt khi mang đến 3 session cực kỳ thực tế được trình bày bởi:

-Nguyễn Thành Trung – Software Engineer | FPT Software
topic: Bảo mật khi Chatbot gọi LLM API

Khi tích hợp LLM vào Chatbot, bảo mật không chỉ dừng ở việc giấu API Key. Anh Trung đã phân tích rất sâu về các rủi ro (như Prompt Injection, rò rỉ dữ liệu) và cách kiểm soát luồng dữ liệu (Data Flow). Bài học lớn nhất là phải thiết kế một kiến trúc AI an toàn ngay từ đầu, có các lớp kiểm duyệt (guardrails) thay vì chỉ tập trung vào tính năng.

-Phạm Minh Quang – Cloud Engineer | NTQ Solution
Topic: Migration Journey: Từ nhiều Account rời rạc đến Landing Zone

Đây là chủ đề rất "đau đầu" với các doanh nghiệp khi hệ thống AWS phình to. Anh Quang đã chia sẻ lộ trình chuẩn hóa việc quản lý đa tài khoản (Multi-Account) thông qua Landing Zone. Việc thiết lập phân quyền, kiểm soát chi phí và bảo mật tập trung giúp việc mở rộng hệ thống trở nên nhàn hơn rất nhiều.

-Vũ Thế Huy – Data Engineer | Rikkiesoft
Topic: Beyond Parquet: Apache Iceberg on AWS

Parquet đã quá quen thuộc trong Data Lake, nhưng khi dữ liệu cần cập nhật liên tục (upsert/delete) thì Parquet bộc lộ điểm yếu. Anh Huy đã chỉ ra cách Apache Iceberg giải quyết bài toán này: hỗ trợ Schema Evolution, Time Travel và quản lý phiên bản dữ liệu cực tốt. Đây là mảnh ghép hoàn hảo để xây dựng Data Lake hiện đại trên AWS.

3 session, 3 mảng màu khác nhau nhưng đều hướng đến một mục tiêu chung: Xây dựng những hệ thống Cloud & AI an toàn, có khả năng mở rộng và sẵn sàng cho thực tế. Không gian văn phòng AWS rất xịn, các diễn giả chia sẻ nhiệt tình và phần trả lời câu hỏi trên Quizizz diễn ra cực kỳ sôi nổi.