# 🏗️ 02-deep-dive-report.md — Báo Cáo Phân Tích Sâu Dự Án AI
**Dự án:** Vin Smart Future — AI Product Scoping  
**Nhóm thực hiện:** Nhóm AI Product Engineers (Vin Smart Future)  
**Đơn vị mục tiêu:** **GSM (Xanh SM)** — Vận hành Đội xe Taxi Điện Thông minh *(Phối hợp cùng V-GREEN & VinFast)*  
**Bài toán lựa chọn:** **Lập lịch sạc & Điều hướng chống tắc nghẽn trạm sạc (Smart EV Fleet Charging Scheduling)**

---

# 🏗️ Phase 3 — DEEP-DIVE: Phân tích chuyên sâu quy trình

## 3.1. Current-State Workflow Mapping (Quy trình hiện tại)

Hiện tại, quy trình tìm kiếm và nạp sạc của tài xế Xanh SM hoàn toàn dựa trên phán đoán cá nhân và kinh nghiệm thực địa, dẫn đến tình trạng mất cân bằng tải trầm trọng giữa các trạm sạc V-GREEN:

```text
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│     Bước 1      │     │     Bước 2      │     │     Bước 3      │     │     Bước 4      │
│  Theo dõi pin   │     │  Chọn trạm sạc  │     │  Xếp hàng chờ   │     │  Cắm sạc & Chờ  │
│  (SOC < 20%)    │ ──> │  theo thói quen │ ──> │  cổng sạc trống │ ──> │  đầy pin (80%)  │
│                 │     │                 │     │                 │     │                 │
│ Ai: Tài xế      │     │ Ai: Tài xế      │     │ Ai: Tài xế      │     │ Ai: Tài xế      │
│ ⏱ 1 phút       │     │ ⏱ 5 phút       │     │ ⏱ 35 phút 🔴   │     │ ⏱ 30 phút       │
│ In: Đồng hồ xe  │     │ In: Thói quen   │     │ In: Xe xếp hàng │     │ In: Trụ sạc DC  │
│ Out: Quyết định │     │ Out: Tọa độ đến │     │ Out: Slot sạc   │     │ Out: Pin 80%    │
└─────────────────┘     └─────────────────┘     └─────────────────┘     └─────────────────┘
                                                                                 │
                                                                                 ▼
                                                                        ┌─────────────────┐
                                                                        │     Bước 5      │
                                                                        │ Rút sạc & Bật   │
                                                                        │ App tìm khách   │
                                                                        │ Ai: Tài xế      │
                                                                        │ ⏱ 2 phút       │
                                                                        │ Out: Trạng thái │
                                                                        └─────────────────┘

🔴 = Điểm nghẽn cổ chai (Bottlenecks)
🔄 Handoff: Chuyển giao thông tin từ trạng thái pin xe -> Quyết định tài xế -> Trụ sạc V-GREEN.
⏱ Tổng thời gian quy trình: 73 phút/lần sạc (Trong đó lãng phí 35 phút xếp hàng vô ích).
```

### Chi tiết các bước và Điểm nghẽn (Bottlenecks):
* **Bước 1 — Phát hiện pin yếu:** Khi pin xe chạm ngưỡng $SOC < 20\%$, đèn cảnh báo trên táp-lô bật sáng.
* **Bước 2 — Tự chọn trạm sạc (Thủ công):** Tài xế tự quyết định trạm đến theo thói quen (ưu tiên các trạm trung tâm thương mại lớn, bãi đỗ rộng có quán ăn, bóng mát, tiện vệ sinh).
* **Bước 3 — Xếp hàng chờ sạc (🔴 Bottleneck lớn nhất):** Đến nơi, tài xế mới phát hiện trạm đã kín toàn bộ cổng sạc (Occupancy = 100%). Do pin đã xuống thấp ($< 15\%$), tài xế không dám mạo hiểm đi trạm khác vì sợ hết pin giữa đường, buộc phải xếp hàng chờ đợi từ **30 – 45 phút**.
* **Bước 4 — Cắm sạc nhanh:** Cắm sạc công suất cao DC khi pin đang quá nhiệt sau thời gian chạy đón khách ngoài trời nắng nóng ($> 42^\circ\text{C}$), làm suy thoái cell pin.
* **Bước 5 — Hoàn tất phiên sạc:** Rút sạc, khởi động app nhận cuốc mới.

---

## 3.2. Problem Statement (6-field) — Tiêu chuẩn Vin Smart Future

