# 01 — Problem Scan & Quick Assessments (Vin Smart Future)

> **Người thực hiện:** sontpm — AI Product Engineer tại Vin Smart Future  
> **Dự án:** AI Product Scoping (Vin Smart Future — Vingroup)  
> **Các mảng kinh doanh tập trung:** GSM (Xanh SM) & VinFast  

---

## 🏛️ Bối cảnh cá nhân

Tôi là **sontpm**, AI Product Engineer tại **Vin Smart Future**. Nhiệm vụ của tôi trong đợt chuyển đổi số và nâng cao năng lực AI đợt này là phối hợp chặt chẽ với hai đơn vị nòng cốt trong hệ sinh thái di chuyển xanh của Vingroup: **Xanh SM (GSM)** và **VinFast**. 

Mục tiêu chính là tìm kiếm các điểm nghẽn (bottlenecks) trong vận hành hàng ngày, phân tích tổn thất hiệu suất, và đánh giá tính khả thi khi ứng dụng mô hình ngôn ngữ lớn (LLM) hoặc giải pháp tự động hóa thông minh nhằm giảm thiểu áp lực cho đội ngũ vận hành và nâng cao trải nghiệm khách hàng.

---

# 🔍 Phase 1 — SCAN: Quét cơ hội bằng 4 Lenses

Áp dụng 4 lăng kính (*Repetitive*, *Time-consuming*, *AI-upgrade*, *Stakeholder Pain*) để rà soát vận hành thực tế tại VinFast và Xanh SM, tôi xác định 5 bài toán tiềm năng sau:

| # | Đơn vị thành viên | Lăng kính (Lens) | Tên bài toán | Mô tả ngắn bài toán & Điểm nghẽn vận hành |
|:---:|:---|:---|:---|:---|
| **1** | **Xanh SM** | **Tốn thời gian** | Điều vận thông minh (Smart Dispatching) | Tối ưu hóa điểm đón taxi điện Xanh SM dựa trên phân tích ngôn ngữ tự nhiên từ tin nhắn/ghi chú của tài xế và tọa độ GPS thực tế. Hiện tại điều phối viên phải đọc tin nhắn văn bản tự do của tài xế để tra cứu thủ công vị trí thực tế, mất nhiều thời gian trong giờ cao điểm. |
| **2** | **VinFast** | **AI có thể tốt hơn** | Trợ lý hướng dẫn trạm sạc thông minh | Tự động phân tích tình trạng xe (dung lượng pin %, dòng xe VF5/VF8/VF9, chuẩn sạc CCS2/GBT) và tọa độ để đề xuất lịch trình sạc tối ưu cùng trạm sạc còn trụ trống phù hợp. Hiện tại tài xế phải tự tra bản đồ và ước lượng dung lượng pin, dễ dẫn đến rủi ro hết pin giữa đường. |
| **3** | **VinFast** | **Lặp lại** | Đối chiếu hóa đơn sạc điện đối tác | So khớp dữ liệu sạc điện hằng tuần từ hàng nghìn trụ sạc liên kết ngoài với hóa đơn thực tế gửi về hệ thống tài chính VinFast. Kế toán đang xử lý bán thủ công bằng Excel, dễ nhầm lẫn số liệu và mất 2-3 ngày làm việc mỗi kỳ chốt công nợ. |
| **4** | **Xanh SM** | **Pain từ người khác** | Phân tích lý do hủy chuyến của khách hàng | Tự động trích xuất và phân tích lý do khách hàng hủy chuyến từ bản ghi âm cuộc gọi CSKH và ghi chú ngắn của tài xế để phân loại 10 lý do phổ biến nhất gây rò rỉ cuốc xe. Hiện tại team vận hành chỉ phân tích mẫu ngẫu nhiên ~5% do không đủ nhân lực nghe lại băng ghi âm. |
| **5** | **VinFast** | **AI có thể tốt hơn** | Chẩn đoán lỗi xe từ mô tả tiếng Việt của khách | Khách hàng mô tả lỗi bằng ngôn ngữ tự nhiên đời thường (ví dụ: *"xe đi qua gờ giảm tốc kêu cụp cụp ở bánh trước"*), hệ thống tiếp nhận dịch vụ cần phân loại mã lỗi kỹ thuật sơ bộ trước khi xe vào xưởng bảo dưỡng. Nhân viên tiếp nhận hiện mất nhiều thời gian phỏng vấn lại khách hàng để mô tả đúng thuật ngữ kỹ thuật. |

