# Lab 02 — Problem Scan & Quick Problem Cards

- **Tên nhóm:** NhómAI
- **Người thực hiện bài cá nhân:** Nguyễn Nhật Minh
- **Email:** nnhatminh2610@gmail.com

**Thành viên tham gia:**

| Họ và tên | Email |
|---|---|
| Nguyễn Nhật Minh | nnhatminh2610@gmail.com|
| Phạm Thế Hùng | thehung1406@gmail.com |
| Ngô Duy Khánh | dtonyk231@gmail.com |
| Phạm Đức Vinh | vinhnd.dev@gmail.com |
| Nguyễn Văn Cường | nvcuong9696@gmail.com |

**Phạm vi bài nộp:** Danh sách 5 ý tưởng và 3 card bên dưới là phần khảo sát cá nhân. Sau khi thảo luận, nhóm chọn **Vinmec Pharmacy Expiry Sentinel** cho phần phân tích sâu tại `02-deep-dive-report.md`. Phần code cá nhân thực hiện bài mẫu Xanh SM theo yêu cầu lớp đã được người làm bài xác nhận.

# Phase 1 — SCAN

Sử dụng bốn góc nhìn: **Lặp lại**, **Tốn thời gian**, **AI có thể tốt hơn** và **Pain từ người khác**.

| # | Subsidiary (VinFast/Xanh SM...) | Lens | Mô tả ngắn bài toán |
|---|---|---|---|
| 1 | Xanh SM | Tốn thời gian | Khi tài xế báo sự cố pin, điều phối viên có thể phải tra cứu vị trí, mức pin và phương án hỗ trợ rồi soạn hướng dẫn qua nhiều thao tác. |
| 2 | Vinhomes | AI có thể tốt hơn | Phản ánh của cư dân bằng văn bản tự do có thể thiếu thông tin hoặc chứa nhiều vấn đề, khiến nhân viên khó phân loại và chọn đúng bộ phận tiếp nhận. |
| 3 | VinFast | Lặp lại | Nhân viên đối soát có thể phải lặp lại việc so khớp bản ghi phiên sạc với hóa đơn đối tác để tìm chênh lệch về giao dịch, thời điểm, điện năng và số tiền. |
| 4 | Vinpearl | Tốn thời gian | Nhân viên đặt phòng đoàn phải đọc email, trích xuất ngày lưu trú, số khách, số phòng và yêu cầu đặc biệt, rồi kiểm tra các mục còn thiếu. |
| 5 | Vinpearl | Pain từ người khác | Phàn nàn trong các đánh giá của khách có thể bị phát hiện muộn hoặc không được tổng hợp đúng chủ đề, làm chậm việc chuyển tới bộ phận phụ trách. |

**Ba bài toán được chọn cho Phase 2:** #1 — Xanh SM, #2 — Vinhomes và #4 — Vinpearl.

---

# Phase 2 — QUICK-ASSESS

