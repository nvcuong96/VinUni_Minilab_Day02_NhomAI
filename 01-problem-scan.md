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

## Phase 2 — QUICK-ASSESS (Cá nhân, 30 min)

Chọn top 3 bài toán từ Phase 1: **#1 lịch hẹn, #2 thủ tục hành chính, #3 phản hồi dịch vụ** (10 phút/card). Các thời gian dưới đây là giả định; mục tiêu vận hành chưa được kiểm chứng. Thời gian chỉ tính thao tác chủ động, không tính chờ phản hồi. Đánh dấu cả Rule và LLM vì hai phần hỗ trợ các bước khác nhau trong cùng giải pháp.

```text
┌────────────────────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                                                  │
│                                                                                        │
│ Bài toán (1 câu): Giảm thời gian xử lý tin nhắn đặt/đổi/hủy lịch bằng cách trích xuất  │
│ yêu cầu và soạn nháp để nhân viên duyệt.                                               │
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [ ] Vinhomes                             │
│                     [x] Vinmec   [ ] Khác (Ghi rõ): Không                              │
│                                                                                        │
│ Ai đang đau (Actor)? Nhân viên tiếp nhận/đặt lịch; người yêu cầu phải cung cấp và xác  │
│ nhận thông tin nhiều lần.                                                              │
│                                                                                        │
│ Workflow thủ công hiện tại (3-5 bước):                                                 │
│ 1. Nhận tin → 2. Đọc/hỏi thêm → 3. Tra lịch                                            │
│ → 4. Trao đổi phương án → 5. Xác minh và thực hiện trên hệ thống.                      │
│                                                                                        │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2 và 4 (3 + 3 = 6 phút/lượt trong tổng 10 phút;  │
│ giả định).                                                                             │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2: trích ý định và trường dữ liệu; bước 4:  │
│ soạn nháp hỏi bổ sung hoặc đề xuất. Code kiểm tra và đối chiếu lịch ở bước 3.          │
│                                                                                        │
│ Đo thành công bằng gì (Metric có số)? Mục tiêu giảm thao tác chủ động từ 10 xuống ≤3   │
│ phút/lượt; 10/10 ca lab giữ nhãn nháp và chờ duyệt, 0 slot bịa. Hiệu quả thời gian     │
│ chưa được đo thực tế.                                                                  │
│                                                                                        │
│ Quick Architecture: [ ] No AI  [x] Rule  [x] LLM  [ ] Agent                            │
└────────────────────────────────────────────────────────────────────────────────────────┘
```

**Ranh giới:** Không tự xác nhận lịch, tư vấn y tế hoặc chọn chuyên khoa từ triệu chứng; chỉ dùng dữ liệu giả lập.

```text
┌────────────────────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                                                  │
│                                                                                        │
│ Bài toán (1 câu): Soạn nháp câu trả lời về giấy tờ và thủ tục đến khám từ tài liệu     │
│ hành chính đã được duyệt.                                                              │
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [ ] Vinhomes                             │
│                     [x] Vinmec   [ ] Khác (Ghi rõ): Không                              │
│                                                                                        │
│ Ai đang đau (Actor)? Nhân viên tổng đài/tiếp nhận phải tìm và viết lại hướng dẫn;      │
│ người đến khám cần câu trả lời dễ hiểu.                                                │
│                                                                                        │
│ Workflow thủ công hiện tại (3-5 bước):                                                 │
│ 1. Đọc câu hỏi → 2. Xác định thủ tục → 3. Tìm tài liệu                                 │
│ → 4. Soạn trả lời → 5. Kiểm tra và gửi.                                                │
│                                                                                        │
│ Bước nào tốn thời gian/lỗi nhất? Bước 3 và 4 (tổng 4 phút/lượt trong tổng 6 phút; giả  │
│ định).                                                                                 │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3-4: tìm đoạn liên quan và diễn đạt thành   │
│ nháp, kèm tài liệu nguồn; nhân viên duyệt tại bước 5.                                  │
│                                                                                        │
│ Đo thành công bằng gì (Metric có số)? Mục tiêu giảm tổng thao tác từ 6 xuống ≤2        │
│ phút/lượt; 100% nháp có nguồn phù hợp hoặc chuyển nhân viên khi thiếu nguồn. Đây là    │
│ mục tiêu chưa đo.                                                                      │
│                                                                                        │
│ Quick Architecture: [ ] No AI  [x] Rule  [x] LLM  [ ] Agent                            │
└────────────────────────────────────────────────────────────────────────────────────────┘
```

