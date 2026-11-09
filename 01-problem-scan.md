# Quét cơ hội AI — Vinmec

**Bài cá nhân, nhánh `thehung1406` — Lab 02: AI Product Scoping.**

Đây là bài tập mô phỏng, không phải khảo sát được Vinmec xác nhận. Các quy trình và baseline dưới đây là giả định để xây dựng prototype. Không sử dụng hồ sơ bệnh nhân thật.

## Phase 1 — SCAN: 5 cơ hội, 4 góc nhìn

| # | Đơn vị | Góc nhìn (lens) | Bài toán và người gặp khó khăn | Cơ hội hỗ trợ |
|---|---|---|---|---|
| 1 | Vinmec | Lặp lại | Nhân viên tiếp nhận đọc tin nhắn đặt/đổi/hủy lịch, hỏi lại dữ liệu thiếu và tra lịch nhiều lần. | Trích xuất yêu cầu và soạn nháp phương án theo lịch được cung cấp. |
| 2 | Vinmec | AI có thể tốt hơn | Người đến khám cần diễn giải hướng dẫn giấy tờ và thủ tục theo nhiều cách hỏi. | Tìm và diễn đạt lại hướng dẫn hành chính đã được duyệt, kèm nguồn. |
| 3 | Vinmec | Pain từ người khác | Người dùng phải nhắc lại phản hồi về thời gian chờ, thanh toán hoặc dịch vụ vì chuyển nhầm bộ phận. | Phân loại phản hồi và soạn đề xuất chuyển đúng bộ phận để nhân viên duyệt. |
| 4 | Vinmec | Tốn thời gian | Bác sĩ phải tổng hợp nhiều phần hồ sơ khi chuẩn bị tóm tắt xuất viện. | Tạo bản nháp có dẫn chứng để bác sĩ đối chiếu; không tự thêm thông tin điều trị. |
| 5 | Vinmec | Lặp lại | Nhân viên bảo hiểm tổng hợp giấy tờ từ nhiều yêu cầu và kiểm tra thiếu mục. | Trích thông tin, đối chiếu checklist; không quyết định quyền lợi hoặc cam kết thanh toán. |

## Phase 2 — QUICK-ASSESS: 3 Quick Problem Cards

### Card 1 — Trợ lý xử lý lịch hẹn

| Thành phần | Nội dung |
|---|---|
| Bài toán | Giảm thao tác đọc và hỏi lại tin nhắn đặt/đổi/hủy lịch của nhân viên tiếp nhận. |
| Actor | Nhân viên tiếp nhận/đặt lịch; người yêu cầu là bên cung cấp và chốt thông tin. |
| Current workflow | Nhận tin → đọc/hỏi thêm → tra lịch → trao đổi phương án → xác minh và thực hiện trên hệ thống. |
| Bottleneck | Đọc/hỏi thêm và trao đổi phương án: giả định 3 + 3 phút/yêu cầu trong tổng 10 phút. Chưa gồm thời gian chờ người dùng trả lời. |
| AI hỗ trợ | Trích xuất ý định và trường dữ liệu từ ngôn ngữ tự nhiên. Code đối chiếu lịch, tạo nháp hỏi bổ sung hoặc đề xuất. |
| Metric | Mục tiêu giả thuyết: giảm thời gian thao tác chủ động từ 10 xuống ≤3 phút/yêu cầu. Trong bộ 10 ca: 10/10 nhãn nháp và chờ duyệt; không bịa slot. |
| Quick architecture | **Rule + LLM Feature**. Không có agent thao tác lịch hẹn. |
| Ranh giới | Không tự xác nhận lịch; không tư vấn y tế; không chọn chuyên khoa từ triệu chứng; dữ liệu chỉ giả lập. |

### Card 2 — Hướng dẫn thủ tục hành chính

| Thành phần | Nội dung |
|---|---|
| Bài toán | Soạn nháp câu trả lời về giấy tờ và quy trình đến khám từ tài liệu đã được duyệt. |
| Actor | Nhân viên tổng đài/tiếp nhận và người đến khám. |
| Current workflow | Đọc câu hỏi → xác định thủ tục → tìm tài liệu → soạn trả lời → kiểm tra và gửi. |
| Bottleneck | Tìm tài liệu và viết lại: giả định 4 phút trong tổng 6 phút/yêu cầu. |
| AI hỗ trợ | Tìm đoạn liên quan và diễn đạt thành nháp, đính kèm tài liệu nguồn. |
| Metric | Mục tiêu chưa đo: tổng thao tác ≤2 phút; 100% câu trả lời có nguồn phù hợp hoặc chuyển nhân viên khi thiếu nguồn. |
| Quick architecture | Rule cho FAQ cố định; LLM + truy xuất tài liệu khi cách hỏi đa dạng. |
| Ranh giới | Không tự thêm phí, điều kiện bảo hiểm hoặc hướng dẫn chuyên môn; nhân viên duyệt. |

### Card 3 — Phân loại phản hồi dịch vụ

| Thành phần | Nội dung |
|---|---|
| Bài toán | Đề xuất chuyển góp ý dịch vụ đến đúng bộ phận, giảm đọc và phân loại thủ công. |
| Actor | Nhân viên chăm sóc khách hàng và đầu mối xử lý phản hồi. |
| Current workflow | Nhận góp ý → đọc → phân loại → chuyển bộ phận → theo dõi và phản hồi. |
| Bottleneck | Đọc/phân loại: giả định 3 phút trong tổng 5 phút thao tác/yêu cầu. |
| AI hỗ trợ | Tóm tắt ý chính, đề xuất nhãn và bộ phận tiếp nhận theo danh mục duyệt trước. |
| Metric | Mục tiêu chưa đo: ≥90% đề xuất trùng nhãn nhân viên trên 50 phản hồi gán nhãn; phân loại ≤30 giây. |
| Quick architecture | Rule cho từ khóa rõ; LLM Feature cho văn bản dài hoặc nhiều ý. |
| Ranh giới | Không tự đóng phản hồi, hứa bồi thường hoặc đánh giá đúng/sai về chuyên môn. |

## Chọn Card 1 để deep-dive

Lịch hẹn có thể biểu diễn bằng tập slot hữu hạn và kiểm tra bằng code, nên phù hợp giới hạn buổi lab. Có thể tạo ca đặt/đổi/hủy, thiếu dữ liệu và prompt injection mà không cần bệnh án thật.

Card 2 cần tài liệu thủ tục được xác nhận và cập nhật. Card 3 cần danh mục bộ phận và phản hồi đã gán nhãn. Bài tập hiện chưa có những dữ liệu đó. Lựa chọn này dựa trên khả năng kiểm chứng trong lab, không khẳng định lịch hẹn đang là ưu tiên thực tế cao nhất tại Vinmec.

## Phản biện và điều chỉnh

1. **Baseline có thật không?** Chưa. Đánh dấu các mốc phút là giả định; cần đo thao tác thực tế trước khi tính ROI.
2. **Rule có giải quyết được không?** Có, nếu đầu vào là form chuẩn. Chỉ thử LLM cho tin nhắn tự do; giữ rule cho đối chiếu và kiểm tra trường bắt buộc.
3. **Có giảm tổng thời gian chờ không?** Chưa chứng minh. Chỉ đặt mục tiêu giảm thời gian thao tác chủ động, không đồng nhất với SLA phản hồi hay thời gian chờ khám.
