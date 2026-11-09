# 🏗️ Phase 3 — DEEP-DIVE (Nhóm)

> **Dự án:** Vinmec Pharmacy Expiry Sentinel — Giám sát & Cảnh báo Sớm Rủi ro Thuốc Cận Date (FEFO)  
> **Đơn vị thành viên:** Hệ thống Y tế Vinmec (Vingroup)  
> **Kiến trúc giải pháp:** Hybrid Architecture (Rule Engine + LLM Feature)

---

## 3.1. Current-State Workflow Mapping

Quy trình quản lý và xử lý thuốc cận date hiện tại của Dược sĩ quản lý kho và Kế toán bệnh viện Vinmec:

```text
┌─────────────────┐       ┌─────────────────┐       ┌─────────────────┐       ┌─────────────────┐
│ Bước 1          │       │ Bước 2          │       │ Bước 3          │       │ Bước 4          │
│ Xuất file tồn   │       │ Lọc thủ công    │       │ Tra cứu run-rate│       │ Soạn email gửi  │
│ kho từ HIS/ERP  │ ───→  │ danh sách cận   │ ───→  │ lịch sử kê đơn  │ ───→  │ Trưởng khoa LS  │
│                 │       │ date (<3-6 th.) │       │ tính nguy cơ hủy│       │ & Kho vận BV    │
│ Ai: Dược sĩ kho │       │ Ai: Dược sĩ kho │       │ Ai: Dược sĩ kho │       │ Ai: Dược sĩ kho │
│ ⏱ 15 phút       │       │ ⏱ 60 phút 🔴    │       │ ⏱ 90 phút 🔴    │       │ ⏱ 60 phút 🔴    │
│ In: HIS/ERP     │       │ In: File Excel  │       │ In: Lịch sử đơn │       │ In: Danh sách   │
│ Out: Raw Excel  │       │ Out: List date  │       │ Out: Tốc độ tiêu│       │ Out: Email nháp │
└─────────────────┘       └─────────────────┘       └─────────────────┘       └─────────────────┘
                                                                                       │
                                                                                       ▼
                                                                                ┌─────────────────┐
                                                                                │ Bước 5          │
                                                                                │ Lập biên bản    │
                                                                                │ điều chuyển     │
                                                                                │ thuốc liên viện │
                                                                                │ Ai: Dược + KT   │
                                                                                │ ⏱ 45 phút       │
                                                                                │ Out: BB ký duyệt│
                                                                                └─────────────────┘

🔴 = Điểm nghẽn cổ chai (Bottlenecks) tại Bước 2, 3 và 4
🔄 = Handoffs: Từ Dược sĩ kho ──> Trưởng khoa Lâm sàng / Kế toán trưởng / Bệnh viện vệ tinh
⏱ Tổng thời gian xử lý thủ công: 270 phút (khoảng 4.5 giờ/tuần)
```

---

## 3.2. Problem Statement (6-field) — Vin Smart Future Standard

