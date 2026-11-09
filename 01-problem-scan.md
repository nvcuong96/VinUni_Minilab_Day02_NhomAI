# 🔍 Phase 1 — SCAN: Tìm kiếm cơ hội (Cá nhân)

Dùng **4 Lenses** quét qua vận hành của các công ty thành viên Vingroup.

| # | Subsidiary | Lens | Mô tả ngắn bài toán |
|---|------------|------|---------------------|
| 1 | **VinFast** | Tốn thời gian | Điều phối viên và kỹ thuật viên phải đọc, phân loại yêu cầu bảo hành/sửa chữa, tra cứu lịch sử lỗi, xác định phụ tùng cần thay và chuyển ticket đến đúng xưởng. Khi mạng lưới hậu mãi mở rộng, việc chuẩn hóa xử lý giữa các xưởng trở nên khó khăn. |
| 2 | **Xanh SM** | Lặp lại | Điều phối thủ công xe vào các trạm sạc, phân bổ thứ tự sạc và điều chỉnh kế hoạch nhận chuyến theo SOC, vị trí xe, mật độ trạm và nhu cầu di chuyển. |
| 3 | **Vinhomes** | AI-upgrade | Phân loại, tổng hợp và xử lý phản ánh của cư dân từ Vinhomes Resident: lỗi thanh toán, đăng ký khuôn mặt, thẻ ra vào, đặt tiện ích, kỹ thuật tòa nhà... Nhân viên phải đọc từng phản ánh, xác định bộ phận phụ trách và soạn phản hồi. |
| 4 | **Vinmec** | Pain từ người khác | Nhân viên tiếp nhận và bộ phận bảo hiểm phải kiểm tra thủ công tính đầy đủ, tính nhất quán của hồ sơ thanh toán bảo hiểm y tế/bảo hiểm tư nhân, đối chiếu chỉ định, kết quả xét nghiệm, chẩn đoán và chứng từ trước khi gửi yêu cầu thanh toán. |
| 5 | **Vinmec** | Tốn thời gian |Điều phối giường bệnh và quy trình xuất viện: xác nhận bệnh nhân đủ điều kiện ra viện, hoàn tất hồ sơ, phối hợp bác sĩ – điều dưỡng – dược sĩ – thu ngân và vệ sinh phòng để giải phóng giường tiếp theo. |

---

# 🃏 Phase 2 — QUICK-ASSESS: 3 Quick Problem Cards (Cá nhân)

Chọn top 3 từ danh sách SCAN: **#1 (VinFast AI Aftersales & Warranty Copilot), #4 (Vinmec AI Insurance Claim Document Validator), #6 (Vinmec Điều phối giường bệnh và quy trình xuất viện).**

## Thẻ bài toán tiêu biểu: Card #4 — Vinmec AI Insurance Claim Document Validator

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                       │
│                                                             │
│ Bài toán: Nhân viên tiếp nhận và bộ phận bảo hiểm phải kiểm tra thủ công tính đầy đủ, tính nhất quán của hồ sơ thanh toán bảo hiểm y tế/bảo hiểm tư nhân, đối chiếu chỉ định, kết quả xét nghiệm, chẩn đoán và chứng từ trước khi gửi yêu cầu thanh toán.     │
│ Công ty thành viên: [x] Vinmec (GSM)                       │
│                                                             │
│ Ai đang đau? Bệnh nhân (chờ đợi), Nhân viên bảo hiểm (quá tải)     │
│                                                             │
│ Workflow thủ công hiện tại (5 bước):                        │
│   1. Khách hàng liên hệ công ty bảo hiểm.
    2. Công ty bảo hiểm phát hành Guarantee of Payment (GOP).
    3. Khách hàng xuất trình giấy tờ.
    4. Sau khi khám và có kết quả, bệnh viện phối hợp với bảo hiểm xác nhận phạm vi chi trả.
    5. Khách hàng xác nhận hồ sơ và thanh toán phần không được bảo hiểm chi trả.                                        │
