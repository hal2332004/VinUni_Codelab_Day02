# 🔍 01-problem-scan.md — Quét Cơ Hội & Đánh Giá Nhanh Bài Toán AI
**Dự án:** Vin Smart Future — AI Product Scoping  
**Người thực hiện:** AI Product Engineer  
**Đơn vị mục tiêu:** **GSM (Xanh SM)** & **Vinhomes** *(Phối hợp cùng V-GREEN & VinFast)*

---

# 🔍 Phase 1 — SCAN: Tìm kiếm cơ hội vận hành

Dưới đây là danh sách **5 bài toán nghiệp vụ vận hành thực tế** được quét qua **4 Lenses** theo yêu cầu:

| # | Subsidiary | Lens | Mô tả ngắn bài toán |
|---|------------|------|---------------------|
| 1 | **Xanh SM** | Tốn thời gian & Rò rỉ | **Lập lịch sạc & Điều hướng chống tắc nghẽn trạm sạc:** Tối ưu hóa điều phối xe đến các trạm sạc V-GREEN khả dụng, giải quyết tình trạng dồn ứ cục bộ tại các trạm TTTM/Hub lớn trong khi các trạm lân cận còn trống cổng. |
| 2 | **Xanh SM** | Pain từ người khác & Khẩn cấp | **Điều phối xử lý sự cố cạn pin và lỗi trụ sạc theo thời gian thực:** Tiếp nhận và điều phối giải cứu nhanh khi xe hết pin giữa đường hoặc cắm sạc nhưng trụ sạc V-GREEN báo lỗi (lỗi giao tiếp OCPP/mất mạng), giảm thời gian xe nằm bãi chờ cứu hộ. |
| 3 | **Xanh SM** | Lặp lại & Pain từ người khác | **Xử lý khiếu nại cước phí / thu nhập của tài xế:** Tiếp nhận, đối soát dữ liệu hành trình GPS và giải quyết các khiếu nại của tài xế về khấu trừ cước, thưởng incentive, tính sai cước do lỗi định vị hoặc tắc đường. |
| 4 | **Vinhomes** | Lặp lại & AI-upgrade | **Phân loại và điều phối yêu cầu hỗ trợ cư dân:** Tự động đọc hiểu, gắn nhãn phân loại và định tuyến các phản ánh/khiếu nại từ cư dân trên App Vinhomes Resident đến đúng ban quản lý/bộ phận kỹ thuật từng tòa nhà. |
| 5 | **Xanh SM** | Lặp lại & Tốn thời gian | **Tự động hóa quy trình đối soát phiên sạc xe điện qua AI Agent:** So khớp, đối chiếu dữ liệu hàng trăm nghìn phiên sạc hằng ngày giữa IoT gateway trụ sạc V-GREEN, ví điện tử/tài khoản tài xế GSM và hóa đơn điện lực EVN. |

---

### 📌 Chi tiết hiện trạng bài toán trọng tâm (Focus Problem #1):
* **Tên bài toán:** Lập lịch sạc & Điều hướng chống tắc nghẽn trạm sạc (Smart EV Fleet Charging Scheduling).
* **Quy trình thủ công hiện tại:**
  Tài xế tự theo dõi dung lượng pin (SOC) trên táp-lô. Khi pin xuống dưới 20%, tài xế tự do tìm trạm sạc theo thói quen (ưu tiên trạm lớn tại TTTM, chung cư, hub lớn vì có quán ăn, bóng râm).
* **Điểm nghẽn & Rò rỉ hiệu suất:**
  * Hiện tượng "hiệu ứng đám đông" khiến các trạm trung tâm quá tải, tài xế xếp hàng 30–45 phút mới đến lượt, trong khi các trạm sạc công cộng lân cận lại trống cổng sạc.
  * Xe bị "chết thời gian" (idle downtime), lãng phí hàng nghìn giờ lăn bánh mỗi ngày trong khung giờ có nhu cầu di chuyển cao.
