---
title: "BlogPost"
date: 2026-11-14
weight: 2
chapter: false
pre: " <b> 3.2 </b> "
---

# AMAZON DYNAMODB HAY AMAZON RDS: ĐÂU LÀ LỰA CHỌN TỐT HƠN CHO HỆ THỐNG ĐƠN HÀNG?

Khi xây dựng một hệ thống thương mại điện tử, việc lựa chọn cơ sở dữ liệu là một trong những quyết định quan trọng. Hai dịch vụ phổ biến trên AWS thường được nhắc đến là Amazon DynamoDB và Amazon RDS.
Cả hai đều có thể được sử dụng để lưu trữ thông tin đơn hàng, khách hàng và sản phẩm. Tuy nhiên, cách hoạt động và mục đích sử dụng của chúng lại khá khác nhau.
Vậy nếu phải lựa chọn một trong hai để xây dựng hệ thống xử lý đơn hàng, đâu mới là phương án phù hợp?
# I. AMAZON RDS – CƠ SỞ DỮ LIỆU QUAN HỆ

Amazon RDS (Relational Database Service) là dịch vụ cơ sở dữ liệu quan hệ được AWS quản lý.
RDS hỗ trợ nhiều hệ quản trị cơ sở dữ liệu như MySQL, PostgreSQL, MariaDB, Oracle và SQL Server.
Một số đặc điểm nổi bật:
- Lưu trữ dữ liệu theo bảng, hàng và cột.
- Hỗ trợ SQL để truy vấn dữ liệu.
- Có thể thiết lập quan hệ giữa nhiều bảng.
- Hỗ trợ giao dịch (Transaction), phù hợp với các nghiệp vụ cần đảm bảo tính nhất quán dữ liệu.
* Ví dụ: Trong hệ thống bán hàng, chúng ta có thể tạo các bảng Customers, Orders và Products. Các bảng liên kết với nhau thông qua khóa chính và khóa ngoại.
RDS phù hợp khi ứng dụng cần truy vấn dữ liệu phức tạp hoặc quản lý nhiều mối quan hệ giữa các bảng.
![AWS RDS](/images/blogpicture/blog2a.jpg)
# II. AMAZON DYNAMODB – CƠ SỞ DỮ LIỆU NoSQL

Khác với RDS, Amazon DynamoDB là dịch vụ cơ sở dữ liệu NoSQL được AWS quản lý hoàn toàn.
DynamoDB lưu trữ dữ liệu dưới dạng Key-Value và Document, không yêu cầu cấu trúc bảng cố định như cơ sở dữ liệu quan hệ.
Một số đặc điểm nổi bật:
- Có khả năng mở rộng tự động theo nhu cầu sử dụng.
- Hỗ trợ truy cập dữ liệu với độ trễ thấp.
- Không cần quản lý máy chủ cơ sở dữ liệu.
- Phù hợp với ứng dụng có lượng yêu cầu lớn và mô hình truy cập dữ liệu rõ ràng.
* Ví dụ: Với hệ thống đơn hàng, mỗi đơn hàng có thể được lưu bằng một OrderID riêng. Khi cần tìm thông tin, hệ thống có thể truy xuất trực tiếp thông qua khóa này.
DynamoDB thường phù hợp với các ứng dụng cần tốc độ truy cập nhanh và khả năng mở rộng linh hoạt.
![AWS DynamoBD](/images/blogpicture/blog2b.jpg)
# III. VẬY HỆ THỐNG ĐƠN HÀNG NÊN DÙNG LOẠI NÀO?
Theo mình, lựa chọn phụ thuộc vào yêu cầu cụ thể của hệ thống.
* Nên chọn RDS khi:
- Có nhiều bảng dữ liệu liên kết với nhau.
- Cần truy vấn và thống kê phức tạp.
- Nghiệp vụ cần các giao dịch SQL và quan hệ dữ liệu rõ ràng.
* Nên chọn DynamoDB khi:
    - Cần truy cập dữ liệu nhanh theo khóa.
    - Lượng yêu cầu có thể tăng đột biến.
    - Ứng dụng có mô hình truy cập dữ liệu được xác định trước.
Ngoài ra, chúng ta cũng có thể kết hợp cả hai dịch vụ nếu hệ thống có những loại dữ liệu và yêu cầu xử lý khác nhau.
# IV. Kết luận

Qua việc tìm hiểu hai dịch vụ, mình nhận thấy không có cơ sở dữ liệu nào tốt nhất cho mọi trường hợp.
RDS nổi bật với dữ liệu quan hệ và khả năng truy vấn SQL.
DynamoDB nổi bật với khả năng mở rộng và truy cập dữ liệu theo khóa.
Việc lựa chọn cần dựa trên yêu cầu nghiệp vụ, cách truy vấn và chi phí vận hành.

 # Nguồn tham khảo:
- https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Welcome.html
- https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Introduction.html
- https://www.linkedin.com/posts/thedevikofficial_aws-day12-dynamodb-activity-7395468191302479872-KZkJ
- https://www.linkedin.com/posts/thedeviksharma_aws-day12-dynamodb-share-7395279832416358400-s4Rl/
#AWS #AmazonRDS #AmazonDynamoDB #Database #NoSQL #AWSStudyGroup

Link bài viết: https://www.facebook.com/groups/660548818043427/user/100074583099174