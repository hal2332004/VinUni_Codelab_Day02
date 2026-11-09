# 02 — Báo Cáo Phân Tích Chuyên Sâu (Deep-Dive Report)
## Dự Án: Lập Lịch Sạc & Điều Hướng Chống Tắc Nghẽn Trạm Sạc (Smart EV Fleet Charging Scheduling)

> **Đơn vị thực hiện:** Nhóm AI Product Engineers — Vin Smart Future  
> **Khách hàng nội bộ:** Khối Vận Hành Xanh SM (GSM) & Mạng lưới trạm sạc V-GREEN / VinFast  
> **Tác giả:** sontpm

---

## 🏛️ 1. Bối cảnh & Quyết định Lựa chọn Bài toán

Trong đợt rà soát vận hành thực tế tại hệ sinh thái Vingroup, nhóm đã khảo sát và đánh giá 3 bài toán tiềm năng nhất tại Phase 2 (Quick-Assess):
* **Card #1 (Điều vận thông minh đón khách):** Tối ưu điểm đón, nhưng bài toán này thiên về vi mô từng chuyến xe.
* **Card #4 (Phân tích lý do hủy cuốc):** Tác vụ phân tích offline (back-office), không giải quyết điểm nóng vận hành tức thời.
* **Card #2 Mở rộng — "Lập lịch sạc & Điều hướng chống tắc nghẽn trạm sạc":** Đây là điểm nghẽn nghiêm trọng nhất của toàn bộ đội xe điện Xanh SM, tác động trực tiếp đến dòng tiền, hiệu suất phương tiện và chi phí khấu hao pin nghìn tỷ của tập đoàn.

👉 **Quyết định của nhóm:** Tập trung 100% nguồn lực thực hiện Deep-Dive vào bài toán **Smart EV Fleet Charging Scheduling** (Lập lịch sạc & Điều hướng chống tắc nghẽn trạm sạc).

---

# 🏗️ Phase 3 — DEEP-DIVE

## 3.1. Phân Tích Quy Trình Hiện Tại (Current-State Workflow)

Hiện nay, việc nạp năng lượng cho đội xe taxi điện đang hoạt động theo cơ chế **phản ứng thụ động và tự phát**:
* Tài xế chỉ tìm trạm sạc khi dung lượng pin (SOC - State of Charge) tụt xuống mức báo động (< 20%).
* Tài xế tự do chọn trạm dựa trên thói quen hoặc tiện đường (thường tập trung tại các đại siêu thị, TTTM Vincom, hub lớn).

Quy trình gồm 5 bước cụ thể:
1. **Bước 1 (Nhận biết pin yếu):** Xe cảnh báo pin thấp (< 20% SOC). Tài xế tắt app đón khách để chuyển sang chế độ tìm sạc. *(Thời gian: ~1 phút)*
2. **Bước 2 (Tra cứu & Chọn trạm cảm tính):** Tài xế mở app bản đồ trạm sạc, chọn trạm quen thuộc gần nhất theo cảm tính mà không biết trước lưu lượng xe đang đổ về. *(🔄 Handoff giữa tài xế và App; Thời gian: ~3 phút)*
3. **Bước 3 (Di chuyển đến trạm):** Tài xế lái xe đến trạm sạc đã chọn. *(Thời gian: ~10 - 15 phút)*
4. **Bước 4 (Xếp hàng chờ tại trạm — 🔴 BOTTLENECK CHÍNH):** Gặp hiện tượng "hiệu ứng đám đông", trạm quá tải, hàng chục xe xếp hàng chờ trụ sạc trống. Trong khi đó, các trạm sạc vệ tinh cách đó 2–3 km lại đang trống cổng sạc. *(Thời gian chờ lãng phí: 35 – 50 phút 🔴)*
5. **Bước 5 (Cắm sạc cưỡng bức):** Cắm sạc nhanh công suất cao khi pin đang bị quá nhiệt sau thời gian chạy liên tục và phơi nắng chờ đợi. *(Thời gian sạc: ~40 phút)*

> 📌 *Sơ đồ chi tiết quy trình hiện tại được trực quan hóa tại file:* [04-workflow-diagram.png](04-workflow-diagram.png)  
> ⏱ **Tổng thời gian chết vô ích (Idle Downtime):** Trung bình **35 – 45 phút/xe/ngày**.

---

## 3.2. Bảng Tuyên Bố Bài Toán (Problem Statement 6-Field) — Vin Smart Future Standard

