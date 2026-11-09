# Quét vấn đề vận hành

Các tình huống và chỉ số dưới đây là giả thuyết để thiết kế bài thử. Nhóm cần xác minh quy trình và đo thời gian thực tế trước khi dùng chúng để đánh giá hiệu quả.

| # | Công ty | Góc nhìn | Vấn đề cần xác minh |
|---|---|---|---|
| 1 | VinFast | Vướng mắc của khách hàng | Khách cần sạc gấp, nhân viên phải đối chiếu cổng, trụ trống và khoảng cách theo tuyến trước khi hướng dẫn. |
| 2 | VinFast | Lặp lại | Nhân viên tài chính phải so khớp từng phiên sạc với dòng hóa đơn đối tác. |
| 3 | VinFast | AI có thể cải thiện | Mô tả lỗi bằng tiếng Việt thường thiếu cấu trúc, khiến bộ phận tiếp nhận mất công hỏi lại trước khi chuyển kỹ thuật. |
| 4 | Vinhomes | Vướng mắc của người vận hành | Phản ánh của cư dân có thể được chuyển nhầm bộ phận khi nội dung đề cập nhiều sự cố. |
| 5 | Vinpearl | Tốn thời gian | Nhân viên đặt phòng phải tách ngày, số khách và yêu cầu phòng từ email đặt đoàn trước khi kiểm tra tồn. |

## Thẻ 1 · Hướng dẫn khách tìm trạm sạc VinFast

- **Người vận hành** — Nhân viên hỗ trợ khách hàng VinFast.
- **Quy trình giả định** — Nhận yêu cầu → xác minh xe, pin và vị trí → tra cứu trạm, đối chiếu cổng và trạng thái → gửi hướng dẫn hoặc chuyển cứu hộ.
- **Điểm nghẽn** — Tra cứu và đối chiếu trạm khoảng 5 phút/ca theo giả định của bài lab.
- **Can thiệp** — Rule lọc trạm và áp dụng ngưỡng pin. LLM soạn nháp từ phương án đã xác định. Nhân viên duyệt và xử lý ca thiếu dữ liệu.
- **Mục tiêu** — Duyệt trong dưới 3 phút kể từ khi có đủ dữ liệu. Mọi trạm được đề xuất trong bộ ca thử đều tương thích và đã xác nhận còn trụ.

## Thẻ 2 · Đối chiếu hóa đơn sạc đối tác

- **Người vận hành** — Nhân viên tài chính phụ trách đối soát.
- **Quy trình giả định** — Nhận hóa đơn → xuất dữ liệu phiên sạc → đối chiếu mã phiên, sản lượng và số tiền → kiểm tra các dòng lệch → lập biên bản.
- **Điểm nghẽn** — Đối chiếu thủ công khoảng 30 phút/100 dòng theo giả định.
- **Can thiệp** — Rule so khớp mã phiên, đơn vị và số tiền. Nhân viên xem các dòng thiếu mã, trùng hoặc chênh lệch.
- **Mục tiêu** — Đối chiếu 100 dòng trong dưới 10 phút và gắn cờ đủ 10 dòng sai lệch được cài sẵn trong bộ thử.

## Thẻ 3 · Phân loại mô tả lỗi xe

- **Người vận hành** — Nhân viên tiếp nhận yêu cầu dịch vụ.
- **Quy trình giả định** — Nhận mô tả → hỏi thêm điều kiện phát sinh → gắn nhóm triệu chứng → chuyển kỹ thuật viên.
- **Điểm nghẽn** — Đọc mô tả và xác định thông tin cần hỏi lại mất khoảng 5 phút/ca theo giả định.
- **Can thiệp** — LLM trích xuất triệu chứng và đề xuất nhóm tiếp nhận. Kỹ thuật viên xác nhận phân loại và quyết định chẩn đoán.
- **Mục tiêu** — Phân loại nháp trong dưới 2 phút/ca và đạt ít nhất 90% khớp nhãn trên 30 mô tả đã được kỹ thuật viên gắn nhãn.

## Chọn đề tài

Chọn hướng dẫn trạm sạc vì có thể kiểm thử cả lựa chọn trạm lẫn tình huống pin yếu bằng dữ liệu giả lập. Đối soát hóa đơn phù hợp với rule so khớp. Phân loại mô tả lỗi cần bộ ca có nhãn do kỹ thuật viên xác nhận.