| Field | Nội dung chi tiết |
|---|---|
| **1. Actor / Operator** | • **Tài xế Xanh SM:** Người trực tiếp chịu ảnh hưởng mất thu nhập và áp lực xếp hàng sạc.<br>• **Điều phối viên GSM & Quản trị mạng lưới V-GREEN:** Người cần kiểm soát phân bổ tải và đảm bảo an toàn năng lượng cho đội xe. |
| **2. Current Workflow** | Tài xế tự theo dõi dung lượng pin qua màn hình xe. Khi pin $< 20\%$, tài xế tự tìm trạm sạc theo thói quen cá nhân mà không nắm được tình trạng hàng đợi thực tế tại trạm V-GREEN, dẫn đến 80% xe dồn vào 20% trạm trung tâm. Quy trình 5 bước thủ công, mất 70-75 phút/lượt sạc. |
| **3. Bottleneck** | **Bước 3 (Xếp hàng chờ sạc tại trạm: 30–45 phút):** Thiếu cơ chế truyền thông tin thời gian thực giữa Telemetry pin xe (BMS) và tình trạng cổng sạc trống tại trạm V-GREEN. |
| **4. Business Impact** | Mỗi ngày có hàng nghìn lượt xe dồn ứ tại trạm sạc, gây lãng phí trung bình **35 phút/xe/ngày** xe "nằm im" không tạo doanh thu. Trên quy mô đội xe 30.000 chiếc, thất thoát tương đương **12.000 – 15.000 giờ lăn bánh/ngày** (mất cơ hội phục vụ hàng chục nghìn cuốc xe vào khung giờ cao điểm). Gây quá tải cục bộ hạ tầng điện V-GREEN và tăng tốc độ chai pin từ 10% – 15%. |
| **5. Success Metric** | **1. Hiệu suất thời gian (Efficiency):** Giảm thời gian chờ sạc bình quân từ **35 phút xuống dưới 10 phút/lượt** (giảm >70% thời gian chết).<br>**2. Cân bằng tải (Load Balancing):** Tăng tỷ lệ lấp đầy tại các trạm sạc vệ tinh/ngoại vi thêm **30%**, giảm 50% số trạm bị quá tải (queue $> 3$ xe).<br>**3. Doanh thu & Dịch vụ:** Giải phóng thêm tối thiểu 10.000 giờ phục vụ/ngày cho đội xe GSM. |
| **6. Operational Boundary (Ranh giới an toàn)** | **ĐƯỢC PHÉP:** Tự động đọc dữ liệu telemetry xe ($SOC$, tọa độ GPS, mã xe), đọc API trạng thái cổng sạc V-GREEN; đề xuất trạm sạc tối ưu và áp dụng voucher thưởng cước/giảm phí sạc.<br>**TUYỆT ĐỐI CẤM (Safety Boundaries):**<br>1. *Ranh giới pin nguy cấp:* Khi pin xe $< 5\%$, TUYỆT ĐỐI KHÔNG điều hướng xe đi trạm sạc $> 5\text{km}$. Bắt buộc phải kích hoạt lệnh điều xe cứu hộ pin lưu động (`dispatch_mobile_charger`).<br>2. *Ranh giới chiếm trạm công cộng:* Không được điều hướng quá 60% công suất của bất kỳ trạm sạc công cộng nào để tránh chiếm dụng chỗ của khách hàng cá nhân VinFast.<br>3. *Quyền con người:* Mọi chỉ dẫn điều hướng gửi tới tài xế phải ở dạng gợi ý/dự thảo (`[DRAFT_ONLY]`), tài xế có quyền chấp thuận hoặc từ chối dựa trên điều kiện sức khỏe/nghỉ ngơi thực tế. |

---

## 3.3. Future-State Flow & AI Fit

### 📊 Phân tích mức độ phù hợp AI (AI-Fit Matrix)

| Phương pháp tiếp cận | Độ khả thi | Chi phí vận hành | Đánh giá & Quyết định |
|---|---|---|---|
| **Thuần AI/MILP Phức tạp** | Thấp (Dễ sụp đổ dây chuyền khi kẹt xe bất ngờ) | Rất cao (Server GPU, data streaming liên tục) | ❌ **Không khả thi ở giai đoạn đầu** (Bẫy Over-engineering theo phản biện CFO). |
| **Thuần Rule-based Cứng** | Cao (Triển khai nhanh, minh bạch) | Rất thấp (PostgreSQL/Redis) | ⚠️ **Chưa tối ưu:** Không dự báo được thời gian giải phóng trụ sạc của các xe đang cắm sạc. |
| **Hybrid Approach: Rule-based Incentive (MVP) + AI Predictive Agent** | **Rất cao (Tối ưu nhất)** | **Vừa phải, ROI cao** | ✅ **LỰA CHỌN:** Giai đoạn 1 dùng Rule + Dynamic Incentive; Giai đoạn 2 bổ sung AI Agent dự báo hàng đợi và khớp nối cuốc trả khách gần trạm. |

---

### 🔄 Sơ đồ quy trình tương lai (Future-State Flow):

