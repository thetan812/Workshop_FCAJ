---
title: "Worklog Tuần 5"
date: 2026-09-19
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---
**Thời gian thực tập:** 12/9/2026 - 19-9/2026
### Mục tiêu tuần 5: Storage & Database 

- Thực hành sao lưu EBS.
- Triển khai RDS và kết nối với EC2.

### Các công việc đã thực hiện trong tuần:

| Ngày | Công việc | Tài liệu tham khảo |
|------|-----------|--------------------|
| 31/08/2026 | Tạo EBS Volume, mount vào EC2. Tạo Snapshot để backup. | [https://cloudjourney.awsstudygroup.com/](https://cloudjourney.awsstudygroup.com/) |
| 01/09/2026 | Tìm hiểu Amazon RDS. Tạo 1 DB MySQL (chọn Free Tier). | [https://cloudjourney.awsstudygroup.com/](https://cloudjourney.awsstudygroup.com/) |
| 02/09/2026 | Thử kết nối từ EC2 vào RDS. Bị lỗi "Connection Refused" do Security Group của RDS chưa cho phép EC2. | [https://cloudjourney.awsstudygroup.com/](https://cloudjourney.awsstudygroup.com/) |
| 03/09/2026 | Sửa Security Group của RDS (Source trỏ về SG của EC2). Kết nối thành công. | [https://cloudjourney.awsstudygroup.com/](https://cloudjourney.awsstudygroup.com/) |


### Kết quả đạt được 
- Biết cách backup dữ liệu ổ cứng. Hiểu được luồng bảo mật khi cho phép 2 dịch vụ (EC2 & RDS) nói chuyện với nhau.