---

# 🃏 Phase 2 — QUICK-ASSESS: 3 Thẻ bài toán tiềm năng nhất

Từ 5 bài toán trên, tôi chọn ra **Top 3 bài toán tiềm năng nhất (#1, #2, #4)** để xây dựng Quick Problem Cards chi tiết.

---

## 📌 Quick Problem Card #1 — Xanh SM: Điều vận thông minh (Smart Dispatching)

```text
┌─────────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                                       │
│                                                                             │
│ Bài toán (1 câu): Phân tích văn bản ghi chú/tin nhắn của tài xế để tự động  │
│ xác định chính xác điểm nghẽn đón khách và đề xuất vị trí đón tối ưu.       │
│ Công ty thành viên: [ ] VinFast  [x] Xanh SM  [ ] Vinhomes                  │
│                     [ ] Vinmec   [ ] Khác ________________                  │
│                                                                             │
│ Ai đang đau (Actor)? Tài xế Xanh SM (chờ khách lâu, khó đón) và             │
│ Điều phối viên (Dispatcher) bị quá tải tin nhắn hỗ trợ trong giờ cao điểm.  │
│                                                                             │
│ Workflow thủ công hiện tại (4 bước):                                        │
│   1. Tài xế nhắn tin/gọi tổng đài báo không tiếp cận được vị trí ghim       │
│   ──> 2. Điều phối viên đọc tin nhắn mô tả vị trí thực tế của tài xế        │
│   ──> 3. Tra cứu bản đồ nội bộ và camera giao thông để tìm lối vào thoáng   │
│   ──> 4. Soạn tin nhắn hướng dẫn tài xế di chuyển tới điểm đón thuận lợi    │
│                                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2 & 3 (⏱ 4 - 6 phút/lượt)             │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2 & 4                             │
│ (Trích xuất thực thể vị trí từ tin nhắn tự do -> Tự động draft hướng dẫn)   │
│                                                                             │
│ Đo thành công bằng gì (Metric có số)?                                       │
│   • Giảm thời gian điều phối xử lý điểm đón từ 6 phút ──> dưới 90 giây.     │
│   • Tỉ lệ tài xế tiếp cận thành công khách hàng tăng từ 82% ──> 95%.        │
│                                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM Feature  [ ] Agent         │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 📌 Quick Problem Card #2 — VinFast: Trợ lý hướng dẫn trạm sạc thông minh

```text
┌─────────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                                       │
│                                                                             │
│ Bài toán (1 câu): Đề xuất trạm sạc tối ưu và cảnh báo cứu hộ khi xe điện    │
│ có nguy cơ cạn pin dựa trên mức pin, loại cổng sạc và vị trí thời gian thực.│
│ Công ty thành viên: [x] VinFast  [ ] Xanh SM  [ ] Vinhomes                  │
│                     [ ] Vinmec   [ ] Khác ________________                  │
│                                                                             │
│ Ai đang đau (Actor)? Chủ xe/Tài xế xe điện VinFast (lo lắng cạn pin - range │
│ anxiety) và Đội ngũ hỗ trợ kỹ thuật cứu hộ pin thực địa.                    │
│                                                                             │
│ Workflow thủ công hiện tại (4 bước):                                        │
│   1. Khách hàng/Tài xế thấy pin yếu (<10%), dừng xe mở app tra cứu trạm     │
│   ──> 2. Lọc thủ công các trạm xem còn trụ trống và đúng cổng sạc không    │
│   ──> 3. Tự ước lượng khoảng cách xem dung lượng pin hiện tại có tới được    │
│   ──> 4. Nếu pin dưới 5%, gọi hotline VinFast yêu cầu xe sạc lưu động       │
│                                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2 & 3 (⏱ 7 - 10 phút/lần tự tra cứu)   │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2, 3 & 4                          │
│ (Tự động tính toán tầm hoạt động, lọc trạm phù hợp, tự động kích hoạt cứu hộ│
│ nếu pin < 5% và trạm quá xa).                                               │
│                                                                             │
│ Đo thành công bằng gì (Metric có số)?                                       │
│   • Giảm thời gian tìm và chốt trạm sạc từ 8 phút ──> dưới 1 phút.          │
│   • Giảm tỉ lệ xe cạn kiệt pin chết máy giữa đường xuống dưới 0.2%.          │
│                                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM Feature  [ ] Agent         │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 📌 Quick Problem Card #4 — Xanh SM: Phân tích lý do khách hàng hủy chuyến

```text
┌─────────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #4                                                       │
│                                                                             │
│ Bài toán (1 câu): Tự động hóa phân tích lý do hủy cuốc từ ghi âm tổng đài   │
│ và ghi chú tài xế để phát hiện nguyên nhân rò rỉ doanh thu theo thời gian.  │
│ Công ty thành viên: [ ] VinFast  [x] Xanh SM  [ ] Vinhomes                  │
│                     [ ] Vinmec   [ ] Khác ________________                  │
│                                                                             │
│ Ai đang đau (Actor)? Quản lý Vận hành (Operations Manager) Xanh SM và       │
│ Đội ngũ Đảm bảo chất lượng (QA/QC) chăm sóc khách hàng.                     │
│                                                                             │
│ Workflow thủ công hiện tại (4 bước):                                        │
│   1. Hệ thống ghi nhận sự kiện khách hàng hủy chuyến kèm mã hủy chung chung │
│   ──> 2. Chuyên viên QA tải mẫu ngẫu nhiên ~50 cuộc gọi ghi âm và ghi chú   │
│   ──> 3. Nghe lại từng cuộc gọi và ghi chép lý do cụ thể vào bảng tính Excel│
│   ──> 4. Tổng hợp báo cáo phân loại hàng tuần gửi cho ban giám đốc vận hành │
│                                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 3 (⏱ 8 - 12 phút/cuộc gọi ghi âm)    │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2 & 3                            │
│ (Speech-to-text tự động -> LLM phân loại vào 10 danh mục nguyên nhân chuẩn) │
│                                                                             │
│ Đo thành công bằng gì (Metric có số)?                                       │
│   • Tăng độ phủ phân tích từ 5% mẫu ngẫu nhiên ──> 100% toàn bộ cuốc bị hủy.│
│   • Giảm thời gian xuất báo cáo nguyên nhân từ 7 ngày (tuần) ──> theo ngày. │
│                                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM Feature  [ ] Agent         │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 💡 Đánh giá sơ bộ và Định hướng lựa chọn Deep-Dive

Sau khi cân nhắc 3 thẻ bài toán:
* **Card #4 (Phân tích hủy chuyến):** Rất hữu ích cho phân tích dữ liệu kinh doanh, tuy nhiên mang tính chất phân tích offline (back-office batch processing), giá trị tác động không diễn ra theo thời gian thực (real-time).
* **Card #1 (Điều vận thông minh Xanh SM):** Cực kỳ thực tế, giải quyết trực tiếp áp lực dispatch giờ cao điểm.
* **Card #2 (Trợ lý hướng dẫn trạm sạc VinFast):** Có tính rủi ro vận hành cao nếu sai sót (xe cạn pin gây tắc đường, rủi ro an toàn giao thông), đòi hỏi **Operational Boundary** rất nghiêm ngặt (đặc biệt là quy tắc an toàn khi pin `< 5%` bắt buộc phải gọi xe cứu hộ thay vì chỉ dẫn trạm sạc xa). Đây là bài toán mẫu mực để kiểm thử an toàn prompt và ranh giới mô hình.

