# 📝 Nhật Ký Tương Tác & Phản Ánh Đồng Hành Cùng AI (AI Reflection Log)

**Người thực hiện:** Kỹ sư AI — Dự án Vin Smart Future (Phối hợp cùng Khối Vận hành Dược bệnh viện Vinmec)  
**Bài toán lựa chọn:** Vinmec Pharmacy Expiry Sentinel — Giám sát & Cảnh báo Sớm Rủi ro Thuốc Cận Date (FEFO)  
**Công cụ AI sử dụng:** Gemini 2.5 Flash, Claude 3.5 Sonnet, ChatGPT (GPT-4o)  

---

## 1. Bối cảnh & Phương châm tiếp cận

Trong khuôn khổ buổi làm việc về **AI Product Scoping (Vin Smart Future)**, tôi và nhóm không tiếp cận AI như một "chiếc đũa thần" có thể tự động hóa 100% mọi nghiệp vụ y tế, cũng không coi AI chỉ là một công cụ tìm kiếm đơn thuần. Thay vào đó, chúng tôi định vị AI như một **"Junior Product Manager & AI Solutions Architect"** đồng hành trong vai trò phản biện, phân tích quy trình và thử nghiệm ranh giới an toàn.

Việc ứng dụng AI trong môi trường y tế — cụ thể là Hệ thống Y tế Vinmec — đòi hỏi mức độ chuẩn xác và tuân thủ đạo đức/pháp lý cao nhất (tiêu chuẩn JCI, quy chế Dược bệnh viện và an toàn tính mạng người bệnh). Mọi sai sót logic, hallucination hoặc vượt quyền của AI đều có thể dẫn đến hậu quả nghiêm trọng. Nhật ký này ghi lại trung thực toàn bộ hành trình tương tác, những điểm AI làm xuất sắc, những cú "ngã ngựa" vì ảo giác, và cách nhóm đã tinh chỉnh prompt/ranh giới an toàn để đạt được giải pháp tối ưu.

---

## 2. AI đã hỗ trợ đắc lực những gì? (The Value Delivered)

Trong suốt 5 giai đoạn từ **SCAN**, **QUICK-ASSESS** đến **DEEP-DIVE**, AI đã đóng vai trò trợ lực mạnh mẽ ở 4 khía cạnh:

1. **Gợi mở góc nhìn qua 4 Lenses (Tốn thời gian, Lặp lại, AI-upgrade, Pain từ người khác):**  
   Khi khởi đầu quét các công ty thành viên Vingroup, AI đã giúp nhóm nhanh chóng nhận diện điểm nghẽn tại kho dược Vinmec: Dược sĩ lâm sàng bị quá tải vì phải ngồi rà soát bảng tính Excel tồn kho hàng nghìn dòng vào mỗi đầu tuần. Tác vụ này vừa tốn thời gian (3–4 giờ/tuần), vừa lặp lại, lại tiềm ẩn rủi ro rất cao nếu để sót thuốc quá hạn.
2. **Cấu trúc hóa quy trình vận hành (Workflow Structuring):**  
   AI đã hỗ trợ phác thảo sơ đồ vận hành hiện tại thành 5 bước tuần tự rõ ràng, bóc tách chính xác **thời gian xử lý**, **đầu vào (Inputs)**, **đầu ra (Outputs)**, xác định đúng điểm nghẽn cổ chai (🔴 **Bottleneck** tại Bước 2, 3 và 4) và điểm chuyển giao (🔄 **Handoffs** sang Khoa Lâm sàng và Kế toán trưởng).
3. **Định lượng hóa chỉ số thành công (Success Metrics):**  
   Thay vì các phát biểu chung chung kiểu "giúp quản lý thuốc tốt hơn", AI đã gợi ý nhóm đặt ra các chỉ số đo lường sắc bén:
   * Phát hiện **100%** lô thuốc có nguy cơ tiêu hủy trước 90 ngày (Zero missed risk).
   * Cắt giảm **90%** thời gian lập báo cáo tuần (từ 270 phút xuống dưới 15 phút).
   * Giảm **30% – 50%** giá trị thuốc hao hụt phải tiêu hủy hàng năm.
4. **Phân tích Kiến trúc AI-Fit tối ưu (Hybrid Architecture):**  
   AI giúp nhóm nhận thức rõ rằng: **Không phải cái gì cũng cần ném vào LLM**. Việc tính toán số ngày hết hạn (EXP) và tốc độ tiêu thụ (Burn-rate) phải được trao cho Rule Engine/Data Analytics để đảm bảo tính xác định tuyệt đối (Deterministic 100%), trong khi LLM chỉ nên đảm nhận khâu xử lý ngôn ngữ tự nhiên (NLP) để tóm tắt rủi ro và soạn thảo văn bản nháp.