| Field | Nội dung chi tiết |
|---|---|
| **1. Actor / Operator** | **Dược sĩ quản lý kho thuốc bệnh viện** (phụ trách giám sát hạn mức, xuất nhập và bảo quản dược phẩm) phối hợp cùng **Kế toán trưởng bệnh viện** (kiểm soát tổn thất tài chính, hao hụt kho). |
| **2. Current Workflow** | Định kỳ hàng tuần, dược sĩ đăng nhập vào hệ thống HIS/ERP bệnh viện, xuất báo cáo tồn kho hàng chục nghìn dòng dữ liệu ra file Excel. Sau đó, mở các trang tính để lọc thủ công các lô thuốc có hạn sử dụng (EXP) dưới 3-6 tháng; tiếp tục đối chiếu với báo cáo tiêu thụ trung bình 30-90 ngày gần nhất (run-rate / burn-rate) để ước lượng số lượng thuốc có khả năng không tiêu thụ kịp. Cuối cùng, dược sĩ tự gõ email cảnh báo gửi các Trưởng khoa Lâm sàng nhằm ưu tiên kê đơn theo nguyên tắc FEFO (First Expired, First Out) hoặc lập biên bản đề xuất điều chuyển thuốc sang các bệnh viện khác trong hệ thống Vinmec có nhu cầu cao hơn. |
| **3. Bottleneck** | **Bước 2, 3 và 4 (chiếm 3.5 – 4 giờ/tuần):**<br>• Lọc và đối chiếu thủ công hàng nghìn mã thuốc với nhiều số lô (Batch/Lot) khác nhau trên Excel rất dễ sót hoặc nhầm lẫn.<br>• Tính toán tốc độ tiêu thụ (run-rate) bằng mắt và công thức Excel rời rạc, không dự báo chính xác điểm rơi hết hạn.<br>• Soạn thảo email/báo cáo phân bổ thuốc cho từng khoa phòng mang tính cá nhân, tốn nhiều thời gian hành chính thay vì tập trung vào chuyên môn dược lâm sàng. |
| **4. Business Impact** | • **Tổn thất tài chính trực tiếp:** Mỗi năm, ước tính các bệnh viện quy mô lớn có thể chịu hao hụt từ 300 triệu đến hơn 1 tỷ đồng do thuốc hết hạn phải tiến hành tiêu hủy theo quy định khắt khe của Bộ Y tế.<br>• **An toàn người bệnh & Tiêu chuẩn JCI:** Nguy cơ phát nhầm thuốc cận date cho bệnh nhân nếu việc kiểm soát FEFO bị bỏ sót do thao tác thủ công.<br>• **Lãng phí nguồn lực:** Tiêu tốn ~200 giờ lao động chất lượng cao của Dược sĩ/năm cho việc nhập liệu và lọc bảng tính.<br>• **Mất cân đối tồn kho:** Bệnh viện này thừa thuốc sắp hết date trong khi bệnh viện khác trong cùng hệ thống Vinmec lại phải đặt mua mới từ nhà cung cấp với giá cao. |
| **5. Success Metric** | 1. **Phát hiện 100%** các lô thuốc có nguy cơ tiêu hủy trước 90 ngày (Zero missed risk).<br>2. **Giảm 90% thời gian** lập báo cáo cảnh báo và đề xuất điều chuyển tuần: từ 4 giờ xuống **dưới 15 phút**.<br>3. **Giảm tỷ lệ hao hụt do thuốc hết hạn tiêu hủy từ 30% – 50%** trong năm đầu tiên đưa hệ thống vào vận hành.<br>4. **Độ chính xác nội dung draft:** ≥ 95% email đề xuất điều chuyển/ưu tiên kê đơn được Dược sĩ duyệt gửi mà không cần chỉnh sửa lớn. |
| **6. Operational Boundary** | **ĐƯỢC PHÉP:**<br>• Truy cập đọc (Read-only) dữ liệu tồn kho, số lô, hạn dùng (EXP), danh mục hoạt chất tương đương và lịch sử kê đơn từ HIS/ERP.<br>• Dùng Rule Engine tính toán chính xác số ngày còn lại (Days-to-Expiry), tốc độ tiêu thụ (Burn-rate) và phân nhóm rủi ro (Đỏ / Vàng / Xanh).<br>• Dùng LLM tự động sinh bản nháp (Draft) tóm tắt rủi ro, dự thảo email gửi Trưởng khoa Lâm sàng và dự thảo biên bản điều chuyển liên viện.<br>• Tất cả đầu ra văn bản của AI phải gắn thẻ bắt buộc `[DRAFT_ONLY - CHỜ DƯỢC SĨ DUYỆT]`.<br><br>🛑 **CẤM TUYỆT ĐỐI:**<br>• AI không được tự ý can thiệp vào y lệnh bác sĩ hoặc tự động đổi thuốc trong phác đồ điều trị.<br>• AI không được tự động xuất kho, lập lệnh hủy thuốc hoặc kích hoạt lệnh điều chuyển trên HIS/ERP khi chưa có chữ ký số/phê duyệt của Dược sĩ trưởng và Kế toán trưởng (Bắt buộc Human-in-the-loop - HITL).<br>• AI không được tự động gửi email trực tiếp đến các Trưởng khoa hoặc đối tác bên ngoài mà không qua giao diện review của Dược sĩ. |

---

## 3.3. Future-State Flow & AI Fit