│                                                             │
│ Bước nào tốn nhất? Bước kiểm tra – đối chiếu hồ sơ bảo hiểm và xử lý hồ sơ bị thiếu/sai (⏱ 15 phút/lượt)                │
│ AI có thể nhảy vào hỗ trợ ở bước nào? 
Bước 3–4: Tự động đọc, trích xuất và xác minh chứng từ trước khi gửi yêu cầu bảo hiểm.

OCR/Document AI: Đọc thẻ bảo hiểm, GOP, hóa đơn, chỉ định và kết quả xét nghiệm.

LLM + RAG: Trích xuất, chuẩn hóa và đối chiếu thông tin theo quy định bảo hiểm.

Rule Engine: Phát hiện giấy tờ thiếu, thông tin sai lệch hoặc không nhất quán.

AI Agent: Tự động tạo checklist hồ sơ còn thiếu và soạn yêu cầu bổ sung cho nhân viên/bộ phận liên quan. │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                        │
│ Thời gian kiểm tra hồ sơ: 15 phút → dưới 3 phút/hồ sơ.

Tỷ lệ hồ sơ được kiểm tra tự động: ≥ 70%.

Tỷ lệ phát hiện hồ sơ thiếu/sai trước khi gửi bảo hiểm: ≥ 90% (mục tiêu pilot).

Giảm ít nhất 50% thời gian xử lý thủ công đối với hồ sơ thông thường.     │
│                                                             │
│ Quick Architecture: [x] AI Document Processing + LLM Feature + RAG + Rule Engine + Agentic Workflow.   │
└─────────────────────────────────────────────────────────────┘
```

---

# 🗳️ Quyết định lựa chọn của nhóm:
Nhóm quyết định chọn bài toán **"Vinmec — Giám sát & Cảnh báo Sớm Rủi ro Thuốc Cận Date (FEFO) (Vinmec Pharmacy Expiry Sentinel)."** để thực hiện Deep-Dive.

* **Lý do lựa chọn:**
  1. **Tác động kinh tế và an toàn điều trị rất lớn (High Impact):** Dược phẩm cận date hoặc hết hạn không chỉ gây tổn thất tài chính trực tiếp (giả định 0.5–2% giá trị tồn kho mỗi năm) mà còn tiềm ẩn nguy cơ an toàn y tế nghiêm trọng nếu bỏ sót lô thuốc hết hạn. Việc tự động hóa giám sát giúp loại bỏ hoàn toàn sai sót do con người rà soát thủ công.
  2. **Ranh giới an toàn (Operational Boundary) sắc bén & chuẩn mực y tế:** Dữ liệu số lô (Lot), hạn dùng (EXP) và tốc độ tiêu thụ có cấu trúc rõ ràng. AI chỉ được phép quét cảnh báo và soạn thảo bản nháp đề xuất luân chuyển có gắn thẻ `[DRAFT_ONLY]`; AI **tuyệt đối không được tự ý ra y lệnh điều trị, không tự ý hủy thuốc hay điều chuyển** mà bắt buộc phải qua bước phê duyệt của Dược sĩ phụ trách kho và Trưởng khoa Lâm sàng (Human-in-the-loop).
  3. **Tỷ lệ AI-Fit tối ưu (Rule Engine + LLM Feature):** Dùng Rule-based/Data Analytics tính toán tốc độ tiêu thụ và lọc hạn dùng theo nguyên tắc FEFO (chính xác 100%), kết hợp LLM để tóm tắt danh sách ưu tiên hành động và soạn nháp báo cáo/email điều chuyển thuốc chuyên nghiệp.
* **Lý do không chọn Thẻ 1 (Vinhomes) & Thẻ 3 (VinFast):**
  * *Thẻ 1 (Vinhomes):* Bài toán đặt tiện ích mang tính chất dịch vụ dân sự thông thường, ít rủi ro và giá trị nghiệp vụ không mang tính sống còn như kiểm soát date dược phẩm trong bệnh viện.
  * *Thẻ 3 (VinFast):* Bài toán điều chuyển xe điện toàn quốc phụ thuộc vào mạng lưới logistics phức tạp và biến động chính sách bán hàng theo tháng, cần khối lượng dữ liệu lịch sử lớn hơn để xác lập baseline chuẩn.

---