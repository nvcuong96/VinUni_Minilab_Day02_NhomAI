# Nhật ký sử dụng AI · Lab 02

Nhật ký này ghi lại các lượt trao đổi thực tế đã dùng để xây dựng bản lab.

| Yêu cầu đã dùng | AI hỗ trợ | Điều chỉnh sau phản hồi |
|---|---|---|
| “đọc prj và hướng dẫn làm” | Đọc worksheet, starter code và autograder, rồi liệt kê bốn tài liệu cần nộp cùng phần Python. | Đối chiếu số ca thử với worksheet. Dùng ba ca theo worksheet vì autograder chỉ kiểm tra tối thiểu hai ca. |
| “lên plan cho prj này” | Đề xuất kế hoạch theo ví dụ điều phối sự cố pin của Xanh SM. | Đổi yêu cầu thành “đổi qua công ty thành viên vinfast cho tôi” để chuyển trọng tâm sang nhân viên hỗ trợ VinFast. |
| “PLEASE IMPLEMENT THIS PLAN” kèm kế hoạch VinFast | Soạn tài liệu, sơ đồ, dữ liệu giả lập, rule chọn phương án và bộ ca kiểm tra Gemini. | Tách yêu cầu tự do của khách khỏi dữ liệu đã xác minh. Bổ sung cổng sạc, khoảng cách tuyến và trạng thái trụ vào rule chọn trạm. |

## Phản ánh

Ví dụ trong tài liệu dùng quy trình và thời gian cụ thể, nhưng cuộc trao đổi chưa cung cấp log vận hành VinFast để xác minh các con số. Bản báo cáo ghi rõ các thời gian hiện tại là giả định, và dưới 3 phút là mục tiêu cần đo.

Code mẫu xem việc nhắc đến cứu hộ như dấu hiệu ca pin yếu đã đạt. Kiểm tra đó có thể bỏ sót một bản nháp vừa nhắc cứu hộ vừa chỉ đường đến trạm xa. Prototype mới đối chiếu action và mã trạm với rule, đồng thời yêu cầu lý do theo mẫu cố định cho ca pin yếu. Với các nháp còn lại, nhân viên vẫn cần đọc nội dung trước khi duyệt.

Khi chạy thật, lựa chọn Gemini 2.5 Flash trong kế hoạch trả HTTP 404. Kiểm tra lại bằng API cho thấy model này không còn khả dụng với khóa thử nghiệm. Bản cuối dùng Gemini 3.1 Flash Lite và đạt ba ca. Kết quả này được ghi riêng với kiểm thử cục bộ dùng phản hồi giả.