* **Đánh giá AI-Fit:** Chọn mô hình **Rule Engine + LLM Feature (Hybrid Architecture)**:
  * *Vì sao không dùng Pure LLM?* Việc tính toán số ngày còn lại, tốc độ tiêu thụ, số lượng tồn dư cần độ chính xác số học tuyệt đối 100% (Deterministic), LLM rất dễ bị ảo giác (hallucination) khi làm phép tính số lớn trên bảng dữ liệu. Rule/Data Analytics sẽ đảm nhiệm việc này.
  * *Vì sao không dùng Pure Rule-based?* Rule thông thường chỉ xuất ra bảng số thô (raw table). Dược sĩ vẫn phải tốn hàng giờ để viết email thuyết phục các Trưởng khoa Lâm sàng, giải thích lý do cần ưu tiên kê đơn, hoặc soạn văn bản điều chuyển liên viện theo đúng văn phong y khoa chuẩn mực. LLM Feature giải quyết triệt để khâu "giao tiếp & tổng hợp ngôn ngữ tự nhiên".
  * *Vì sao không dùng Autonomous Agent?* Môi trường bệnh viện và ngành y tế đòi hỏi tuân thủ nghiêm ngặt chuẩn mực JCI và pháp lý y tế. Hệ thống không cho phép Agent tự trị ra quyết định điều chuyển thuốc hay thay đổi luồng xuất nhập kho.

* **Quy trình tương lai (Future-State):**

```text
┌─────────────────┐       ┌─────────────────┐       ┌─────────────────┐       ┌─────────────────┐
│ Bước 1          │       │ Bước 2          │       │ Bước 3          │       │ Bước 4          │
│ Ingest dữ liệu  │       │ 🔵 Rule Engine  │       │ 🔵 LLM Feature  │       │ 🟢 HITL Review  │
│ tự động từ      │ ───→  │ tính Burn-Rate, │ ───→  │ Tóm tắt rủi ro  │ ───→  │ Dược sĩ kho     │
│ HIS/ERP định kỳ │       │ gắn cờ FEFO     │       │ & Draft Email / │       │ kiểm tra, nhấn  │
│ (API Scheduled) │       │ (Critical/Warn) │       │ Biên bản chuyển │       │ "Duyệt & Gửi"   │
└─────────────────┘       └─────────────────┘       └─────────────────┘       └─────────────────┘
                                                                                       │
                                                                                       ▼
                                                                                ┌─────────────────┐
                                                                                │ Bước 5          │
                                                                                │ Hệ thống tự     │
                                                                                │ động gửi email  │
                                                                                │ & đẩy phiếu lên │
                                                                                │ HIS chờ KT ký   │
                                                                                └─────────────────┘
                                                                                       │
                                                                                       ▼
                                                                                ↩️ Fallback:
                                                                                Nếu LLM lỗi hoặc
                                                                                dữ liệu bất thường,
                                                                                hệ thống rơi về file
                                                                                báo cáo CSV phân
                                                                                loại cảnh báo FEFO
                                                                                để dược sĩ tự xử lý.
```

---

# 🏁 Phase 5 — EVALUATE (Đánh giá & Quyết định)

### AI Readiness Checklist:
1. **[x] Dữ liệu mẫu/logs sạch để test:** Dữ liệu tồn kho dược phẩm trên hệ thống HIS/ERP của Vinmec đã được chuẩn hóa theo chuẩn quốc tế (Mã hoạt chất, Tên thương mại, Số lô, Ngày sản xuất, Hạn dùng EXP, Số lượng tồn, Đơn giá, Lịch sử xuất kho từng ngày).
2. **[x] Rủi ro khi AI sai nằm trong tầm kiểm soát:** 
   - Phần tính toán số học do Rule Engine đảm bảo chính xác 100%.
   - Phần văn bản do LLM sinh ra bắt buộc gắn tiền tố `[DRAFT_ONLY]`, Dược sĩ kho bắt buộc phải đọc và nhấn nút xác nhận trước khi phát hành.
   - Cơ chế Fallback sẵn sàng tự động trả về bảng tính Excel phân loại màu theo quy tắc truyền thống nếu dịch vụ AI gặp sự cố.
3. **[x] Stakeholders sẵn sàng thay đổi quy trình:** 
   - Dược sĩ quản lý kho rất mong muốn loại bỏ tác vụ lọc Excel tẻ nhạt, giảm nguy cơ bị kỷ luật do sót thuốc hết hạn.
   - Kế toán trưởng và Ban Giám đốc bệnh viện ủng hộ vì trực tiếp cắt giảm chi phí thất thoát thuốc tiêu hủy và tối ưu dòng tiền lưu động giữa các cơ sở Vinmec.

---

### Quyết định cuối cùng của Ban Giám Đốc Vin Smart Future:
* **Quyết định:** **[x] GO (Bắt đầu xây dựng Prototype)**

