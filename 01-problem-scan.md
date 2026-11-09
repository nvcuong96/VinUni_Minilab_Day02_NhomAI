# 01 — Problem Scan & Quick Cards — Vin Smart Future

> **Bài làm cá nhân — Phase 1 (SCAN) & Phase 2 (QUICK-ASSESS).**
>
> * **Thành viên thực hiện:** Phạm Vinh — branch `dev_vinh`
> * **Mảng kinh doanh đề xuất:** **Xanh SM (Green SM) — Hỗ trợ khách hàng sau chuyến đi.**
> * *Số liệu quy mô doanh nghiệp có nguồn `[n]` ở cuối file; số phút/lượt là ước tính của tôi, cần xác minh khi khảo sát thực tế.*

---

## 🏛️ Bối cảnh: Tôi là ai?

Tôi là **Vinh**, AI Engineer tại **Vin Smart Future**, thành viên của nhóm được giao tìm cơ hội tối ưu vận hành bằng AI cho các công ty thành viên Vingroup.

Khi tìm hiểu quy trình chăm sóc khách hàng của **Xanh SM** — nền tảng đang chạy khoảng **1 triệu chuyến/ngày** `[1]` — tôi nhận thấy một việc nhỏ nhưng lặp lại rất nhiều: **khách quên đồ trên xe**. Mỗi yêu cầu, khách phải gọi hotline, cung cấp giờ đi, điểm đón/đến, biển số và mô tả món đồ; tổng đài viên kiểm tra với tài xế rồi mới gọi lại khách `[2][3]`. Với quy mô chuyến đi như vậy, chỉ một tỉ lệ rất nhỏ khách quên đồ cũng tạo ra hàng trăm cuộc gọi mỗi ngày. Bài toán tôi mang vào buổi Lab hôm nay đến từ quan sát này.

---

# 🔍 Phase 1 — SCAN: Tìm kiếm cơ hội (Cá nhân)

Dùng **4 Lenses** quét qua vận hành của các công ty thành viên Vingroup.

| # | Subsidiary | Lens | Mô tả ngắn bài toán |
|---|------------|------|---------------------|
| 1 | **Xanh SM** | Pain từ người khác | Khách quên đồ trên xe phải gọi hotline, tổng đài viên tra chuyến và gọi tài xế thủ công (mất ~15 phút/yêu cầu); đồ quan trọng như hộ chiếu, thuốc, laptop không được ưu tiên. |
| 2 | **VinFast (V-Green)** | Lặp lại | Điều phối viên đọc và phân loại thủ công báo hỏng trụ sạc do người dùng gửi bằng lời tự do (*"cắm vào không nhận"*), ~6 phút/báo cáo; kỹ thuật viên hay mang sai đồ nghề phải quay lại. Mạng lưới >150.000 cổng sạc `[4]`. |
| 3 | **Vinhomes** | Tốn thời gian | Kỹ sư bàn giao nhà ghi tay + chụp ảnh lỗi (nứt, thấm, sơn bong…), sau đó nhập lại Excel và phân lỗi cho từng nhà thầu, ~45 phút/căn. Năm 2025 Vinhomes bàn giao >34.000 căn `[5]`. |
| 4 | **Vinmec** | Tốn thời gian | Nhân viên đọc quyền lợi hợp đồng bảo hiểm và điều kiện thời gian chờ để làm bảo lãnh viện phí; bệnh nhân chờ 15–45 phút tại quầy tùy công ty bảo hiểm `[6]`. |
| 5 | **Vinpearl** | Pain từ người khác | Yêu cầu của khách (đổi phòng, gối thêm, đón sân bay) ghi trong sổ giấy/nhóm chat, dễ bị sót khi lễ tân và buồng phòng đổi ca (~20 phút bàn giao/ca). Hệ thống bán ~2,3 triệu đêm phòng/năm `[7]`. |
| 6 | **VinFast** | AI có thể tốt hơn | Tổng đài viên lật sổ tay PDF để trả lời các câu hỏi lặp lại về cách dùng tính năng xe (*"cài giới hạn sạc 80% ở đâu"*), ~6 phút/cuộc gọi. Năm 2025 có 175.099 xe mới bàn giao, nhiều khách lần đầu dùng xe điện `[4]`. |

---

# 🃏 Phase 2 — QUICK-ASSESS: 3 Quick Problem Cards (Cá nhân)

Chọn top 3 từ danh sách SCAN: **#1 (Xanh SM Đồ thất lạc), #3 (Vinhomes Lỗi bàn giao), #6 (VinFast Hỏi đáp tính năng).**