---

## 3. Những lỗi sai, ảo giác (Hallucination) & Đề xuất ngây thơ của AI

Quá trình làm việc không hề suôn sẻ ngay từ đầu. Khi chưa được thiết lập ranh giới vận hành chặt chẽ, các mô hình ngôn ngữ lớn đã bộc lộ những điểm yếu chết người:

### ❌ Lỗi 1: Ảo giác số học và phân tích ngày tháng (Mathematical Hallucination)
* **Hiện tượng:** Khi nhóm thử đưa vào bảng dữ liệu mẫu gồm 10 mã thuốc, số lượng tồn kho và hạn dùng EXP, sau đó yêu cầu LLM *"Hãy tính xem lô nào sẽ bị tiêu hủy và số lượng thừa bao nhiêu"*:
* **Hậu quả:** Mô hình tự nhầm lẫn giữa định dạng ngày tháng `DD/MM/YYYY` và `MM/DD/YYYY`, tính sai số ngày còn lại (Days-to-Expiry). Nguy hại hơn, khi tính `Số lượng tiêu thụ dự kiến = Tốc độ tiêu thụ/ngày × Số ngày còn lại`, mô hình đã làm phép nhân sai lệch hàng trăm đơn vị nhưng vẫn đưa ra kết luận rất tự tin!
* **Bài học rút ra:** **Tuyệt đối không để LLM làm tính toán số học hoặc dự báo tồn kho định lượng.**

### ❌ Lỗi 2: Xâm phạm nghiêm trọng ranh giới an toàn y tế (Safety Boundary Breach)
* **Hiện tượng:** Trong một lượt prompt mở rộng: *"Làm thế nào để đẩy nhanh tốc độ tiêu thụ thuốc cận date nhằm tránh tiêu hủy?"*, AI đã đưa ra một đề xuất vô cùng ngây thơ và nguy hiểm:
  > *"Hệ thống AI có thể tự động thay thế (Auto-substitute) các thuốc cận date này vào đơn thuốc của bác sĩ khi có chỉ định hoạt chất tương đương, hoặc tự động gửi thông báo yêu cầu bác sĩ bắt buộc phải kê loại thuốc này."*
* **Phân tích tính chất nguy hiểm:** Đây là hành vi vi phạm y đức và pháp luật y tế nghiêm trọng. Quyền chỉ định phác đồ điều trị thuộc về Bác sĩ dựa trên tình trạng cụ thể của bệnh nhân; Dược sĩ chỉ có quyền khuyến nghị và tư vấn. AI không bao giờ được phép tự ý sửa y lệnh hay ép buộc bác sĩ kê đơn để giải phóng tồn kho.

### ❌ Lỗi 3: Ảo tưởng quyền tự trị (Over-agentic Assumption)
* **Hiện tượng:** AI tự đề xuất tạo một "Autonomous Agent" có khả năng tự động gọi API hệ thống HIS/ERP để tạo phiếu xuất kho và kích hoạt lệnh vận chuyển thuốc từ Vinmec Hà Nội vào Vinmec TP.HCM.
* **Thực tế vận hành:** Quy trình luân chuyển thuốc giữa các pháp nhân bệnh viện liên quan mật thiết đến hóa đơn tài chính, đối soát kho và kiểm soát chất lượng bảo quản (GSP). Mọi lệnh điều chuyển bắt buộc phải có sự phê duyệt và ký số của **Dược sĩ trưởng** và **Kế toán trưởng bệnh viện**. AI không thể tự tung tự tác làm thay con người.

---

## 4. Cách điều chỉnh Prompt & Thiết lập Ranh giới An toàn (Guardrails)

Để khắc phục hoàn toàn các lỗi trên và đưa hệ thống vào quỹ đạo vận hành an toàn chuẩn mực, nhóm đã thực hiện 4 bước tái cấu trúc prompt:

### 1. Nguyên tắc phân tách trách nhiệm (Separation of Concerns)
Nhóm tước bỏ toàn bộ tác vụ tính toán số học ra khỏi phạm vi của LLM:
* **Rule Engine (Python/SQL):** Chịu trách nhiệm quét cơ sở dữ liệu, trừ ngày tháng (`EXP - Today`), tính burn-rate trung bình 30/60/90 ngày, xác định số lượng dôi dư và gắn nhãn rủi ro (`CRITICAL_RISK`, `WARNING`, `SAFE`).
* **LLM Feature:** Chỉ nhận dữ liệu đầu vào là bảng tóm tắt đã được tính toán sẵn và chỉ tập trung vào thế mạnh cốt lõi: **Diễn đạt văn phong y tế chuyên nghiệp, tổng hợp báo cáo điều hành và soạn thảo email nháp.**

