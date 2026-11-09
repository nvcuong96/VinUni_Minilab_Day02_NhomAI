# Lab 02 — Problem Deep-Dive: Vinmec Pharmacy Expiry Sentinel

- **Tên nhóm:** NhómAI
- **Người thực hiện bài cá nhân:** Nguyễn Nhật Minh
- **Email:**

**Thành viên tham gia:**

| Họ và tên | Email |
|---|---|
| Nguyễn Nhật Minh | |
| | |

> **Phân biệt bằng chứng:** Chủ đề do nhóm lựa chọn. Thời gian, chi phí, mục tiêu, quy trình và kịch bản tài chính bên dưới là **giả định/mục tiêu để lập kế hoạch**, chưa phải số liệu vận hành được Vinmec xác nhận. Chưa có log HIS/ERP, khảo sát nhân sự, kết quả prototype Vinmec hay xác nhận của dược sĩ và các bên liên quan. Phần lập trình cá nhân thực hiện bài mẫu Xanh SM theo yêu cầu lớp đã được người làm bài xác nhận; kết quả code này không phải bằng chứng cho KPI Vinmec.

## 1. Bài toán nhóm lựa chọn

**Vinmec Pharmacy Expiry Sentinel** hỗ trợ nhân sự kho dược lập báo cáo các lô thuốc sắp hết hạn, để dược sĩ có thể kiểm tra và xử lý kịp thời theo quy trình của cơ sở.

**Phạm vi đề xuất:** Một kho dược và một báo cáo định kỳ hằng tuần, sử dụng dữ liệu lô thuốc giả lập. Ngưỡng cảnh báo **0–90 ngày trước hạn** là giả định cho prototype, cần dược sĩ xác nhận trước khi áp dụng. Thuốc đã quá hạn hoặc lô thiếu/sai dữ liệu được liệt kê riêng để người phụ trách kiểm tra; không tự xác định là an toàn hay đủ điều kiện sử dụng.