## Card #1 — Xanh SM Tìm & trả đồ thất lạc trên xe

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                       │
│                                                             │
│ Bài toán: Khách quên đồ trên xe Xanh SM cần được kết nối    │
│ nhanh với đúng tài xế để nhận lại đồ.                       │
│ Công ty thành viên: [x] Xanh SM (GSM)                       │
│                                                             │
│ Ai đang đau? Khách hàng (mất đồ, chờ lâu), Tổng đài viên    │
│ (quá tải), Tài xế (bị gọi nhiều lần, mất giờ chạy xe)       │
│                                                             │
│ Workflow thủ công hiện tại (5 bước):                        │
│   1. Khách gọi hotline / chat báo quên đồ                   │
│   → 2. Tổng đài viên hỏi lại giờ đi, điểm đón/đến, biển số  │
│   → 3. Tra cứu thủ công chuyến đi trên hệ thống             │
│   → 4. Gọi tài xế, chờ tài xế dừng xe kiểm tra              │
│   → 5. Gọi lại khách, hẹn giờ và điểm trả đồ                │
│                                                             │
│ Bước nào tốn nhất? Bước 2-4 (⏱ 12 phút/lượt)                │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2-4              │
│ (AI trích xuất thông tin từ lời khách -> Rule ghép chuyến   │
│  -> AI soạn nháp tin nhắn in-app gửi tài xế)                │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                        │
│ Giảm thời gian xử lý của tổng đài viên từ 15 phút ──> dưới  │
│ 5 phút/yêu cầu; tài xế nhận thông tin trong < 15 phút.      │
│                                                             │
│ Quick Architecture: [x] Rule (ghép chuyến) + LLM Feature    │
│ (trích xuất mô tả & soạn tin nhắn)                          │
└─────────────────────────────────────────────────────────────┘
```

## Card #2 — Vinhomes Số hóa danh sách lỗi khi bàn giao nhà

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                       │
│                                                             │
│ Bài toán: Ghi chú + ảnh lỗi khi bàn giao căn hộ cần được    │
│ chuẩn hóa và chuyển đúng nhà thầu chịu trách nhiệm.         │
│ Công ty thành viên: [x] Vinhomes                            │
│                                                             │
│ Ai đang đau? Kỹ sư bàn giao (nhập liệu nhiều), Cư dân       │
│ (chờ sửa lỗi), Nhà thầu (nhận việc sai hạng mục)            │
│                                                             │
│ Workflow thủ công hiện tại (4 bước):                        │
│   1. Kỹ sư + cư dân kiểm từng phòng, ghi tay, chụp ảnh      │
│   → 2. Nhập lại vào Excel, chuẩn hóa tên lỗi                │
│   → 3. Tra bảng trách nhiệm, phân lỗi cho từng nhà thầu     │
│   → 4. Gửi nhà thầu, theo dõi đến khi sửa xong              │
│                                                             │
│ Bước nào tốn nhất? Bước 2-3 (⏱ 30 phút/căn)                 │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2                │
│ (AI đọc ảnh + ghi chú -> mục lỗi chuẩn: vị trí, hạng mục,   │
│  mức độ); Bước 3 dùng Rule tra bảng hạng mục -> nhà thầu    │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                        │
│ Giảm thời gian lập biên bản từ 45 phút ──> dưới 15 phút/căn;│
│ phân đúng hạng mục ≥ 85% (kỹ sư sửa phần còn lại).          │
│                                                             │
│ Quick Architecture: [x] LLM Feature (đa phương thức) + Rule │
└─────────────────────────────────────────────────────────────┘
```