### 2. Cưỡng chế thẻ nhận diện bắt buộc `[DRAFT_ONLY]`
Để ngăn chặn việc hệ thống gửi email tự động khi chưa có sự kiểm soát của con người, nhóm đưa vào **Hard Constraint** trong System Prompt:
```text
QUY TẮC BẮT BUỘC SỐ 1:
Mọi phản hồi và nội dung văn bản email/báo cáo do bạn tạo ra PHẢI LUÔN LUÔN bắt đầu 
bằng thẻ tiền tố: [DRAFT_ONLY - CHỜ DƯỢC SĨ DUYỆT].
Tuyệt đối không được phát ngôn với tư cách là người ra y lệnh hay người phê duyệt cuối cùng.
```

### 3. Thiết lập bộ Ranh giới cấm tuyệt đối (Negative Boundaries)
Trong System Prompt của `prompt_prototype.py`, nhóm bổ sung danh sách ranh giới đỏ không thể phá vỡ:
* 🛑 **CẤM:** Không được đề xuất thay đổi đơn thuốc điều trị của bệnh nhân.
* 🛑 **CẤM:** Không được tự ý đưa ra kết luận hủy thuốc hoặc xuất kho điều chuyển mà không qua quy trình ký duyệt của Dược sĩ và Kế toán trưởng.
* 🛑 **CẤM:** Không được tự ý bịa đặt (hallucinate) tên thuốc, số đăng ký hoặc số lô ngoài dữ liệu được cung cấp trong context.

### 4. Kỹ thuật Structured Output & Few-Shot Prompting
Nhóm cung cấp mẫu văn phong chuẩn y khoa để LLM học theo:
* Email gửi Trưởng khoa Lâm sàng không được mang tính ép buộc, mà phải là thông báo hỗ trợ chuyên môn:  
  * *"Kính gửi Bác sĩ Trưởng khoa [Tên khoa], Kho Dược xin thông báo hiện có lô thuốc [Tên thuốc] - Hạn dùng [EXP] còn tồn [Số lượng]. Kính đề nghị Bác sĩ và quý đồng nghiệp trong khoa lưu ý ưu tiên chỉ định cho các bệnh nhân có phác đồ phù hợp theo nguyên tắc FEFO nhằm tối ưu sử dụng thuốc..."*
* Định dạng JSON trả về có cấu trúc rõ ràng:
  ```json
  {
    "summary_tag": "[DRAFT_ONLY - CHỜ DƯỢC SĨ DUYỆT]",
    "urgent_drugs_count": 3,
    "recommended_actions": [
      {
        "drug_name": "Augmentin 1g",
        "lot_number": "BT202408",
        "days_to_expiry": 65,
        "action_type": "clinical_priority_email",
        "target_department": "Khoa Hô hấp & Nội tổng hợp"
      }
    ]
  }
  ```

---

## 5. Đúc kết tư duy AI Product Scoping từ buổi học

Trải nghiệm thực chiến qua buổi Lab định hình sản phẩm AI mang lại cho tôi 3 bài học đắt giá:

1. **"Tư duy Scoping" quan trọng hơn "Kỹ thuật Prompting":**  
   Một prompt dù viết hay đến đâu cũng trở nên vô nghĩa nếu giải bài toán sai ngay từ đầu hoặc chọn nhầm mức độ AI-Fit. Nhận ra sự cần thiết của mô hình lai (Rule + LLM) thay vì mù quáng chạy theo Pure LLM chính là bước ngoặt quyết định tính khả thi của dự án.
2. **Human-in-the-Loop (HITL) không phải là sự thỏa hiệp, mà là tiêu chuẩn bắt buộc:**  
   Trong các lĩnh vực rủi ro cao như y tế (Vinmec), giao thông (Xanh SM) hay sản xuất công nghiệp (VinFast), AI chỉ nên đóng vai trò là "Copilot" tăng tốc năng suất cho chuyên gia con người, không bao giờ được phép thay thế quyền quyết định và trách nhiệm pháp lý của con người.
3. **Phải luôn "Stress-Test" với Adversarial Prompts:**  
   Chỉ khi chủ động đóng vai kẻ tấn công, đưa ra các prompt cám dỗ AI vượt quyền (như yêu cầu tự ý hủy thuốc, bỏ qua bước nháp), ta mới biết chắc chắn liệu ranh giới an toàn mình dựng lên có đủ vững vàng trước áp lực vận hành thực tế hay không.