**Ranh giới:** Rule xử lý FAQ cố định; LLM truy xuất và diễn đạt tài liệu. Không tự thêm phí, điều kiện bảo hiểm hoặc hướng dẫn chuyên môn; nhân viên duyệt.

```text
┌────────────────────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                                                  │
│                                                                                        │
│ Bài toán (1 câu): Phân loại phản hồi dịch vụ và đề xuất chuyển đến đúng bộ phận để     │
│ nhân viên kiểm tra.                                                                    │
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [ ] Vinhomes                             │
│                     [x] Vinmec   [ ] Khác (Ghi rõ): Không                              │
│                                                                                        │
│ Ai đang đau (Actor)? Nhân viên chăm sóc khách hàng, đầu mối xử lý phản hồi và người    │
│ gửi góp ý bị chuyển nhầm bộ phận.                                                      │
│                                                                                        │
│ Workflow thủ công hiện tại (3-5 bước):                                                 │
│ 1. Nhận góp ý → 2. Đọc → 3. Phân loại                                                  │
│ → 4. Chuyển bộ phận → 5. Theo dõi và phản hồi.                                         │
│                                                                                        │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2 và 3 (tổng 3 phút/lượt trong tổng 5 phút; giả  │
│ định).                                                                                 │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2-3: tóm tắt ý chính, đề xuất nhãn và bộ    │
│ phận theo danh mục duyệt trước; nhân viên duyệt trước khi chuyển ở bước 4.             │
│                                                                                        │
│ Đo thành công bằng gì (Metric có số)? Mục tiêu ≥90% đề xuất trùng nhãn nhân viên trên  │
│ 50 phản hồi gán nhãn; đọc/phân loại từ 3 phút xuống ≤30 giây/lượt. Đây là mục tiêu     │
│ chưa đo.                                                                               │
│                                                                                        │
│ Quick Architecture: [ ] No AI  [x] Rule  [x] LLM  [ ] Agent                            │
└────────────────────────────────────────────────────────────────────────────────────────┘
```

**Ranh giới:** Rule xử lý từ khóa rõ, LLM hỗ trợ văn bản dài/nhiều ý. Không tự đóng phản hồi, hứa bồi thường hoặc đánh giá đúng/sai chuyên môn.


## Chọn Card 1 để deep-dive

Lịch hẹn có thể biểu diễn bằng tập slot hữu hạn và kiểm tra bằng code, nên phù hợp giới hạn buổi lab. Có thể tạo ca đặt/đổi/hủy, thiếu dữ liệu và prompt injection mà không cần bệnh án thật.

Card 2 cần tài liệu thủ tục được xác nhận và cập nhật. Card 3 cần danh mục bộ phận và phản hồi đã gán nhãn. Bài tập hiện chưa có những dữ liệu đó. Lựa chọn này dựa trên khả năng kiểm chứng trong lab, không khẳng định lịch hẹn đang là ưu tiên thực tế cao nhất tại Vinmec.

## Phản biện và điều chỉnh

1. **Baseline có thật không?** Chưa. Đánh dấu các mốc phút là giả định; cần đo thao tác thực tế trước khi tính ROI.
2. **Rule có giải quyết được không?** Có, nếu đầu vào là form chuẩn. Chỉ thử LLM cho tin nhắn tự do; giữ rule cho đối chiếu và kiểm tra trường bắt buộc.
3. **Có giảm tổng thời gian chờ không?** Chưa chứng minh. Chỉ đặt mục tiêu giảm thời gian thao tác chủ động, không đồng nhất với SLA phản hồi hay thời gian chờ khám.
