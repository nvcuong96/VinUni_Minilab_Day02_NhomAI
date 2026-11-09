# Quét vấn đề vận hành

Các tình huống và chỉ số dưới đây là giả thuyết để thiết kế bài thử. Nhóm cần xác minh quy trình và đo thời gian thực tế trước khi dùng chúng để đánh giá hiệu quả.

| # | Công ty | Góc nhìn | Vấn đề cần xác minh |
|---|---|---|---|
| 1 | VinFast | Vướng mắc của khách hàng | Khách cần sạc gấp, nhân viên phải đối chiếu cổng, trụ trống và khoảng cách theo tuyến trước khi hướng dẫn. |
| 2 | VinFast | Lặp lại | Nhân viên tài chính phải so khớp từng phiên sạc với dòng hóa đơn đối tác. |
| 3 | VinFast | AI có thể cải thiện | Mô tả lỗi bằng tiếng Việt thường thiếu cấu trúc, khiến bộ phận tiếp nhận mất công hỏi lại trước khi chuyển kỹ thuật. |
| 4 | Vinhomes | Vướng mắc của người vận hành | Phản ánh của cư dân có thể được chuyển nhầm bộ phận khi nội dung đề cập nhiều sự cố. |
| 5 | Vinpearl | Tốn thời gian | Nhân viên đặt phòng phải tách ngày, số khách và yêu cầu phòng từ email đặt đoàn trước khi kiểm tra tồn. |

## Phase 2 — QUICK-ASSESS

```text
┌──────────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                                        │
│                                                                              │
│ Bài toán (1 câu): Rút ngắn thời gian tìm trạm sạc phù hợp cho khách VinFast. │
│ Công ty thành viên: [x] VinFast  [ ] Xanh SM  [ ] Vinhomes                   │
│ [ ] Vinmec   [ ] Khác (Ghi rõ): Không                                        │
│                                                                              │
│ Ai đang đau (Actor)? Nhân viên hỗ trợ khách hàng VinFast.                    │
│                                                                              │
│ Workflow thủ công hiện tại (4 bước, giả định):                               │
│ 1. Nhận yêu cầu → 2. Xác minh xe, pin, vị trí và cổng sạc                    │
│ → 3. Tra cứu trạm, đối chiếu cổng và trạng thái trụ                          │
│ → 4. Gửi hướng dẫn hoặc chuyển cứu hộ.                                       │
│                                                                              │
│ Bước nào tốn thời gian/lỗi nhất? Tra cứu và đối chiếu trạm                   │
│ (5 phút/lượt, giả định).                                                     │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3–4: Rule lọc trạm                │
│ và áp dụng ngưỡng pin; LLM soạn nháp từ phương án đã xác định.               │
│ Nhân viên duyệt và xử lý ca thiếu dữ liệu.                                   │
│                                                                              │
│ Đo thành công bằng gì (Metric có số)? Duyệt trong dưới 3 phút                │
│ từ khi đủ dữ liệu; 100% trạm đề xuất trong bộ ca thử tương thích             │
│ và đã xác nhận còn trụ.                                                      │
│                                                                              │
│ Quick Architecture: [ ] No AI  [x] Rule  [x] LLM  [ ] Agent                  │
└──────────────────────────────────────────────────────────────────────────────┘
```

```text
┌──────────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                                        │
│                                                                              │
│ Bài toán (1 câu): Giảm thời gian đối chiếu hóa đơn sạc đối tác tại VinFast.  │
│ Công ty thành viên: [x] VinFast  [ ] Xanh SM  [ ] Vinhomes                   │
│ [ ] Vinmec   [ ] Khác (Ghi rõ): Không                                        │
│                                                                              │
│ Ai đang đau (Actor)? Nhân viên tài chính phụ trách đối soát.                 │
│                                                                              │
│ Workflow thủ công hiện tại (5 bước, giả định):                               │
│ 1. Nhận hóa đơn → 2. Xuất dữ liệu phiên sạc                                  │
│ → 3. Đối chiếu mã phiên, sản lượng và số tiền                                │
│ → 4. Kiểm tra các dòng lệch → 5. Lập biên bản.                               │
│                                                                              │
│ Bước nào tốn thời gian/lỗi nhất? Đối chiếu thủ công                          │
│ (30 phút/lượt 100 dòng, giả định).                                           │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3 không cần LLM:                  │
│ Rule so khớp mã phiên, đơn vị và số tiền. Nhân viên xem các dòng             │
│ thiếu mã, trùng hoặc chênh lệch ở bước 4.                                    │
│                                                                              │
│ Đo thành công bằng gì (Metric có số)? Giảm từ 30 xuống dưới                  │
│ 10 phút/100 dòng; gắn cờ đủ 10/10 dòng sai lệch cài sẵn trong bộ thử.        │
│                                                                              │
│ Quick Architecture: [ ] No AI  [x] Rule  [ ] LLM  [ ] Agent                  │
└──────────────────────────────────────────────────────────────────────────────┘
```

```text
┌──────────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                                        │
│                                                                              │
│ Bài toán (1 câu): Rút ngắn thời gian phân loại mô tả lỗi xe VinFast.         │
│ Công ty thành viên: [x] VinFast  [ ] Xanh SM  [ ] Vinhomes                   │
│ [ ] Vinmec   [ ] Khác (Ghi rõ): Không                                        │
│                                                                              │
│ Ai đang đau (Actor)? Nhân viên tiếp nhận yêu cầu dịch vụ.                    │
│                                                                              │
│ Workflow thủ công hiện tại (4 bước, giả định):                               │
│ 1. Nhận mô tả → 2. Hỏi thêm điều kiện phát sinh                              │
│ → 3. Gắn nhóm triệu chứng → 4. Chuyển kỹ thuật viên.                         │
│                                                                              │
│ Bước nào tốn thời gian/lỗi nhất? Đọc mô tả và xác định thông tin             │
│ cần hỏi lại (5 phút/lượt, giả định).                                         │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2–3: LLM trích xuất               │
│ triệu chứng và đề xuất nhóm tiếp nhận. Kỹ thuật viên xác nhận                │
│ phân loại và quyết định chẩn đoán.                                           │
│                                                                              │
│ Đo thành công bằng gì (Metric có số)? Phân loại nháp từ 5 xuống              │
│ dưới 2 phút/lượt; đạt ít nhất 90% khớp nhãn trên 30 mô tả đã được            │
│ kỹ thuật viên gắn nhãn.                                                      │
│                                                                              │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent                  │
└──────────────────────────────────────────────────────────────────────────────┘
```

## Chọn đề tài

Chọn hướng dẫn trạm sạc vì có thể kiểm thử cả lựa chọn trạm lẫn tình huống pin yếu bằng dữ liệu giả lập. Đối soát hóa đơn phù hợp với rule so khớp. Phân loại mô tả lỗi cần bộ ca có nhãn do kỹ thuật viên xác nhận.
