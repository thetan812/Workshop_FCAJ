---
title : "Bản đề xuất"
date : 2026-11-11
weight : 2
chapter : false
pre : " <b> 2. </b> "
---

# Thiết kế và triển khai kiến trúc xử lý đơn hàng event-driven chịu lỗi, có giám sát trên AWS, và đánh giá thực nghiệm

### 1. Bối cảnh và vấn đề

Khi khách đặt hàng trên một cửa hàng trực tuyến, hệ thống phải làm nhiều việc: ghi nhận đơn, gửi email xác nhận, cập nhật trạng thái và báo cho người vận hành nếu có gì bất thường. Cách đơn giản nhất là dồn toàn bộ các việc này vào **một yêu cầu duy nhất** (xử lý đồng bộ). Cách này dễ viết nhưng bộc lộ bốn điểm yếu khi hệ thống chạy thật:

| Điểm yếu | Biểu hiện |
|---|---|
| Phản hồi chậm | Khách phải chờ cả chuỗi xử lý, đặc biệt khi một dịch vụ phụ (như gửi email) chậm hoặc bị giới hạn tốc độ |
| Dễ mất hoặc kẹt đơn | Một bước phụ lỗi làm cả yêu cầu thất bại, đơn có thể dừng ở trạng thái dở dang |
| Xử lý trùng | Khách bấm gửi nhiều lần hoặc hệ thống tự thử lại khiến một đơn bị xử lý và gửi email nhiều lần |
| Lỗi không được phát hiện | Không có giám sát nên người vận hành chỉ biết khi khách phản ánh |

Đề tài này đề xuất một kiến trúc **hướng sự kiện (event-driven)** trên AWS để giải quyết các điểm yếu trên, rồi **đo bằng thực nghiệm** xem kiến trúc đó tốt hơn phương án đồng bộ ở những chỉ số nào và đánh đổi những gì.

### 2. Mục tiêu và phạm vi

**Mục tiêu**

1. Thiết kế và triển khai luồng xử lý đơn hàng bất đồng bộ, chịu lỗi, không xử lý trùng và có cảnh báo.
2. Xây dựng thêm một phương án xử lý **đồng bộ** làm đối chứng, chạy trong cùng điều kiện.
3. Đo và so sánh hai phương án qua các chỉ số định lượng.
4. Biên soạn tài liệu workshop để người khác có thể làm lại từng bước.

**Phạm vi:** hệ thống chạy ở region Asia Pacific (Singapore) `ap-southeast-1`, triển khai bằng AWS SAM, mức tải thử nghiệm 20 và 50 đơn. Đề tài **không** xây dựng giao diện web cho khách hàng, không xử lý thanh toán và không nhằm đo hiệu năng ở quy mô sản xuất.

### 3. Kiến trúc đề xuất

#### 3.1. Luồng xử lý

1. Khách gửi đơn đến **API Gateway (HTTP API)**; một hàm **Lambda** kiểm tra khóa API và định dạng đơn, đưa đơn vào hàng đợi **SQS** rồi trả về `202 Accepted` ngay.
2. Một hàm Lambda khác đọc tin nhắn từ SQS, **ghi sổ DynamoDB** (`RECEIVED`) bằng điều kiện chỉ ghi nếu mã đơn chưa tồn tại, rồi khởi động một **execution của Step Functions** với tên bằng mã đơn.
3. State machine gửi email xác nhận cho khách bằng **Amazon SES**, có thử lại theo cấp số nhân.
4. Thành công: cập nhật `DONE`. Thất bại sau khi hết số lần thử: cập nhật `FAILED`, gửi cảnh báo qua **Amazon SNS** và kết thúc ở trạng thái lỗi.
5. **CloudWatch** theo dõi toàn bộ luồng và phát cảnh báo khi có bất thường.

#### 3.2. Vai trò từng thành phần

| Thành phần | Dịch vụ | Vai trò |
|---|---|---|
| Cổng vào | API Gateway, Lambda | Nhận đơn, kiểm tra hợp lệ, phản hồi nhanh |
| Hàng đợi | Amazon SQS | Tách bước nhận đơn khỏi bước xử lý, làm đệm khi lượng đơn tăng |
| Hàng đợi lỗi | SQS DLQ | Giữ lại tin nhắn không xử lý được sau số lần nhận tối đa |
| Sổ đơn | Amazon DynamoDB | Ghi trạng thái từng đơn, chống xử lý trùng |
| Điều phối | AWS Step Functions | Quản lý từng bước, thử lại và bắt lỗi rõ ràng |
| Thông báo | Amazon SES, Amazon SNS | Gửi email cho khách và cảnh báo cho quản trị viên |
| Giám sát | Amazon CloudWatch | Alarm và dashboard |
| Triển khai | AWS SAM (CloudFormation) | Mô tả toàn bộ hạ tầng bằng một template |

### 4. Thiết kế chịu lỗi

Mỗi loại sự cố có một cơ chế xử lý riêng, thay vì dựa vào một cơ chế chung:

| Sự cố | Cơ chế xử lý | Kết quả |
|---|---|---|
| Tin nhắn sai định dạng, Lambda lỗi | SQS thử nhận lại, quá 3 lần thì chuyển vào DLQ | Không mất tin nhắn, có thể kiểm tra và xử lý lại |
| Gửi cùng một đơn nhiều lần | Ghi DynamoDB có điều kiện; tên execution bằng mã đơn | Một đơn chỉ chạy một lần, không có email thứ hai |
| Lỗi tạm thời khi gửi email (ví dụ SES giới hạn tốc độ) | `Retry` của Step Functions: 6 lần, hệ số 2, có jitter | Tự phục hồi mà không cần can thiệp |
| Lỗi vĩnh viễn khi gửi email | `Catch` chuyển sang `MarkFailed` rồi `NotifyAdminFailure` | Đơn được đánh dấu `FAILED` và quản trị viên được báo ngay |

### 5. Giám sát và cảnh báo

Hệ thống có ba CloudWatch Alarm: có tin nhắn trong DLQ, có execution Step Functions thất bại, và Lambda báo lỗi. Cả ba gửi cảnh báo qua cùng một SNS topic. Ngoài ra, một dashboard hiển thị độ dài hàng đợi, tuổi tin nhắn cũ nhất, số lần gọi và lỗi của Lambda, số execution của Step Functions trên cùng một màn hình, giúp người vận hành nắm tình trạng hệ thống trong một lần nhìn.

### 6. Phương án thực nghiệm

Hai phương án được triển khai trong cùng một stack và cùng một khóa API để so sánh công bằng:

| | Đồng bộ (đối chứng) | Bất đồng bộ (đề xuất) |
|---|---|---|
| Đường dẫn | `POST /sync` | `POST /async` |
| Cách xử lý | Ghi DynamoDB rồi gửi SES ngay trong request | Đưa đơn vào SQS, xử lý nền bằng Lambda và Step Functions |
| Chống trùng, thử lại, DLQ, cảnh báo | Không | Có |

**Ba thí nghiệm**

1. **Độ trễ và tải:** gửi 20 và 50 đơn với nhiều yêu cầu đồng thời, đo phản hồi API (trung bình, p95), số đơn hoàn tất, thất bại, bị kẹt hoặc mất, và thời gian hoàn tất xử lý.
2. **Gửi trùng:** gửi cùng một mã đơn 5 lần vào mỗi đường, đếm số email dư.
3. **Lỗi và cảnh báo:** dùng email chưa xác minh để SES từ chối, đo thời gian từ lúc lỗi đến lúc có cảnh báo (trực tiếp qua SNS và qua CloudWatch Alarm), kiểm tra tin nhắn lỗi định dạng chuyển vào DLQ.

**Chỉ số đánh giá:** phản hồi API trung bình và p95; tỉ lệ đơn mất hoặc kẹt khi có lỗi; số email gửi trùng; thời gian có cảnh báo khi lỗi; thời gian hoàn tất xử lý; chi phí thực tế của toàn bộ thí nghiệm.

### 7. Ràng buộc và rủi ro

| Ràng buộc hoặc rủi ro | Cách giảm thiểu |
|---|---|
| SES sandbox giới hạn 1 email/giây và chỉ gửi tới địa chỉ đã xác minh | Dùng hộp thư giả lập `success@simulator.amazonses.com`, giới hạn tải ở 20 và 50 đơn, ghi nhận đây là nút thắt chung của cả hai phương án |
| Hai đường API công khai trên Internet | Bảo vệ bằng khóa `x-api-key`, không đưa khóa và `samconfig.toml` lên kho mã, xóa toàn bộ stack khi xong |
| Số liệu dao động do khởi động lạnh của Lambda và thời điểm chạy | Chạy khởi động nóng trước, lặp mỗi mức tải ít nhất 3 lần và lấy trung bình |
| Phát sinh chi phí ngoài dự kiến | Đặt AWS Budgets từ đầu, dọn dẹp tài nguyên ở cuối, kiểm tra Cost Explorer sau 1 đến 2 ngày |

### 8. Kết quả mong đợi

- Một hệ thống xử lý đơn hàng chạy được trên AWS, triển khai lặp lại được bằng một lệnh `sam deploy`.
- Đơn hàng không bị mất khi một bước lỗi; đơn lỗi được giữ lại và có cảnh báo.
- Một đơn chỉ được xử lý và gửi email một lần dù bị gửi nhiều lần.
- Bảng số liệu so sánh thực tế giữa hai phương án, kèm nhận xét trung thực về điểm mạnh và hạn chế, kể cả những chỉ số mà kiến trúc bất đồng bộ không có lợi thế (ví dụ thời gian hoàn tất thường dài hơn do xử lý chạy nền).
- Bộ tài liệu workshop hướng dẫn từng bước: chuẩn bị, SQS và DLQ, chống trùng, Step Functions, giám sát, thực nghiệm, dọn dẹp.

**Hướng phát triển:** bổ sung cơ chế xử lý lại đơn `FAILED` (hiện gửi lại cùng mã sẽ bị coi là trùng), và xin chuyển SES ra khỏi sandbox để thử ở mức tải lớn hơn.

### 9. Thời lượng và chi phí

Thời gian thực hiện workshop khoảng 2 đến 3 giờ. Ở quy mô thử nghiệm, phần lớn dịch vụ nằm trong free tier hoặc có chi phí rất thấp; chi phí thực tế sẽ được ghi nhận từ Cost Explorer sau khi chạy xong. Cần đặt cảnh báo chi phí (AWS Budgets) từ đầu và thực hiện bước dọn dẹp tài nguyên ở cuối để tránh phát sinh phí.