## Quick Problem Card #1 — Xanh SM: Xử lý sự cố pin

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                       │
│                                                             │
│ Bài toán (1 câu): Điều phối viên cần tổng hợp thông tin sự  │
│   cố pin và chuẩn bị phương án hỗ trợ tài xế nhanh hơn,     │
│   đồng thời kiểm soát nội dung được gửi đi.                 │
│                                                             │
│ Công ty thành viên: [ ] VinFast  [x] Xanh SM  [ ] Vinhomes  │
│                   [ ] Vinmec  [ ] Khác                      │
│                                                             │
│ Ai đang đau (Actor)? Điều phối viên xử lý sự cố và tài xế   │
│   đang chờ phương án hỗ trợ.                                │
│                                                             │
│ Workflow thủ công hiện tại (3–5 bước, giả định):            │
│                                                             │
│   1. Điều phối viên mở báo cáo đã có thông tin đầu vào và   │
│   ghi nhận yêu cầu — 2 phút.                                │
│   → 2. Kiểm tra thông tin xe, vị trí và mức pin — 2 phút.   │
│   → 3. Tra cứu trạm sạc hoặc phương án hỗ trợ phù hợp — 5   │
│   phút.                                                     │
│   → 4. Soạn hướng dẫn hoặc yêu cầu hỗ trợ — 5 phút.         │
│   → 5. Rà soát và duyệt phương án — 1 phút.                 │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 3–4: tra cứu và soạn  │
│   nội dung, giả định 10 phút/lượt; tổng quy trình giả định  │
│   15 phút/lượt.                                             │
│                                                             │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2–4: trích       │
│   thông tin từ báo cáo và soạn phương án nháp từ dữ liệu    │
│   được cung cấp. Rule kiểm tra thông tin bắt buộc, mức pin  │
│   và khoảng cách; điều phối viên duyệt.                     │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│                                                             │
│ - Mục tiêu giảm thời gian trung vị từ 15 phút giả định      │
│   xuống ≤5 phút/lượt, tính từ lúc mở báo cáo đủ dữ liệu     │
│   đến lúc phương án được duyệt; không tính thời gian di     │
│   chuyển hoặc chờ cứu hộ.                                   │
│ - Mục tiêu đúng hành động ở ít nhất 18/20 tình huống giả    │
│   lập, đo trước khi người duyệt sửa. Trong bộ thử nghiệm:   │
│   0 ca chấp thuận chỉ đường tới trạm hơn 5 km khi pin dưới  │
│   5% và 0 ca tự gửi tin hoặc điều xe thật mà chưa duyệt.    │
│                                                             │
│ Quick Architecture: [ ] No AI  [x] Rule  [x] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘
```

**Kiểm chứng và ranh giới bổ sung:** Dự kiến 20 tình huống giả lập: 8 thông thường, 6 pin thấp, 4 thiếu/mâu thuẫn dữ liệu và 2 yêu cầu bỏ quy tắc; so sánh xử lý thủ công, rule + mẫu tin và rule + LLM. Bản nháp gửi tài xế phải có `[DRAFT_ONLY]`. Theo quy tắc bài lab, pin dưới 5% thì đề xuất `dispatch_mobile_charger` để người phụ trách duyệt; đây không phải kết luận về quãng đường thực tế của xe. Không bịa dữ liệu trạm, vị trí hoặc khoảng cách; nếu thiếu dữ liệu hoặc mô hình lỗi, chuyển điều phối viên xác minh và xử lý thủ công. Chưa giả định có kết nối hệ thống vận hành thật.

---

## Quick Problem Card #2 — Vinhomes: Phân loại phản ánh cư dân

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                       │
│                                                             │
│ Bài toán (1 câu): Nhân viên tiếp nhận cần phân loại phản    │
│   ánh bằng văn bản tự do và xác định đúng bộ phận phụ       │
│   trách nhanh hơn.                                          │
│                                                             │
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [x] Vinhomes  │
│                   [ ] Vinmec  [ ] Khác                      │
│                                                             │
│ Ai đang đau (Actor)? Nhân viên chăm sóc khách hàng/ban      │
│   quản lý và cư dân có phản ánh cần xử lý.                  │
│                                                             │
│ Workflow thủ công hiện tại (3–5 bước, giả định):            │
│                                                             │
│   1. Tiếp nhận và mở nội dung phản ánh — 1 phút.            │
│   → 2. Đọc, xác định các vấn đề và mức cần ưu tiên — 3      │
│   phút.                                                     │
│   → 3. Tra cứu địa điểm và bộ phận phụ trách — 2 phút.      │
│   → 4. Soạn xác nhận, rà soát và duyệt đề xuất chuyển tiếp  │
│   hoặc câu hỏi bổ sung — 2 phút.                            │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2–3: xác định vấn đề  │
│   và nơi tiếp nhận, giả định 5 phút/lượt; tổng quy trình    │
│   giả định 8 phút/lượt.                                     │
│                                                             │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2–3: LLM trích   │
│   địa điểm, tóm tắt và đề xuất nhãn. Rule kiểm tra trường   │
│   bắt buộc, ánh xạ nhãn–địa điểm sang bộ phận; nhân viên    │
│   kiểm tra trước khi chuyển.                                │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│                                                             │
│ - Mục tiêu giảm thời gian trung vị từ 8 phút giả định       │
│   xuống ≤4 phút/lượt, từ lúc mở phản ánh đến khi nhân viên  │
│   duyệt đề xuất chuyển hoặc hỏi lại; tính cả thời gian      │
│   người duyệt sửa.                                          │
│ - Mục tiêu đề xuất đúng toàn bộ bộ phận ở ít nhất 27/30 ca  │
│   thông thường trước khi người duyệt sửa. Trong bộ thử      │
│   nghiệm, 20/20 ca ngoại lệ cần được giữ để nhân viên xem   │
│   xét và 0 ca tự chuyển, gửi hoặc đóng phản ánh trước khi   │
│   duyệt.                                                    │
│                                                             │
│ Quick Architecture: [ ] No AI  [x] Rule  [x] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘
```