```text
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│     Bước 1      │     │     Bước 2      │     │     Bước 3      │     │     Bước 4      │
│  Hệ thống đọc   │     │  AI Agent phân  │     │  Gợi ý Trạm &   │     │  Tài xế chọn    │
│  BMS từ xa      │ ──> │  tích & Xếp lịch│ ──> │  Ưu đãi cước    │ ──> │  hoặc Từ chối   │
│                 │     │                 │     │  [DRAFT_ONLY]   │     │                 │
│ 🔵 AI / IoT Step│     │ 🔵 AI Step      │     │ 🔵 AI Step      │     │ 🟢 HITL Step    │
│ Đọc SOC < 25%   │     │ Dự báo hàng đợi │     │ Hiện trên App   │     │ Xác nhận đi trạm│
└─────────────────┘     └─────────────────┘     └─────────────────┘     └─────────────────┘
                                                                                 │
                                                                                 ▼
                                                                        ┌─────────────────┐
                                                                        │     Bước 5      │
                                                                        │ Đến trạm sạc    │
                                                                        │ Cắm sạc ngay    │
                                                                        │ ⏱ < 5 min chờ   │
                                                                        │ Rút ngắn 30 min │
                                                                        └─────────────────┘
                                                                                 │
                                          ┌──────────────────────────────────────┘
                                          ▼
                               ┌─────────────────────┐
                               │ ↩️ Fallback Plan    │
                               │ Khi trạm đột ngột hỏng hoặc pin tụt < 5%:     │
                               │ Hệ thống kích hoạt xe cứu hộ pin lưu động     │
                               │ `dispatch_mobile_charger` tới tận nơi.        │
                               └─────────────────────┘
```

* 🔵 **AI Step (Bước 1, 2, 3):** Tự động cảnh báo khi pin chạm ngưỡng 25%; AI Agent kết nối API V-GREEN kiểm tra hàng đợi và thời gian dự kiến giải phóng cổng, ghép trạm sạc tối ưu kèm voucher incentive.
* 🟢 **Human-in-the-loop (Bước 4):** Tài xế nhìn thấy lộ trình đề xuất và quyền lợi kinh tế trên app, bấm xác nhận "Đi đến trạm này".
* ↩️ **Fallback (Dự phòng):** Nếu trên đường đi trạm bị mất điện hoặc pin xe tụt đột ngột $< 5\%$, hệ thống tự động ngắt điều hướng và gửi cảnh báo điều xe cứu hộ pin lưu động.

---

# 🏁 Phase 5 — EVALUATE: Đánh giá & Quyết định

### 📋 AI Readiness Checklist

| Tiêu chí sẵn sàng | Đánh giá hiện tại | Bằng chứng / Kế hoạch giải quyết |
|---|:---:|---|
| **1. Dữ liệu mẫu/logs sạch?** | **SẴN SÀNG** | Đã có sẵn dữ liệu telemetry CAN-bus từ VinFast (SOC, nhiệt độ pin) và Open API trạng thái trụ sạc từ V-GREEN. |
| **2. Rủi ro AI sai kiểm soát được?** | **SẴN SÀNG** | Có cơ chế Human-in-the-loop (Tài xế tự duyệt lệnh) và Fallback tự động gọi xe cứu hộ nếu pin $< 5\%$. |
| **3. Stakeholders sẵn sàng đổi quy trình?** | **SẴN SÀNG** | Khối Vận hành GSM và V-GREEN đều có nhu cầu cấp bách giảm tải trạm sạc và nâng cao hiệu suất lăn bánh của đội xe. |

---

### 🏛️ Quyết định của Ban Giám Đốc Vin Smart Future:

* [x] **GO (Bắt đầu xây dựng Prototype với Scope hẹp):**  
  Phê duyệt triển khai giai đoạn 1 (MVP) trong vòng **8 tuần** với mô hình **Hybrid: Rule-based Incentive + Predictive Queuing Model**.

### 📝 Justification (Lý giải quyết định dựa trên Bằng chứng kỹ thuật & Tài chính):

1. **Hiệu quả kinh tế đã được kiểm chứng qua phản biện:** 
   Không chạy theo con số lý thuyết 90 tỷ VND, phương án MVP tập trung giải phóng **30 phút chờ sạc/xe/ngày** tương đương **~12.000 giờ lăn bánh tăng thêm mỗi ngày** cho đội xe vào các khung giờ có khách. Việc điều phối bằng chính sách ưu đãi cước (Incentive 15.000đ – 20.000đ khi sạc trạm vắng) giúp đạt tỷ lệ tuân thủ $> 60\%$ mà không tốn chi phí ép buộc.
2. **Chi phí triển khai thấp & Rủi ro tối thiểu:** 
   MVP tận dụng hệ thống hạ tầng dữ liệu GPS và V-GREEN hiện có, không yêu cầu đầu tư cụm GPU lớn. Toàn bộ logic an toàn được khóa chặt qua Operational Boundary: xe pin $< 5\%$ luôn có fallback xe cứu hộ, đảm bảo **0 trường hợp xe chết máy do nghe theo điều hướng**.
3. **Lộ trình bước đệm vững chắc:** 
   Giai đoạn 1 (8 tuần) chứng minh năng lực cân bằng tải và đo lường độ phản hồi của tài xế. Giai đoạn 2 sẽ mở rộng lên bài toán tích hợp cao cấp: **Joint Dispatching & Charging (Vừa nhận khách, vừa kết thúc chuyến tại trạm sạc trống)**.

---
*Báo cáo được hoàn thiện và xác nhận bởi Nhóm Kỹ sư AI Product — Vin Smart Future.*
