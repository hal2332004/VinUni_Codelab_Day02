# Lab 02 — Problem Scan & Quick Assessment (Vin Smart Future)

---

## 🔍 Phase 1 — SCAN (Tìm kiếm cơ hội ứng dụng AI)

Sử dụng **4 Lenses** (*Lặp lại, Tốn thời gian, AI có thể tốt hơn, Pain từ người khác*) để quét qua hoạt động vận hành của các công ty thành viên Vingroup:

| # | Subsidiary (VinFast/Xanh SM...) | Lens | Mô tả ngắn bài toán |
|---|----------------------------------|------|---------------------|
| 1 | **Xanh SM (GSM)** | **Tốn thời gian** *(Time-consuming)* | Điều phối viên phải tra cứu thủ công vị trí GPS của xe gặp sự cố pin (< 5%), tìm trụ sạc VinFast còn trống hoặc điều xe cứu hộ và nhắn tin chỉ đường (mất ~15 phút/sự cố; ~80 ca/ngày tại Hà Nội gây lãng phí ~20 giờ công/ngày). |
| 2 | **VinFast** | **AI có thể tốt hơn** *(AI-upgrade)* | Cố vấn dịch vụ tại xưởng phải đọc mô tả triệu chứng lỗi bằng ngôn ngữ đời thường của chủ xe (VD: *"xe đi qua gờ giảm tốc kêu lục cục ở bánh trước"*) để tra mã lỗi kỹ thuật ban đầu, mất 10–15 phút/xe và dễ phân bổ sai kỹ thuật viên. |
| 3 | **Vinhomes** | **Lặp lại** *(Repetitive)* | Ban quản lý nhận hàng trăm phản ánh mỗi ngày qua App Vinhomes Resident (mất nước, hỏng đèn, tiếng ồn, phí dịch vụ) và phải đọc, phân loại thủ công để chuyển cho bộ phận kỹ thuật/an ninh, khiến SLA phản hồi bị trễ 6–12 tiếng. |
| 4 | **Vinmec** | **Pain từ người khác** *(Stakeholder Pain)* | Bác sĩ nội trú mất 20–30 phút/bệnh nhân để tổng hợp bệnh án điện tử, kết quả xét nghiệm và ghi chú lâm sàng thành bản Tóm tắt hồ sơ xuất viện (Discharge Summary) dễ hiểu cho bệnh nhân, gây quá tải hành chính. |
| 5 | **Vinpearl** | **Tốn thời gian** *(Time-consuming)* | Nhân viên Sales & Reservation phải đọc các email đặt phòng đoàn (Group Booking) phức tạp từ đại lý lữ hành để bóc tách số lượng phòng, hạng phòng, ngày lưu trú và kiểm tra quỹ phòng thủ công (mất 25–30 phút/email). |

---

## 🃏 Phase 2 — QUICK-ASSESS (3 Quick Problem Cards)