### Justification (Lý giải quyết định dựa trên bằng chứng kỹ thuật và chi phí):
1. **Giá trị kinh tế và ROI rõ ràng:** 
   - Chi phí phát triển Prototype (Rule Engine + LLM Feature bằng Gemini API) rất tiết kiệm, chi phí gọi API ước tính dưới 5 USD/tháng cho mỗi bệnh viện (xử lý ~5.000 dòng dữ liệu/tuần).
   - Giá trị mang lại: Giảm thiểu từ 300 triệu đến 1 tỷ đồng tiền thuốc hủy mỗi năm trên một bệnh viện trong hệ thống Vinmec, hoàn vốn (Payback period) ngay trong tháng đầu tiên vận hành.
2. **Khả năng kiểm soát rủi ro y tế cao:** Thiết kế kiến trúc phân tách rõ ràng giữa *Tính toán (Rule Engine)* và *Soạn thảo (LLM)*, kết hợp chốt chặn con người (HITL) giúp loại trừ hoàn toàn nguy cơ sai sót y khoa hoặc vi phạm quy chế dược bệnh viện.
3. **Tiềm năng nhân rộng (Scalability):** Sau khi pilot thành công tại Vinmec Times City hoặc Vinmec Central Park, giải pháp có thể dễ dàng đóng gói và triển khai đồng loạt cho toàn bộ hệ thống bệnh viện và phòng khám Vinmec trên cả nước.


# 🏁 Phase 5 — EVALUATE (Đánh giá & Quyết định)

### AI Readiness Checklist:
1. **[x] Dữ liệu mẫu/logs sạch để test:** Dữ liệu tồn kho dược phẩm trên hệ thống HIS/ERP của Vinmec đã được chuẩn hóa theo chuẩn quốc tế (Mã hoạt chất, Tên thương mại, Số lô, Ngày sản xuất, Hạn dùng EXP, Số lượng tồn, Đơn giá, Lịch sử xuất kho từng ngày).
2. **[x] Rủi ro khi AI sai nằm trong tầm kiểm soát:** 
   - Phần tính toán số học do Rule Engine đảm bảo chính xác 100%.
   - Phần văn bản do LLM sinh ra bắt buộc gắn tiền tố `[DRAFT_ONLY]`, Dược sĩ kho bắt buộc phải đọc và nhấn nút xác nhận trước khi phát hành.
   - Cơ chế Fallback sẵn sàng tự động trả về bảng tính Excel phân loại màu theo quy tắc truyền thống nếu dịch vụ AI gặp sự cố.
3. **[x] Stakeholders sẵn sàng thay đổi quy trình:** 
   - Dược sĩ quản lý kho rất mong muốn loại bỏ tác vụ lọc Excel tẻ nhạt, giảm nguy cơ bị kỷ luật do sót thuốc hết hạn.
   - Kế toán trưởng và Ban Giám đốc bệnh viện ủng hộ vì trực tiếp cắt giảm chi phí thất thoát thuốc tiêu hủy và tối ưu dòng tiền lưu động giữa các cơ sở Vinmec.

---

### Quyết định cuối cùng của Ban Giám Đốc Vin Smart Future:
* **Quyết định:** **[x] GO (Bắt đầu xây dựng Prototype)**

### Justification (Lý giải quyết định dựa trên bằng chứng kỹ thuật và chi phí):
1. **Giá trị kinh tế và ROI rõ ràng:** 
   - Chi phí phát triển Prototype (Rule Engine + LLM Feature bằng Gemini API) rất tiết kiệm, chi phí gọi API ước tính dưới 5 USD/tháng cho mỗi bệnh viện (xử lý ~5.000 dòng dữ liệu/tuần).
   - Giá trị mang lại: Giảm thiểu từ 300 triệu đến 1 tỷ đồng tiền thuốc hủy mỗi năm trên một bệnh viện trong hệ thống Vinmec, hoàn vốn (Payback period) ngay trong tháng đầu tiên vận hành.
2. **Khả năng kiểm soát rủi ro y tế cao:** Thiết kế kiến trúc phân tách rõ ràng giữa *Tính toán (Rule Engine)* và *Soạn thảo (LLM)*, kết hợp chốt chặn con người (HITL) giúp loại trừ hoàn toàn nguy cơ sai sót y khoa hoặc vi phạm quy chế dược bệnh viện.
3. **Tiềm năng nhân rộng (Scalability):** Sau khi pilot thành công tại Vinmec Times City hoặc Vinmec Central Park, giải pháp có thể dễ dàng đóng gói và triển khai đồng loạt cho toàn bộ hệ thống bệnh viện và phòng khám Vinmec trên cả nước.