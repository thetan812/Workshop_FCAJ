---
title: "BlogPost"
date: 2026-11-14
weight: 1
chapter: false
pre: " <b> 3.1 </b> "
---
# XÂY DỰNG HỆ THỐNG XỬ LÝ ĐƠN HÀNG ỔN ĐỊNH TRÊN AWS VỚI SQS, LAMBDA VÀ CLOUDWATCH

Khi mua hàng trên một trang thương mại điện tử, chúng ta thường mong muốn đơn hàng được xử lý nhanh chóng và chính xác. Tuy nhiên, phía sau một thao tác đặt hàng đơn giản có thể là rất nhiều thành phần hoạt động cùng lúc.

Vậy nếu hệ thống gặp lỗi, chẳng hạn như dịch vụ xử lý đơn hàng bị gián đoạn hoặc có quá nhiều yêu cầu gửi đến cùng lúc, thì điều gì sẽ xảy ra?

Trong quá trình tìm hiểu AWS, mình nhận thấy Amazon SQS, AWS Lambda và Amazon CloudWatch là những dịch vụ có thể kết hợp để xây dựng một hệ thống xử lý đơn hàng linh hoạt và có khả năng chịu lỗi tốt hơn.

# I. TẠI SAO HỆ THỐNG CẦN CÓ HÀNG ĐỢI?
Hãy tưởng tượng một website bán hàng nhận được hàng nghìn đơn hàng trong thời gian ngắn. Nếu mỗi yêu cầu đều phải chờ hệ thống xử lý hoàn tất ngay lập tức, máy chủ có thể bị quá tải.

Để giải quyết vấn đề này, chúng ta có thể sử dụng Amazon Simple Queue Service (SQS).
SQS là dịch vụ hàng đợi thông điệp của AWS, cho phép lưu trữ các thông điệp để những thành phần khác xử lý vào thời điểm phù hợp.

Một số lợi ích của SQS:
- Giúp tách biệt thành phần gửi yêu cầu và thành phần xử lý.
- Hạn chế việc các dịch vụ phụ thuộc trực tiếp vào nhau.
- Có thể lưu giữ thông điệp khi hệ thống xử lý tạm thời gặp sự cố.
- Hỗ trợ xử lý các yêu cầu theo cơ chế bất đồng bộ.
Ví dụ: Khi khách hàng đặt hàng, thông tin đơn hàng được gửi vào SQS. Thay vì phải xử lý ngay toàn bộ công việc, hệ thống có thể tiếp nhận đơn hàng và đưa vào hàng đợi để xử lý sau.
# II. KẾT HỢP SQS VỚI AWS LAMBDA
Nếu SQS có nhiệm vụ lưu trữ thông điệp thì AWS Lambda đảm nhiệm việc thực thi mã xử lý.
Lambda là dịch vụ điện toán không máy chủ (Serverless) của AWS. Người phát triển chỉ cần viết mã xử lý mà không phải tự quản lý máy chủ.

Khi kết hợp hai dịch vụ, luồng hoạt động có thể diễn ra như sau:
- Khách hàng gửi yêu cầu đặt hàng.
- Hệ thống đưa thông tin đơn hàng vào SQS.
- AWS Lambda nhận thông điệp từ hàng đợi.
- Lambda thực hiện các công việc như kiểm tra thông tin và lưu đơn hàng vào cơ sở dữ liệu.
- Sau khi xử lý thành công, thông điệp được xóa khỏi hàng đợi.
Cách kết hợp này giúp hệ thống xử lý công việc linh hoạt hơn, đặc biệt khi lượng yêu cầu tăng đột biến.
# III. RETRY VÀ DEAD LETTER QUEUE (DLQ)
Một vấn đề quan trọng trong hệ thống phân tán là lỗi có thể xảy ra bất cứ lúc nào.

Ví dụ, Lambda không thể kết nối đến cơ sở dữ liệu hoặc gặp lỗi trong quá trình xử lý đơn hàng. Khi đó, thông điệp chưa được xử lý thành công sẽ không bị xóa khỏi SQS và có thể được nhận lại sau khi hết thời gian Visibility Timeout.
Đây chính là cơ chế Retry (thử xử lý lại).

Tuy nhiên, nếu một thông điệp liên tục gặp lỗi, hệ thống không nên thử lại vô hạn. AWS cung cấp cơ chế Dead Letter Queue (DLQ) để giải quyết vấn đề này.

DLQ là một hàng đợi riêng dùng để lưu những thông điệp đã thất bại nhiều lần.
Ví dụ:
- Đơn hàng được đưa vào SQS.
- Lambda nhận và xử lý nhưng gặp lỗi.
- Thông điệp được thử xử lý lại.
- Sau khi vượt quá số lần nhận tối đa được cấu hình, thông điệp được chuyển sang DLQ.
Một số lợi ích của DLQ:
- Tránh thông điệp lỗi liên tục làm ảnh hưởng đến quá trình xử lý chung.
- Giúp xác định những yêu cầu không thể xử lý thành công.
- Cho phép kiểm tra nguyên nhân và xử lý lại thông điệp khi đã khắc phục lỗi.
![AWS](/images/blogpicture/blog1.jpg)
# IV. IDEMPOTENCY – TRÁNH XỬ LÝ TRÙNG ĐƠN HÀNG
Một điều mình thấy khá thú vị khi tìm hiểu hệ thống sử dụng SQS và Lambda là: một thông điệp có thể được xử lý nhiều lần.