Chọn **Top 3 bài toán** tiềm năng nhất từ danh sách SCAN (**#1 Xanh SM**, **#3 Vinhomes**, **#4 Vinmec**) để đánh giá nhanh:

### Card #1 — Xanh SM: Điều phối xử lý sự cố pin thực địa

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                       │
│                                                             │
│ Bài toán (1 câu): Tài xế Xanh SM báo sự cố hết pin/lỗi sạc  │
│ giữa đường cần điều phối trạm sạc gần nhất hoặc xe cứu hộ.  │
│ Công ty thành viên: [ ] VinFast  [x] Xanh SM  [ ] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác: _______________  │
│                                                             │
│ Ai đang đau (Actor)? Tài xế (chờ lâu), Điều phối viên       │
│                      (quá tải giờ cao điểm)                 │
│                                                             │
│ Workflow thủ công hiện tại (5 bước):                        │
│   1. Tài xế gọi tổng đài báo sự cố pin                      │
│   ──> 2. Điều phối viên tra tọa độ GPS xe trên bản đồ       │
│   ──> 3. Mở dashboard tìm trạm sạc VinFast còn trụ trống    │
│   ──> 4. Soạn tin nhắn chỉ đường gửi qua App tài xế         │
│   ──> 5. Gọi đội cứu hộ pin di động nếu pin < 5%            │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 3 & 4 (⏱ 10 phút/lượt)│
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3 & 4: Tự động   │
│ đối chiếu GPS + trạm trống/cứu hộ và soạn bản nháp tin nhắn │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│   "Giảm thời gian xử lý sự cố từ 15 phút ──> dưới 3 phút;   │
│    độ chính xác điều hướng trạm sạc/cứu hộ đạt >= 98%"      │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘
```

---

### Card #2 — Vinhomes: Phân loại & Điều hướng phản ánh cư dân

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                       │
│                                                             │
│ Bài toán (1 câu): Phân loại mức độ khẩn cấp và điều hướng   │
│ tự động phản ánh của cư dân trên App Vinhomes Resident.     │
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [x] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác: _______________  │
│                                                             │
│ Ai đang đau (Actor)? Nhân viên CSKH Ban quản lý & Cư dân    │
│                                                             │
│ Workflow thủ công hiện tại (4 bước):                        │
│   1. Cư dân gửi ticket phản ánh (chữ + ảnh) qua App         │
│   ──> 2. CSKH đọc từng ticket, xác định tòa nhà & loại sự cố│
│   ──> 3. Gán nhãn ưu tiên và giao việc cho Kỹ thuật/An ninh │
│   ──> 4. Soạn tin nhắn xác nhận tiếp nhận gửi lại cư dân    │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2 & 4 (⏱ 8 phút/ticket)│
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2, 3 & 4:        │
│ Tự động phân loại bộ phận, gắn mức ưu tiên và draft phản hồi│
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│   "Giảm thời gian phân loại & phản hồi đầu tiên từ 6 giờ    │
│    ──> dưới 5 phút; tỉ lệ phân đúng bộ phận đạt >= 92%"     │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘
```

---

### Card #3 — Vinmec: Trợ lý soạn thảo Tóm tắt hồ sơ xuất viện

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                       │
│                                                             │
│ Bài toán (1 câu): Hỗ trợ bác sĩ nội trú tổng hợp bệnh án    │
│ điện tử thành bản nháp Tóm tắt hồ sơ xuất viện cho bệnh nhân│
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [ ] Vinhomes  │
│                     [x] Vinmec   [ ] Khác: _______________  │
│                                                             │
│ Ai đang đau (Actor)? Bác sĩ nội trú / Điều dưỡng hành chính │
│                      & Bệnh nhân chờ làm thủ tục xuất viện  │
│                                                             │
│ Workflow thủ công hiện tại (4 bước):                        │
│   1. Bác sĩ mở hệ thống HIS xem chẩn đoán, xét nghiệm, đơn  │
│   ──> 2. Tổng hợp và gõ tay bản tóm tắt quá trình điều trị  │
│   ──> 3. Viết hướng dẫn dùng thuốc, ăn uống và lịch tái khám│
│   ──> 4. Bác sĩ điều trị chính kiểm tra và ký duyệt         │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2 & 3 (⏱ 25 phút/hồ sơ)│
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2 & 3:           │
│ Trích xuất dữ liệu từ HIS và tạo bản nháp tóm tắt dễ hiểu   │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│   "Giảm thời gian soạn hồ sơ xuất viện từ 25 phút ──> dưới  │
│    5 phút/bệnh nhân; 100% hồ sơ có bác sĩ duyệt trước khi in│
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘
```

---

## 🧪 Stress-Test & Lựa chọn bài toán Deep-Dive

Sau khi phản biện dưới góc nhìn vận hành và tài chính (CFO & Trưởng phòng Vận hành):
* **Card #2 (Vinhomes):** Các phản ánh liên quan đến phí quản lý hoặc tranh chấp cần quy trình pháp lý chặt chẽ; phần phân loại cơ bản có thể giải quyết một phần bằng Rule-based trước.
* **Card #3 (Vinmec):** Dữ liệu y tế nhạy cảm và yêu cầu tích hợp sâu với hệ thống HIS nội bộ bệnh viện nên thời gian triển khai thực tế sẽ dài hơn.
* **Quyết định chọn Card #1 (Xanh SM — Điều phối xử lý sự cố pin thực địa):** Tác động trực tiếp đến vận hành thời gian thực, dữ liệu đầu vào rõ ràng (tọa độ GPS, % pin, danh sách trạm sạc), dễ dàng kiểm soát rủi ro bằng cơ chế `[DRAFT_ONLY]` (Human-in-the-loop) và quy tắc cứng khi pin `< 5%`.