WHO mô tả nguyên tắc **FEFO** (lô hết hạn trước được ưu tiên xuất trước), việc ghi số lô và hạn dùng, cảnh báo và kiểm kê trong quản lý tồn kho. Nguồn này dùng làm tham khảo nghiệp vụ chung, **không chứng minh quy trình hay số liệu tại Vinmec**. [WHO — Standard operating procedures, mục 7](https://iris.who.int/bitstream/handle/10665/360835/9789240049581-eng.pdf?sequence=1).

## Phase 3 — DEEP-DIVE

### 3.1. Current-State Workflow Mapping — quy trình hiện tại giả định

Nhóm giả định việc lập báo cáo mất **4 giờ/tuần**. Phân bổ thời gian cho từng bước dưới đây chỉ là mô hình minh họa cộng thành 240 phút, **chưa quan sát hay đo thực tế**.

| Bước | Người/hệ thống | Công việc và đầu ra | Thời gian giả định | Điểm chuyển giao / bottleneck |
|---|---|---|---:|---|
| 1 | Nhân viên kho dược; hệ thống tồn kho | Xuất danh sách tồn theo lô và hạn dùng cho kỳ báo cáo | 30 phút | **Handoff:** hệ thống → nhân viên; chưa xác minh nguồn hay quyền truy cập |
| 2 | Nhân viên kho dược | Rà soát số lô, hạn dùng, số lượng; đánh dấu dữ liệu thiếu/mâu thuẫn | 60 phút | **Bottleneck:** đối chiếu thủ công, có thể phát sinh sai lệch |
| 3 | Nhân viên kho dược | Lọc các lô theo ngưỡng hạn, tổng hợp danh sách cần kiểm tra | 90 phút | **Bottleneck:** lọc và rà soát nhiều dòng dữ liệu |
| 4 | Nhân viên kho dược | Lập bảng báo cáo, ghi chú các lô cần xác minh | 45 phút | **Handoff:** người lập báo cáo → dược sĩ phụ trách |
| 5 | Dược sĩ phụ trách | Kiểm tra và duyệt báo cáo; chuyển cho bên liên quan sau khi duyệt | 15 phút đến mốc duyệt | **Handoff sau mốc đo:** dược sĩ → người nhận báo cáo |
| **Tổng** | | | **240 phút = 4 giờ/tuần** | Hai bước 2–3 giả định chiếm 150 phút |

Sơ đồ minh họa quy trình này nằm ở `04-workflow-diagram.png`. Cần xác minh từng bước, người phụ trách, công cụ, thời gian và điểm chuyển giao với nhóm vận hành trước khi coi là hiện trạng thực tế.

### 3.2. Problem Statement (6-field) & Metrics

| Trường | Nội dung |
|---|---|
| **1. Actor / Operator** | Nhân viên kho dược lập báo cáo; dược sĩ phụ trách kiểm tra, duyệt và quyết định xử lý. Các vai trò cụ thể cần xác minh với cơ sở. |
| **2. Current Workflow** | Theo giả định: xuất dữ liệu theo lô, đối chiếu hạn và số lượng, lọc lô cần kiểm tra, lập và duyệt báo cáo hằng tuần; giả định tổng 4 giờ/tuần. Hệ thống nguồn và quyền truy cập chưa xác nhận. |
| **3. Bottleneck** | Giả định bước đối chiếu dữ liệu và lọc lô tốn 150/240 phút, dễ phụ thuộc vào chất lượng dữ liệu và thao tác thủ công. Cần xác minh bằng quan sát hoặc log. |
| **4. Business Impact** | Giả định 208 giờ/năm cho việc lập báo cáo; thuốc gần hạn có thể cần được xử lý kịp thời. Giá trị thuốc **có nguy cơ** và giá trị thuốc **thực sự bị tiêu hủy** là hai đại lượng khác nhau; baseline hao hụt chưa có. |
| **5. Success Metric** | Mục tiêu báo cáo 4 giờ xuống 15 phút/tuần, gồm người duyệt; phát hiện ≥99% lô thuộc tiêu chí trong bộ kiểm thử có nhãn; mục tiêu giảm thời gian xử lý cảnh báo ≥70% và giảm giá trị tiêu hủy 30–50%, chỉ đánh giá khi có baseline và dữ liệu phù hợp. |
| **6. Operational Boundary** | Quy tắc tính hạn, lọc và cảnh báo dựa trên dữ liệu được xác minh; LLM nếu dùng chỉ tóm tắt từ kết quả đã tính. Dược sĩ duyệt và quyết định. Không tự cấp phát, điều chuyển, sửa tồn kho, hủy thuốc hoặc đưa ra chỉ định điều trị. Thiếu/sai dữ liệu thì cảnh báo và chuyển người kiểm tra. |

#### KPI và kế hoạch đo

Tất cả chỉ tiêu là **mục tiêu đề xuất, chưa đạt được hay được kiểm chứng**.

| KPI | Định nghĩa / phạm vi đo | Baseline và mục tiêu |
|---|---|---|
| Thời gian lập báo cáo tuần | Từ khi bắt đầu chuẩn bị dữ liệu đến khi dược sĩ duyệt báo cáo; tính cả thời gian người kiểm tra và sửa | Giả định 4 giờ/tuần → mục tiêu 15 phút/tuần, giảm 93,75% |
| Tỷ lệ phát hiện lô gần hạn | Trên bộ kiểm thử có đáp án độc lập: số lô tồn dương có hạn hợp lệ từ 0 đến 90 ngày được đưa vào danh sách / tổng số lô đủ tiêu chí trong tập tham chiếu | Mục tiêu ≥99%; phạm vi tính theo dữ liệu đã kiểm chứng, không phải cam kết cho toàn bộ kho thực tế |
| Dữ liệu thiếu/sai | Thống kê riêng lô thiếu hạn, số lô, số lượng, dữ liệu trùng hoặc không khớp. Phải đánh dấu để kiểm tra; không âm thầm loại khỏi báo cáo | Chưa có baseline; mục tiêu prototype hiển thị và chuyển các lỗi dữ liệu đã nhận diện cho người phụ trách |
| Thời gian xử lý cảnh báo | Đo từ lúc một cảnh báo được tạo đến lúc dược sĩ ghi nhận quyết định xử lý; không tính thời gian vận chuyển/hủy thực tế | Mục tiêu giảm ≥70%; **chưa có baseline**, cần đo cả trước và sau |
| Giá trị thuốc tiêu hủy do hết hạn | Tổng giá trị thuốc thực tế phải tiêu hủy do hết hạn trong cùng phạm vi kho và kỳ đo; định nghĩa giá trị và nguyên nhân cần thống nhất với tài chính/dược | Mục tiêu giảm 30–50% so với baseline thực tế; **baseline chưa có**, cần thời gian theo dõi và kiểm soát yếu tố gây nhiễu |

Tỷ lệ phát hiện lô gần hạn không tự chứng minh hệ thống dự đoán đúng mọi lô sẽ bị tiêu hủy. Cần đo thêm báo động sai, độ đầy đủ và độ cập nhật của nguồn dữ liệu; dược sĩ xác nhận định nghĩa, mốc thời gian và ngưỡng cảnh báo.

#### Business case — tính toán giả định

**Thời gian và giá trị nhân sự**

Giả sử có 52 báo cáo/năm, thời gian 4 giờ và 15 phút đều tính cho cùng phạm vi công việc, gồm bước duyệt:

| Phép tính | Kết quả giả định |
|---|---:|
| Trước: 4 giờ × 52 tuần | 208 giờ/năm |
| Sau: 0,25 giờ × 52 tuần | 13 giờ/năm |
| Thời gian giải phóng: 208 − 13 | **195 giờ/năm** |
| Tỷ lệ giảm: 195 / 208 | **93,75%** |
| Quy đổi: 195 / 8 giờ/ngày | **24,375 ngày công**, khoảng 24,4 ngày |
| Nếu chi phí nhân sự 100.000 đồng/giờ: 195 × 100.000 | **19,5 triệu đồng/năm** giá trị thời gian nhân sự |

Đây là giá trị năng lực được giải phóng theo giả định, **không tự động là tiền mặt tiết kiệm được**. Cần đo thời gian thực, số tuần phát sinh và xác định liệu chi phí nhân sự có thực sự giảm hoặc năng lực được dùng vào việc khác.

**Giá trị thuốc tiêu hủy và lợi ích ròng**

Gọi **W** là giá trị thuốc thực tế bị tiêu hủy do hết hạn mỗi năm trong phạm vi khảo sát. Nếu mức giảm thực tế là **r** từ 30% đến 50%, phần tránh tiêu hủy là **W × r**.

**Lợi ích gộp minh họa = 19,5 triệu đồng + W × r.** Lợi ích ròng ước tính bằng lợi ích gộp trừ chi phí triển khai, vận hành và các khoản liên quan; tránh tính trùng và chỉ cộng các khoản được xác nhận có thể quy đổi thành tiền.

Ví dụ: **nếu giả định thêm W = 500 triệu đồng/năm là giá trị thuốc thực tế bị tiêu hủy** thì phần tránh tiêu hủy là 150–250 triệu đồng, và tổng lợi ích gộp minh họa là 169,5–269,5 triệu đồng/năm trước chi phí. Con số **500 triệu đồng thuốc có nguy cơ hết hạn** do nhóm nêu chưa cho biết bao nhiêu thực sự sẽ bị tiêu hủy; vì vậy không đủ cơ sở để kết luận khoản tiết kiệm 150–250 triệu hoặc tổng lợi ích trên. Cần đo W và mức giảm thực tế.

### 3.3. Future-State Flow & AI Fit

**AI Fit — so sánh giải pháp**

| Phương án | Vai trò | Nhận xét / lựa chọn |
|---|---|---|
| **Rule / State-Machine** | Kiểm tra dữ liệu bắt buộc, tính số ngày đến hạn theo ngày chốt, phân nhóm lô và tạo danh sách; tách quá hạn hoặc thiếu dữ liệu | **Ưu tiên cho phần phát hiện** vì có tiêu chí rõ ràng, có thể kiểm thử và đối chiếu với dữ liệu nguồn. Ngưỡng 90 ngày chỉ là giả định cần dược sĩ duyệt. |
| **LLM Feature** | Tùy chọn: tóm tắt danh sách đã được rule tính hoặc soạn bản nháp báo cáo | Chỉ dùng nếu so sánh cho thấy có lợi ích hơn mẫu báo cáo cố định. Không cho LLM tính hạn hay tự bịa lô, số lượng hoặc kết luận nghiệp vụ. |
| **Agentic Loop** | Tự gọi công cụ, quyết định và thực hiện chuỗi hành động | Chưa cần cho prototype; quyết định về thuốc và thao tác hệ thống cần người có thẩm quyền kiểm tra. |

**Đề xuất:** Làm rule-based trước; có thể thử LLM để soạn nháp nhưng không giả định AI luôn cần thiết. Prototype giới hạn ở dữ liệu giả lập, không kết nối kho thật và không thực hiện thay đổi tồn kho.

**Future-State Flow — quy trình đề xuất**

```text
Nhân viên chọn dữ liệu lô giả lập và ngày chốt
  → Rule kiểm tra số lô, hạn dùng, tồn; tách thiếu/sai/trùng
  → Rule phân loại: quá hạn / 0–90 ngày / ngoài ngưỡng
  → Lập danh sách và báo cáo nháp từ dữ liệu hợp lệ
  → [AI tùy chọn] LLM tóm tắt dữ liệu đã tính; không tự thêm số
  → [HITL] Dược sĩ kiểm tra dữ liệu, hạn, số lượng và duyệt
  → Chuyển báo cáo đã duyệt theo quy trình của cơ sở

Fallback: dữ liệu thiếu/mâu thuẫn, hệ thống hoặc LLM lỗi
  → đánh dấu vấn đề → dược sĩ kiểm tra và lập/xử lý thủ công.
```

**Ranh giới:** Không cấp phát, điều chuyển, đặt mua, tiêu hủy hoặc sửa tồn kho tự động; không tự gửi báo cáo ra ngoài hoặc thay đổi hạn dùng. Không dựa vào kết quả LLM để xác định thuốc có được sử dụng hay không. Bất kỳ quyết định và thao tác đối với thuốc đều thuộc người có thẩm quyền theo quy trình của cơ sở.

## Phase 5 — EVALUATE

### AI Readiness Checklist

- [ ] Có sẵn dữ liệu mẫu/log sạch để test.
- [ ] Rủi ro khi AI sai đã được kiểm soát bằng HITL hoặc fallback và được kiểm thử.
- [ ] Stakeholder sẵn sàng thay đổi quy trình cũ.

| Câu hỏi | Trạng thái hiện tại | Việc cần xác minh |
|---|---|---|
| Có dữ liệu mẫu/log sạch để test? | **Chưa xác nhận** | Chuẩn bị dữ liệu giả lập có đáp án; nếu dùng dữ liệu thật phải được cấp quyền và kiểm tra chất lượng/phạm vi. |
| Rủi ro khi AI sai có trong tầm kiểm soát? | **Mới đề xuất** | Thiết kế và kiểm thử rule, cảnh báo dữ liệu thiếu, HITL và fallback; chưa có kết quả test. |
| Stakeholder sẵn sàng đổi quy trình? | **Chưa xác nhận** | Trao đổi với dược sĩ, kho dược, CNTT và các bên liên quan về workflow và quyền hạn. |

### Quyết định và Justification

- [ ] **GO:** Bắt đầu xây dựng prototype LLM trong phạm vi hẹp.
- [x] **NOT YET:** Cần chuẩn bị dữ liệu và xác lập baseline trước khi đầu tư prototype LLM cho Vinmec.
- [ ] **NO-GO:** Không tiếp tục giải pháp AI, hoặc đã có bằng chứng rule-based tốt hơn.

**Lý giải:** Nhóm chưa có tập dữ liệu lô thuốc để đánh giá, chưa đo được baseline, chưa so sánh rule với LLM và chưa xác nhận stakeholder. Vì vậy chọn **NOT YET**, hoãn prototype LLM Vinmec để chuẩn bị dữ liệu, tiêu chí đánh giá và ước tính chi phí. Các ngưỡng 15 phút, ≥99% và 30–50% mới là mục tiêu. Chưa có bằng chứng để khẳng định GO hay NO-GO cho phần LLM. Bài thực hành code Xanh SM vẫn được thực hiện riêng theo yêu cầu lớp và không thay thế bước kiểm chứng này.

### Điều kiện xem xét lại quyết định

1. Dược sĩ/kho dược xác nhận quy trình, thời gian từng bước, người phê duyệt và ngưỡng cảnh báo.
2. Xác định phạm vi kho, số báo cáo/năm, baseline thời gian, W = giá trị thuốc thực tế tiêu hủy do hết hạn và chi phí dự kiến.
3. Xác nhận danh sách thành viên, email và người đóng góp để hoàn thiện đầu file.
4. Chuẩn bị dữ liệu giả lập có đáp án để đánh giá rule trước; nếu cần LLM và đủ căn cứ thì xem xét GO cho prototype hẹp. Ghi kết quả thật khi thực hiện và cập nhật quyết định.

## Phần thực hành code cá nhân — Xanh SM

Theo phạm vi được người làm bài xác nhận, code tại `starter-code/prompt_prototype.py` thực hiện **bài mẫu Xanh SM** của slide, tách với chủ đề phân tích nhóm Vinmec. Mã cần có system instruction, đầu ra phù hợp từng trường hợp và ít nhất **3 prompt tấn công** theo Phase 4 của worksheet. Trạng thái kết quả kiểm tra tĩnh, mô phỏng và gọi API thật được ghi tại `03-ai-log.md`; không dùng kết quả mô phỏng thay cho phản hồi Gemini.