Điều này có thể xảy ra khi Lambda đã thực hiện xong một phần công việc nhưng gặp lỗi trước khi thông điệp được xác nhận thành công.

Nếu không có biện pháp kiểm soát, khách hàng có thể bị tạo nhiều đơn hàng giống nhau hoặc bị ghi nhận thanh toán trùng lặp.

Để hạn chế vấn đề này, chúng ta sử dụng khái niệm Idempotency (tính lũy đẳng).
Hiểu đơn giản, dù một yêu cầu được gửi hoặc xử lý nhiều lần thì kết quả nghiệp vụ vẫn không bị thực hiện trùng.
Ví dụ:
- Mỗi đơn hàng có một mã định danh riêng như ORDER_001.
- Trước khi tạo đơn hàng, hệ thống kiểm tra mã này.
- Nếu đơn hàng đã được ghi nhận, hệ thống không thực hiện lại thao tác tạo đơn.
- Nếu chưa tồn tại, hệ thống mới tiến hành xử lý.
- Có thể sử dụng DynamoDB Conditional Write để kiểm soát việc tạo bản ghi theo điều kiện, hạn chế tình trạng hai yêu cầu đồng thời cùng tạo một đơn hàng.
# V. GIÁM SÁT HỆ THỐNG VỚI AMAZON CLOUDWATCH
Xây dựng hệ thống là chưa đủ. Chúng ta còn cần biết hệ thống đang hoạt động như thế nào và có gặp lỗi hay không.

Đây là lúc Amazon CloudWatch phát huy vai trò.
CloudWatch là dịch vụ giám sát của AWS, hỗ trợ thu thập và theo dõi dữ liệu hoạt động của các tài nguyên.

Một số thành phần quan trọng:
1. CloudWatch Logs
Lưu trữ nhật ký hoạt động của Lambda, giúp kiểm tra quá trình xử lý và tìm nguyên nhân khi xảy ra lỗi.
2. CloudWatch Metrics
Theo dõi các chỉ số như:
- Lambda Errors: Số lần hàm Lambda gặp lỗi.
- Lambda Duration: Thời gian thực thi của Lambda.
- ApproximateNumberOfMessagesVisible: Số thông điệp đang chờ trong SQS.
- ApproximateAgeOfOldestMessage: Tuổi của thông điệp cũ nhất trong hàng đợi.
3. CloudWatch Alarms
Cho phép thiết lập cảnh báo khi một chỉ số vượt ngưỡng mong muốn.
Ví dụ, nếu số lượng thông điệp trong DLQ lớn hơn 0, hệ thống có thể phát cảnh báo để người quản trị kiểm tra.
Nhờ đó, chúng ta có thể phát hiện sự cố sớm thay vì chỉ biết hệ thống gặp vấn đề khi khách hàng phản ánh.
# VI. TỔNG KẾT
Qua việc tìm hiểu các dịch vụ trên, mình nhận thấy mỗi thành phần đều đảm nhiệm một vai trò riêng trong hệ thống xử lý đơn hàng:
- Amazon SQS: Lưu trữ và điều phối thông điệp.
- AWS Lambda: Thực hiện xử lý đơn hàng.
- Retry: Hỗ trợ thử xử lý lại khi xảy ra lỗi.
- Dead Letter Queue: Lưu những thông điệp thất bại nhiều lần.
- Idempotency: Hạn chế việc xử lý trùng lặp.
- Amazon CloudWatch: Theo dõi và cảnh báo tình trạng hệ thống.

Điều mình rút ra là một hệ thống tốt không chỉ cần xử lý yêu cầu nhanh mà còn phải có khả năng kiểm soát lỗi, tránh dữ liệu trùng lặp và hỗ trợ giám sát trong quá trình vận hành.

Đây cũng là một trong những lý do khiến các dịch vụ Serverless và kiến trúc Event-Driven được sử dụng rộng rãi khi xây dựng ứng dụng trên AWS.

# NGUỒN THAM KHẢO
- Amazon SQS https://docs.aws.amazon.com/.../SQSDeveloper.../welcome.html
- AWS Lambda with Amazon SQS https://docs.aws.amazon.com/lambda/latest/dg/with-sqs.html
- Amazon SQS Dead Letter Queues https://docs.aws.amazon.com/.../sqs-dead-letter-queues.html
- Amazon CloudWatch https://docs.aws.amazon.com/.../mon.../WhatIsCloudWatch.html

#AWS #AmazonSQS #AWSLambda #AmazonCloudWatch #Serverless #EventDrivenArchitecture #AWSStudyGroup

link bài viết: https://www.facebook.com/groups/660548818043427/user/100074583099174