* **Thống kê tổn thất ước tính:**
  * Mỗi xe mất trung bình **35 – 45 phút chờ đợi vô ích/ngày** do lệch pha trạm sạc.
  * Với đội xe quy mô hàng chục nghìn chiếc, thiệt hại tương đương hàng chục tỷ đồng doanh thu cơ hội bị mất/năm.
  * Sạc nhanh DC khi pin đang quá nhiệt làm pin suy hao nhanh hơn **10% – 15%** chu kỳ nạp xả.
* **Cơ hội ứng dụng AI:**
  Ứng dụng **Rule-based Dynamic Pricing / Incentive** kết hợp **Predictive Queuing & Multi-Agent Routing** kết nối dữ liệu BMS và tình trạng cổng sạc V-GREEN để chủ động điều hướng xe đến trạm tối ưu trước khi cạn pin.

---

# 🃏 Phase 2 — QUICK-ASSESS: 3 Quick Problem Cards (Vấn đề 1, 2 và 5)

---

### 🃏 QUICK PROBLEM CARD #1: Lập lịch sạc & Điều hướng chống tắc nghẽn trạm sạc

```text
┌─────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                                   │
│                                                                         │
│ Bài toán (1 câu): Tối ưu hóa điều hướng và lập lịch sạc cho đội xe taxi │
│ điện để xóa bỏ hiện tượng quá tải cục bộ tại các trạm sạc V-GREEN.      │
│ Công ty thành viên: [ ] VinFast  [x] Xanh SM  [ ] Vinhomes              │
│                     [ ] Vinmec   [ ] Khác (Ghi rõ)________              │
│                                                                         │
│ Ai đang đau (Actor)? Tài xế Xanh SM (chôn chân chờ sạc, mất thu nhập),   │
│ Khách hàng (thiếu xe phục vụ), V-GREEN (quá tải cục bộ công suất lưới). │
│                                                                         │
│ Workflow thủ công hiện tại (5 bước):                                    │
│   1. Pin xe chạm ngưỡng cảnh báo (SOC < 20%)                            │
│   ──> 2. Tài xế tự lái theo thói quen đến trạm TTTM/Hub lớn gần nhất   │
│   ──> 3. Đến nơi phát hiện kín cổng, phải xếp hàng chờ (30 - 45 phút)   │
│   ──> 4. Cắm sạc nhanh DC lúc pin đang quá nhiệt, ngồi chờ sạc          │
│   ──> 5. Rút sạc, quay lại bật app tìm khách đón xe                     │
│                                                                         │
│ Bước nào tốn thời gian/lỗi nhất? Bước 3 (Xếp hàng chờ sạc: ⏱ 35 phút/lượt)│
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2 & Bước 3                    │
│ (Dự báo hàng đợi theo thời gian thực + Điều hướng phân luồng chủ động)  │
│                                                                         │
│ Đo thành công bằng gì (Metric có số)?                                   │
│   "Giảm thời gian chờ sạc bình quân từ 35 phút ──> dưới 10 phút/lượt;    │
│    giải phóng thêm 12.000 giờ lăn bánh phục vụ khách mỗi ngày."         │
│                                                                         │
│ Quick Architecture: [x] Rule (MVP Phase)  [x] Agent (Joint-Optimization)│
└─────────────────────────────────────────────────────────────────────────┘
```

---

### 🃏 QUICK PROBLEM CARD #2: Điều phối xử lý sự cố cạn pin và lỗi trụ sạc theo thời gian thực