| Trường thông tin (Field) | Nội dung chi tiết |
|:---|:---|
| **1. Actor / Operator** | • Tài xế đội xe taxi điện Xanh SM (GSM).<br>• Điều phối viên trung tâm điều vận (Fleet Dispatchers).<br>• Đội ngũ kỹ thuật vận hành mạng lưới trạm sạc V-GREEN. |
| **2. Current Workflow** | Vận hành bán tự động & tự phát: Tài xế tự canh dung lượng pin (SOC < 20%), tự mở app tra cứu và tự chọn trạm theo thói quen. Không có cơ chế đặt chỗ (reservation) hay điều phối cân bằng tải theo thời gian thực giữa các cụm trạm sạc. |
| **3. Bottleneck (Điểm nghẽn)** | Hiện tượng **lệch pha tải trạm sạc (Load Imbalance)** do hiệu ứng đám đông. Xe dồn ứ cục bộ tại các hub trung tâm (chờ 35–50 phút), gây tê liệt trạm sạc, trong khi các trạm lân cận (bán kính 2–3 km) thừa công suất. Pin bị cắm sạc nhanh khi nhiệt độ cell pin đang quá cao. |
| **4. Business Impact (Tổn thất đo lường)** | • **Thời gian lãng phí:** Trung bình 35 – 45 phút chờ đợi vô ích/xe/ngày.<br>• **Tổn thất công suất phục vụ:** Với quy mô 25.000 xe, lãng phí tương đương **~15.000 giờ phục vụ/ngày** (mất cơ hội thực hiện **~20.000 cuốc xe/ngày**).<br>• **Rò rỉ doanh thu:** Mất doanh thu cơ hội ước tính **60 – 90 tỷ VND/năm**.<br>• **Khấu hao tài sản:** Pin suy hao nhanh hơn **10% – 15%** chu kỳ nạp/xả do liên tục sạc nhanh cưỡng bức ở nhiệt độ pin cao. |
| **5. Success Metric (Chỉ số thành công)** | 1. **Giảm thời gian chờ đợi tại trạm:** Giảm từ 35–45 phút xuống **dưới 8 phút/lượt** (Efficiency).<br>2. **Hệ số cân bằng tải trạm sạc (Utilization Rate):** Chênh lệch tải giữa trạm hub và trạm vệ tinh giảm từ 70% xuống **dưới 15%**.<br>3. **Bảo vệ tuổi thọ pin:** 100% xe được điều hướng vào slot sạc khi pin đạt ngưỡng nhiệt độ an toàn quy chuẩn BMS. |
| **6. Operational Boundary (Ranh giới an toàn)** | • **Ngưỡng pin nguy cấp (< 5% SOC):** Tuyệt đối KHÔNG điều hướng xe đi xa quá 3 km; nếu không có trạm khả thi, tự động kích hoạt lệnh điều xe sạc pin lưu động (Mobile Charger) cứu hộ.<br>• **Quyền tự quyết của tài xế (HITL):** Hệ thống chỉ đề xuất (Recommend & Lock Slot), tài xế có 60 giây để xác nhận/từ chối trước khi slot chuyển cho xe khác.<br>• **Cam kết giữ chỗ (Slot Reservation):** Hệ thống chỉ khoá trụ sạc tối đa 15 phút theo lộ trình di chuyển của xe; nếu xe không đến đúng giờ, slot tự động mở lại để tránh lãng phí hạ tầng. |

---

## 3.3. Future-State Flow & Phân Tích Mức Độ Tương Thích AI (AI Fit)

### 🧩 Phân loại kiến trúc công nghệ (AI-Fit Matrix)
* **Tại sao không dùng Pure LLM (Chỉ dùng GenAI)?** LLM không có năng lực giải quyết bài toán tối ưu tổ hợp đa biến, không đảm bảo ràng buộc cứng (hard constraints) về công suất lưới điện, dung lượng pin và khoảng cách di chuyển.
* **Tại sao không dùng Pure Rule-based (Quy tắc cố định)?** Quy tắc tĩnh (if-else) không thể thích ứng với biến động động của giao thông thời gian thực, tốc độ tụt pin theo địa hình, và thời gian sạc thực tế của từng xe.
* **Giải pháp tối ưu:** Kiến trúc Hybrid gồm **Constrained Operations Research (MILP — Mixed Integer Linear Programming)** kết hợp **Predictive Queuing Model**, có lớp **LLM Co-pilot** giao tiếp ngôn ngữ tự nhiên thân thiện với tài xế.
  * **Hạ tầng dữ liệu:** Thu thập dữ liệu thời gian thực từ BMS (Battery Management System qua CAN-bus: SOC, nhiệt độ pack pin, SoH) và trạng thái trụ sạc V-GREEN (trụ trống, công suất khả dụng kW).
  * **Engine tối ưu:** Chủ động tính toán và phân bổ "slot sạc thông minh" trước khi xe chạm ngưỡng cạn kiệt (khi pin còn 25–30%).

### 🔄 Luồng quy trình tương lai (Future-State Flow):

