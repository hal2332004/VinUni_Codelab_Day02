# 🔍 01-problem-scan.md — Quét Cơ Hội & Đánh Giá Nhanh Bài Toán AI
**Dự án:** Vin Smart Future — AI Product Scoping  
**Người thực hiện:** AI Product Engineer  
**Đơn vị mục tiêu:** VinFast / V-Green (Hệ thống Trạm sạc Xe điện Toàn quốc)

---

# 🔍 Phase 1 — SCAN: Tìm kiếm cơ hội vận hành

Dưới đây là bài toán nghiệp vụ vận hành trọng tâm được quét qua lăng kính **Lặp lại (Repetitive)** và **Tốn thời gian (Time-consuming)** tại mạng lưới trạm sạc VinFast / V-Green:

| # | Subsidiary | Lens | Mô tả ngắn bài toán |
|---|------------|------|---------------------|
| 1 | **VinFast / V-Green** | Lặp lại & Tốn thời gian | **So khớp & Đối soát giao dịch sạc điện giữa Trụ sạc và Đối tác thanh toán / V-Green (Charging Session Data Reconciliation):** Quy trình xử lý đối soát thủ công hàng trăm nghìn phiên sạc hằng ngày giữa log trụ sạc, hóa đơn điện EVN và cổng thanh toán. |

### 📌 Chi tiết hiện trạng bài toán:
* **Quy trình thủ công hiện tại:**
  Mạng lưới trạm sạc VinFast / V-Green phát sinh hàng trăm nghìn phiên sạc mỗi ngày trên cả nước (bao gồm trụ sạc công cộng, trạm nhượng quyền, trụ sạc tại Vinhomes). Cuối tuần/tháng, nhân viên tài chính - vận hành phải xuất file log CSV từ các gateway trụ sạc, so khớp đối chiếu với hóa đơn tiền điện từ EVN và giao dịch trừ ví/thẻ từ cổng thanh toán của khách.
* **Điểm nghẽn & Rò rỉ hiệu suất:**
  Các phiên sạc bị mất kết nối mạng giữa chừng (drop session), trụ sạc gửi log trễ hoặc chênh lệch đơn giá giờ cao điểm/thấp điểm dẫn đến việc đối soát thủ công bằng Excel cực kỳ nặng nề và dễ phát sinh sai sót.
* **Thống kê tổn thất ước tính:**
  * Tiêu tốn **120–160 giờ làm việc/tháng** của đội ngũ kế toán - đối soát.
  * Tỷ lệ sai lệch hoặc thất lạc dữ liệu phiên sạc chiếm **1.5% – 2.5%** tổng doanh thu sạc, gây chậm thu hồi hoặc thất thoát từ hàng trăm triệu đến hàng tỷ đồng mỗi tháng.
  * Chu kỳ thanh toán cho các đối tác nhượng quyền vị trí trạm bị kéo dài từ cam kết **T+3 sang T+15**, làm giảm uy tín và làm chậm tốc độ mở rộng mạng lưới trạm nhượng quyền.
* **Cơ hội ứng dụng AI:**
  Trợ lý **AI Agentic Reconciliation** tự động so khớp bất thường (Anomaly Detection) giữa nhiều nguồn dữ liệu log không đồng nhất và tự động dự thảo biên bản giải trình các phiên sạc bị lệch cho kiểm toán viên phê duyệt.

---

# 🃏 Phase 2 — QUICK-ASSESS: Quick Problem Card

```text
┌─────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                                   │
│                                                                         │
│ Bài toán (1 câu): Tự động hóa đối soát và phát hiện bất thường các      │
│ phiên sạc điện giữa Gateway trụ sạc, Cổng thanh toán và Hóa đơn EVN.    │
│                                                                         │
│ Công ty thành viên: [x] VinFast / V-Green     [ ] Xanh SM               │
│                     [ ] Vinhomes              [ ] Vinmec                │
│                                                                         │
│ Ai đang đau (Actor)?                                                    │
│ Chuyên viên Tài chính - Đối soát & Quản lý vận hành mạng lưới trạm sạc. │
│                                                                         │
│ Workflow thủ công hiện tại (4 bước):                                    │
│   1. Xuất file log CSV phiên sạc từ hệ thống IoT gateway trụ sạc        │
│   ──> 2. Tải báo cáo sao kê từ Cổng thanh toán và Hóa đơn EVN           │
│   ──> 3. Dùng Excel/VLOOKUP thủ công so khớp từng phiên sạc (Session ID)│
│   ──> 4. Truy tìm nguyên nhân & soạn biên bản giải trình các case lệch  │
│                                                                         │
│ Bước nào tốn thời gian/lỗi nhất? Bước 3 & 4 (⏱ 120–160 giờ/tháng)       │
│                                                                         │
│ AI có thể nhảy vào hỗ trợ ở bước nào?                                   │
│ Hỗ trợ Bước 3 & Bước 4: AI Agent tự động map dữ liệu đa nguồn, phát hiện│
│ pattern mất mạng/drop session, tự động draft biên bản giải trình lệch.  │
│                                                                         │
│ Đo thành công bằng gì (Metric có số)?                                   │
│ • Giảm thời gian đối soát từ 120–160h ──> dưới 10h/tháng (giảm >90%).   │
│ • Giảm tỷ lệ thất thoát/sai lệch doanh thu từ 2.5% ──> dưới 0.1%.       │
│ • Rút ngắn chu kỳ thanh toán đối tác nhượng quyền từ T+15 ──> T+2.      │
│                                                                         │
│ Quick Architecture: [x] Agent (Agentic Reconciliation Loop)             │
└─────────────────────────────────────────────────────────────────────────┘
```
https://docs.google.com/document/d/1a_dd0E1KRvR_VyFRHrA8jzqdmKtGG_riKna6-n7di5E/edit?hl=vi&tab=t.0