## Card #3 — VinFast Trợ lý tra cứu sổ tay cho tổng đài viên

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                       │
│                                                             │
│ Bài toán: Tổng đài viên cần trả lời nhanh và đúng các câu   │
│ hỏi "cách dùng tính năng xe" theo sổ tay chính thức.        │
│ Công ty thành viên: [x] VinFast                             │
│                                                             │
│ Ai đang đau? Tổng đài viên CSKH, Chủ xe điện lần đầu        │
│                                                             │
│ Workflow thủ công hiện tại (4 bước):                        │
│   1. Khách gọi hỏi cách dùng một tính năng                  │
│   → 2. Tổng đài viên xác định dòng xe + phiên bản phần mềm  │
│   → 3. Lật sổ tay PDF / hỏi đồng nghiệp tìm câu trả lời     │
│   → 4. Hướng dẫn khách từng bước                            │
│                                                             │
│ Bước nào tốn nhất? Bước 3 (⏱ 4 phút/cuộc gọi)               │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3                │
│ (AI tìm trong sổ tay đúng dòng xe -> trả lời ngắn kèm trích │
│  dẫn trang để tổng đài viên kiểm tra trước khi đọc cho khách)│
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                        │
│ Giảm thời lượng cuộc gọi từ 6 phút ──> dưới 3 phút; trả lời │
│ đúng & có trích dẫn ≥ 90% trên bộ 100 câu hỏi mẫu.          │
│                                                             │
│ Quick Architecture: [x] LLM Feature (tra cứu sổ tay - RAG)  │
└─────────────────────────────────────────────────────────────┘
```

---

# 🗳️ Đề xuất lựa chọn của tôi (gửi nhóm thảo luận):
Tôi đề xuất chọn bài toán **"Card #1 — Xanh SM Tìm & trả đồ thất lạc trên xe"** để nhóm thực hiện Deep-Dive.

## Lý do lựa chọn và loại bỏ các thẻ khác:
* **Card #1 (Xanh SM Đồ thất lạc) — Chọn:** Quy mô rất lớn (~1 triệu chuyến/ngày `[1]`) và đây là vấn đề chung của mọi nền tảng gọi xe — riêng Uber ghi nhận hơn 1 triệu điện thoại bị bỏ quên trong một năm `[8]`. Quy trình hiện tại đã rõ ràng nên dễ vẽ workflow. Giải pháp trung thực: phần ghép chuyến dùng **Rule** (tra lịch sử chuyến), LLM chỉ làm phần ngôn ngữ; nếu AI sai thì chỉ làm chậm, không gây nguy hiểm.
* **Card #2 (Vinhomes Lỗi bàn giao):** Giá trị lớn, nhưng biên bản bàn giao gắn với hợp đồng và trách nhiệm nhà thầu — sai sót có thể dẫn đến tranh chấp. Cần có danh mục lỗi chuẩn và dữ liệu biên bản cũ trước, nên để giai đoạn sau.
* **Card #3 (VinFast Hỏi đáp tính năng):** Khả thi, nhưng chưa có số liệu về tỉ lệ cuộc gọi hỏi "cách dùng"; nếu trả lời sai về phanh, ADAS hay pin cao áp thì ảnh hưởng an toàn — cần xác lập baseline và ranh giới chặt hơn trước khi làm.
* **Các bài toán không được chọn làm card:** #2 V-Green có thể giải quyết phần lớn bằng **Rule** vì trụ sạc thông minh tự gửi mã lỗi; #4 Vinmec đã có quy trình 15–45 phút và sai sót ảnh hưởng trực tiếp tiền của bệnh nhân; #5 Vinpearl chưa có dữ liệu số (sổ giấy/nhóm chat) — cần số hóa trước khi nghĩ đến AI.

---

## 📚 Nguồn số liệu
1. Dân Việt — "Xanh SM cán mốc 1 triệu chuyến/ngày". https://danviet.vn/xanh-sm-can-moc-1-trieu-chuyen-ngay-huong-toi-clb-100-trieu-giao-dich-thang-d1353440.html
2. Xanh SM — Trung tâm giải đáp. https://www.xanhsm.com/cau-hoi-thuong-gap/
3. Điện Máy Xanh — Tổng đài Xanh SM & cách xử lý khi quên đồ. https://www.dienmayxanh.com/kinh-nghiem-hay/so-tong-dai-xanh-sm-1589581
4. Tuổi Trẻ — "VinFast lập kỷ lục bàn giao 175.099 xe ô tô điện tại Việt Nam năm 2025" (400 xưởng, >150.000 cổng sạc). https://tuoitre.vn/khoahocphothong/vinfast-lap-ky-luc-ban-giao-175-099-xe-o-to-dien-tai-viet-nam-nam-2025-104263734.htm
5. Người Đô Thị — "Vinhomes khép lại năm 2025 với doanh thu, lợi nhuận cao kỷ lục" (>34.000 căn bàn giao). https://nguoidothi.net.vn/vinhomes-khep-lai-nam-2025-voi-doanh-thu-loi-nhuan-cao-ky-luc-51563.html
6. Vinmec — Hướng dẫn thủ tục thanh toán bảo hiểm. https://www.vinmec.com/vie/bai-viet/huong-dan-thu-tuc-thanh-toan-bao-hiem-tai-vinmec-vi
7. MekongAsean — "Vinpearl hoàn thành 111% kế hoạch doanh thu năm 2025" (~2,3 triệu đêm phòng). https://mekongasean.vn/vinpearl-hoan-thanh-111-ke-hoach-doanh-thu-nam-2025-51486.html
8. Uber — The 10th Annual Uber Lost & Found Index (2026). https://www.uber.com/us/en/newsroom/the-2026-uber-lost-found-index/