**Kiểm chứng và ranh giới bổ sung:** Dự kiến 50 phản ánh giả lập: 30 thông thường, 10 mơ hồ/thiếu thông tin và 10 có dấu hiệu cần ưu tiên; so sánh xử lý thủ công, rule từ khóa và rule + LLM. Danh mục bộ phận trong bộ thử là giả lập. AI chỉ đề xuất, không tự đóng phản ánh, cam kết thời hạn hay quyết định bồi thường. Khi thiếu thông tin, nhãn xung đột, có dấu hiệu cần ưu tiên hoặc mô hình lỗi, chuyển nhân viên xem xét thủ công. Nếu rule đáp ứng mục tiêu với chi phí thấp hơn, ưu tiên rule.

---

## Quick Problem Card #3 — Vinpearl: Trích xuất email đặt phòng đoàn

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                       │
│                                                             │
│ Bài toán (1 câu): Nhân viên đặt phòng đoàn cần trích xuất   │
│   yêu cầu và phát hiện thông tin thiếu hoặc mâu thuẫn       │
│   trong email nhanh hơn.                                    │
│                                                             │
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [ ] Vinhomes  │
│                   [ ] Vinmec  [x] Khác: Vinpearl            │
│                                                             │
│ Ai đang đau (Actor)? Nhân viên đặt phòng đoàn; đại diện     │
│   đoàn và bộ phận kinh doanh phối hợp cung cấp thông tin.   │
│                                                             │
│ Workflow thủ công hiện tại (3–5 bước, giả định):            │
│                                                             │
│   1. Mở email và đọc chuỗi trao đổi — 2 phút.               │
│   → 2. Trích ngày lưu trú, số phòng, số khách, loại phòng   │
│   và yêu cầu đặc biệt vào bảng — 6 phút.                    │
│   → 3. Rà soát thông tin thiếu, mâu thuẫn hoặc đã thay đổi  │
│   — 3 phút.                                                 │
│   → 4. Đối chiếu phòng và giá qua nguồn chính thức, hoặc    │
│   hỏi lại nếu chưa đủ thông tin — 6 phút.                   │
│   → 5. Soạn phản hồi hỏi lại hoặc trình phương án — 3       │
│   phút.                                                     │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2–3: trích xuất và    │
│   rà soát, giả định 9 phút/email; tổng quy trình tham       │
│   chiếu giả định 20 phút/email.                             │
│                                                             │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2–3: trích xuất  │
│   thành JSON/bảng nháp, dẫn đoạn làm căn cứ, đánh dấu       │
│   thông tin thiếu/mâu thuẫn và đề xuất câu hỏi làm rõ.      │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│                                                             │
│ - Mục tiêu giảm thời gian riêng bước 2–3 từ 9 phút giả      │
│   định xuống ≤3 phút/email, gồm thời gian nhân viên kiểm    │
│   tra và sửa; không phải mục tiêu cho toàn bộ quy trình.    │
│ - Mục tiêu độ đúng theo trường ≥95% trên 20 email giả lập,  │
│   tính trước khi người duyệt sửa. Trường thiếu để null;     │
│   trường mâu thuẫn phải được gắn cờ.                        │
│ - Trong bộ thử nghiệm, 100% ca thiếu hoặc mâu thuẫn được    │
│   gắn cờ và 0 ca tự bịa phòng trống/giá, gửi email hay xác  │
│   nhận giữ phòng.                                           │
│                                                             │
│ Quick Architecture: [ ] No AI  [x] Rule  [x] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘
```

**Kiểm chứng và ranh giới bổ sung:** Dự kiến 20 email giả lập: 5 đầy đủ, 5 thiếu trường, 5 có thông tin thay đổi/mâu thuẫn và 5 chứa yêu cầu vượt quyền. Gán nhãn cơ sở lưu trú, ngày nhận/trả phòng, số phòng, số khách, loại phòng và yêu cầu đặc biệt; so sánh nhập thủ công, rule và rule + LLM. Nhân viên kiểm tra bản nháp và dùng nguồn chính thức để xác minh phòng/giá; AI không tự gửi email, giữ phòng hoặc đặt phòng. Khi mô hình lỗi hoặc dữ liệu mâu thuẫn, chuyển nhân viên xử lý thủ công.

---

**Các thông tin cần bổ sung trước khi nộp:** Email của Nguyễn Nhật Minh và họ tên/email của các thành viên tham gia. Các baseline và chỉ tiêu cần được đối chiếu lại khi có khảo sát hoặc kết quả kiểm thử thực tế.