```text
┌─────────────────┐       ┌─────────────────┐       ┌─────────────────┐       ┌─────────────────┐
│ Bước 1 (Xe)     │       │ Bước 2 (Engine) │       │ Bước 3 (AI UI)  │       │ Bước 4 (Tài xế) │
│ BMS CAN-bus gửi │ ────> │ 🔵 MILP Engine  │ ────> │ 🔵 LLM Co-pilot │ ────> │ 🟢 Tài xế duyệt │
│ telematics:     │       │ giải bài toán   │       │ sinh thông báo  │       │ trên App: Chấp  │
│ SOC = 25%, Temp,│       │ cân bằng tải,   │       │ [DRAFT_ONLY]    │       │ thuận giữ slot  │
│ GPS vị trí      │       │ chốt slot tối ưu│       │ lộ trình sạc    │       │ và đi theo nav  │
└─────────────────┘       └─────────────────┘       └─────────────────┘       └─────────────────┘
                                                           │                           │
                                                           ▼ (Nếu lỗi Engine / Timeout) ▼ (Nếu từ chối)
                                                    ┌───────────────────────────────────────────┐
                                                    │ ↩️ Fallback Plan:                         │
                                                    │ Quay về cơ chế hiển thị danh sách trạm sạc│
                                                    │ theo khoảng cách địa lý đơn thuần trên App│
                                                    └───────────────────────────────────────────┘
```

* 🔵 **AI Steps:**
  1. Engine MILP dự báo hàng đợi và phân bổ slot sạc tối ưu tổng thể mạng lưới.
  2. LLM format lộ trình sạc thân thiện, tự động gắn thẻ `[DRAFT_ONLY]` để kiểm soát ranh giới duyệt trước khi kích hoạt định vị.
* 🟢 **Human-in-the-loop (HITL):** Tài xế xem đề xuất (thời gian đến, ước tính chờ, trụ sạc dành riêng) và nhấn xác nhận nhận slot.
* ↩️ **Fallback:** Nếu hệ thống gặp sự cố mạng hoặc engine timeout (> 5 giây), app tự động chuyển về chế độ hiển thị danh sách trạm sạc gần nhất như hiện tại.

---

# 🏁 Phase 5 — EVALUATE: Đánh Giá Độ Sẵn Sàng & Quyết Định Đầu Tư

### 📋 AI Readiness Checklist
1. **Dữ liệu mẫu/logs sạch (Data Readiness):**  
   ✅ **ĐẠT.** Toàn bộ xe taxi điện Xanh SM đều trang bị modem IoT viễn thông truyền dữ liệu CAN-bus chuẩn về Data Lake (SOC, nhiệt độ pin, toạ độ GPS tần suất 5-10s/lần). Mạng lưới trạm V-GREEN có API quản lý trạng thái trụ sạc theo thời gian thực.
2. **Kiểm soát rủi ro (Risk Mitigation):**  
   ✅ **ĐẠT.** Ranh giới an toàn được bảo vệ chặt chẽ: Khi pin `< 5%`, hệ thống cấm điều hướng xa và tự động điều động xe cứu hộ lưu động (`dispatch_mobile_charger`). Cơ chế Fallback giúp tài xế luôn chủ động tìm trạm thủ công nếu mất kết nối.
3. **Mức độ sẵn sàng thay đổi của Stakeholders (Adoption Readiness):**  
   ✅ **ĐẠT.** Cả tài xế (muốn giảm thời gian chờ đợi để tăng thu nhập) và khối Vận hành GSM (muốn tối đa hóa số cuốc/ngày và bảo vệ tuổi thọ pin) đều hưởng lợi ích kinh tế trực tiếp và rõ ràng.

---

### 🏆 Quyết định của Hội Đồng Đánh Giá Vin Smart Future:

# ✅ [ GO ] — BẮT ĐẦU XÂY DỰNG PROTOTYPE VÀ PILOT THỬ NGHIỆM

### 📝 Lý giải quyết định (Justification):
1. **Lợi ích kinh tế vượt trội:** Giảm 30 phút chờ/xe/ngày mang lại thêm 15.000 giờ xe lăn bánh, thu hồi từ 60–90 tỷ VND doanh thu rò rỉ mỗi năm, vượt xa chi phí đầu tư hạ tầng máy chủ AI/OR.
2. **Kéo dài vòng đời tài sản cốt lõi:** Ngăn chặn việc sạc nhanh khi pin quá nhiệt giúp kéo dài tuổi thọ pack pin xe điện thêm 10–15%, tiết kiệm hàng trăm tỷ đồng chi phí thay thế pin cho GSM.
3. **Tính khả thi kỹ thuật cao:** Dữ liệu CAN-bus và API V-GREEN đã sẵn sàng; mô hình tối ưu tổ hợp MILP và LLM feature là công nghệ chín muồi, ít rủi ro ảo giác so với các mô hình Agentic tự trị hoàn toàn.

---

*Báo cáo được đính kèm cùng mã nguồn kiểm thử an toàn ranh giới tại:* [`starter-code/prompt_prototype.py`](starter-code/prompt_prototype.py)