```text
┌─────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                                   │
│                                                                         │
│ Bài toán (1 câu): Tự động tiếp nhận, chẩn đoán lỗi trụ sạc/cạn pin và   │
│ điều phối xe cứu hộ pin hoặc định tuyến trạm thay thế trong thời gian   │
│ thực cho tài xế Xanh SM.                                                │
│ Công ty thành viên: [ ] VinFast  [x] Xanh SM  [ ] Vinhomes              │
│                     [ ] Vinmec   [x] Khác: V-GREEN                      │
│                                                                         │
│ Ai đang đau (Actor)? Tài xế Xanh SM (xe chết máy giữa đường/không sạc   │
│ được), Điều phối viên khẩn cấp (quá tải cuộc gọi), Đội cứu hộ pin.      │
│                                                                         │
│ Workflow thủ công hiện tại (5 bước):                                    │
│   1. Xe cạn pin giữa đường hoặc cắm sạc nhưng trụ báo lỗi (OCPP error)  │
│   ──> 2. Tài xế gọi hotline khẩn cấp cho tổng đài điều vận GSM          │
│   ──> 3. Điều phối viên hỏi vị trí, mở dashboard tra cứu thủ công       │
│   ──> 4. Gọi điện thoại điều xe cứu hộ pin lưu động hoặc hướng dẫn trạm │
│   ──> 5. Cập nhật trạng thái thủ công vào file theo dõi sự cố           │
│                                                                         │
│ Bước nào tốn thời gian/lỗi nhất? Bước 3 & 4 (⏱ 15–25 phút/ca xử lý)     │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2, 3 & 4                     │
│ (Tự động chẩn đoán mã lỗi trụ/pin qua IoT + Tự động điều xe cứu hộ gần  │
│  nhất hoặc reroute sang cổng sạc khả dụng ngay lập tức)                 │
│                                                                         │
│ Đo thành công bằng gì (Metric có số)?                                   │
│   "Giảm thời gian phản hồi điều phối sự cố từ 20 phút ──> dưới 2 phút;  │
│    rút ngắn thời gian xe nằm đường chờ cứu hộ từ 60 phút ──> dưới 25 phút"│
│                                                                         │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [x] Agent             │
└─────────────────────────────────────────────────────────────────────────┘
```

---

### 🃏 QUICK PROBLEM CARD #5: Tự động hóa quy trình đối soát phiên sạc xe điện qua AI Agent

```text
┌─────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #5                                                   │
│                                                                         │
│ Bài toán (1 câu): Tự động hóa đối soát và phát hiện bất thường các      │
│ phiên sạc xe điện giữa Gateway trụ sạc V-GREEN, Ví tài xế GSM và EVN.   │
│ Công ty thành viên: [ ] VinFast  [x] Xanh SM  [ ] Vinhomes              │
│                     [ ] Vinmec   [x] Khác: V-GREEN                      │
│                                                                         │
│ Ai đang đau (Actor)? Chuyên viên Kế toán - Đối soát GSM & V-GREEN       │
│ (ngập trong hàng trăm nghìn dòng dữ liệu CSV, áp lực kỳ chốt sổ).      │
│                                                                         │
│ Workflow thủ công hiện tại (4 bước):                                    │
│   1. Trích xuất file log CSV phiên sạc từ IoT gateway trạm sạc V-GREEN  │
│   ──> 2. Tải sao kê trừ tiền từ tài khoản/ví tài xế GSM và hóa đơn EVN  │
│   ──> 3. Dùng Excel/VLOOKUP thủ công so khớp từng Session ID, số kWh    │
│   ──> 4. Thủ công tìm nguyên nhân các ca lệch (rớt mạng, ngắt sạc sớm)  │
│          và soạn biên bản giải trình                                    │
│                                                                         │
│ Bước nào tốn thời gian/lỗi nhất? Bước 3 & 4 (⏱ 120–160 giờ làm việc/tháng)│
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3 & Bước 4                   │
│ (AI Agent tự động map dữ liệu đa nguồn, phân loại nguyên nhân lệch dữ   │
│  liệu và tự động dự thảo biên bản giải trình đối soát)                  │
│                                                                         │
│ Đo thành công bằng gì (Metric có số)?                                   │
│   "Giảm thời gian đối soát định kỳ từ 120 giờ ──> dưới 6 giờ/tháng;     │
│    giảm tỷ lệ sai lệch thất thoát dữ liệu sạc từ 2.0% ──> dưới 0.05%"   │
│                                                                         │
│ Quick Architecture: [ ] No AI  [ ] Rule  [ ] LLM  [x] Agent             │
└─────────────────────────────────────────────────────────────────────────┘