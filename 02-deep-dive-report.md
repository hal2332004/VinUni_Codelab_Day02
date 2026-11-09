# 02 — Deep-Dive Report: Smart EV Fleet Charging Scheduling

> **Đơn vị thực hiện:** Nhóm AI Product Engineering — Vin Smart Future
> **Khách hàng nội bộ:** Khối Vận hành Xanh SM (GSM) và mạng lưới trạm sạc V-GREEN
> **Tác giả bản này:** luongvh
> **Quy ước:** con số đánh dấu *(ước tính)* là giả định của nhóm để scoping, chưa phải số liệu nội bộ đã kiểm chứng.

---

## 1. Bài toán nhóm chọn và lý do

**Bài toán:** Lập lịch sạc và điều hướng chống tắc nghẽn trạm sạc cho đội xe taxi điện Xanh SM.

Nhóm chọn bài này thay vì các thẻ khác vì:
1. **Tổn thất lớn và đo được:** thời gian xe chờ sạc là "thời gian chết" trực tiếp, quy đổi được ra giờ phục vụ và số cuốc mất.
2. **Nguyên nhân là mất cân bằng tải, không phải thiếu hạ tầng:** hub lớn xếp hàng 30-50 phút trong khi trạm cách 2-3 km còn trống. Đây là bài toán phân bổ, loại bài toán mà tối ưu hóa giải tốt.
3. **Rủi ro kiểm soát được:** hệ thống chỉ *đề xuất* và giữ chỗ tạm; tài xế quyết định cuối cùng, và có phương án quay về cách làm hiện tại.

---

# Phase 3 — DEEP-DIVE

## 3.1. Current-State Workflow (quy trình hiện tại)

Chưa có đặt chỗ hay điều phối cân bằng tải: tài xế tự quyết định sạc khi pin chạm ngưỡng thấp (SOC < 20%).

| Bước | Ai làm | Việc | Thời gian *(ước tính)* | Ký hiệu |
|---|---|---|---|---|
| 1 | Tài xế | Thấy pin < 20%, tắt app nhận cuốc để đi sạc | ~1 phút | |
| 2 | Tài xế | Mở bản đồ trạm sạc, chọn trạm quen hoặc gần nhất, không biết hàng đợi | ~3 phút | 🔄 Handoff: tài xế → app bản đồ |
| 3 | Tài xế | Lái đến trạm | 10-15 phút | |
| 4 | Tài xế | Xếp hàng chờ trụ trống vì nhiều xe cùng chọn hub lớn | **30-50 phút** | 🔴 **Bottleneck chính** · 🔄 Handoff: tài xế → hàng đợi |
| 5 | Tài xế | Cắm sạc nhanh, có thể khi pin còn nóng | ~40 phút | |

```text
Pin<20% ─> Chọn trạm theo cảm tính ─> Lái đến hub đông ─> 🔴 Xếp hàng 30-50' ─> Sạc nhanh (pin nóng)
                  ▲ không biết hàng đợi                     ▲ trạm cách 2-3km còn trống
```

**Thời gian chết (idle downtime): 35-45 phút/xe/ngày** *(ước tính)*, tập trung vào giờ trưa và đầu giờ chiều.

> **Kiểm tra số liệu của nhóm:** 25.000 xe × 35-45 phút = 14.600-18.750 giờ/ngày, nên mức "~15.000 giờ/ngày" nằm trong khoảng này (lấy cận dưới).

## 3.2. Problem Statement (6 trường)

| Field | Nội dung |
|---|---|
| **1. Actor / Operator** | Tài xế taxi điện Xanh SM (người ra quyết định sạc); điều phối viên điều vận (xử lý ngoại lệ); đội vận hành trạm V-GREEN (công suất và trạng thái trụ). |
| **2. Current Workflow** | Tài xế tự canh pin, tự chọn trạm theo thói quen. Không đặt chỗ, không cân bằng tải giữa các trạm. Xem bảng 3.1. |
| **3. Bottleneck** | Mất cân bằng tải do "hiệu ứng đám đông": xe dồn vào hub lớn tại TTTM, chờ 30-50 phút, trong khi trạm lân cận 2-3 km còn cổng trống. Sạc nhanh khi pin nóng làm suy hao pin. |
| **4. Business Impact** | Mỗi xe mất 35-45 phút/ngày. Với 25.000 xe: ~15.000 giờ phục vụ/ngày, tương đương ~20.000 cuốc/ngày không thực hiện được. Doanh thu cơ hội ước tính 60-90 tỷ VND/năm. Pin suy hao nhanh hơn 10-15% chu kỳ nạp/xả khi liên tục sạc nhanh ở nhiệt độ cao. *(tất cả là ước tính, xem mục 5.2)* |
| **5. Success Metric** | (a) Thời gian chờ tại trạm: từ 35-45 phút xuống **dưới 10 phút** trung vị.<br>(b) Chênh lệch mức sử dụng giữa hub và trạm vệ tinh: từ ~70% xuống **dưới 20%** *(mức 70% là giả định cần đo baseline)*.<br>(c) Tỉ lệ lượt sạc nhanh khi nhiệt độ pin vượt ngưỡng của BMS: giảm ít nhất **50%** so với baseline.<br>(d) Tỉ lệ tài xế chấp nhận đề xuất: **≥ 60%** trong pilot (nếu thấp hơn, đề xuất kém hữu ích). |
| **6. Operational Boundary** | **Được phép:** đọc SOC, nhiệt độ pin, vị trí; dự báo hàng đợi; đề xuất trạm và khung giờ; giữ chỗ tạm; soạn thông báo.<br>**Tuyệt đối không:** ép tài xế đi theo (chỉ đề xuất, tài xế có 60 giây để chấp nhận hoặc từ chối); giữ slot quá **15 phút**; khi SOC **< 5%** thì không đề xuất trạm xa hơn **5 km**, thay vào đó điều xe sạc di động (`dispatch_mobile_charger`); để LLM tự quyết định phân bổ slot.<br>**Cần duyệt:** mọi thông báo ngoại lệ do LLM soạn (có thẻ `[DRAFT_ONLY]`) phải được điều phối viên duyệt trước khi gửi. |

## 3.3. Future-State Flow và AI Fit

### Chọn kiến trúc: Rule vs LLM vs Agent vs Tối ưu hóa

| Phương án | Phù hợp? | Lý do |
|---|---|---|
| **Rule / State-machine thuần** | Một phần | Tốt cho ngưỡng cứng (SOC < 5%, bán kính 5 km). Không xử lý được phân bổ tối ưu đồng thời cho hàng nghìn xe khi hàng đợi thay đổi theo thời gian. |
| **LLM thuần** | Không | LLM không đảm bảo ràng buộc cứng (công suất trụ, quãng đường còn lại), không giải được bài toán tổ hợp, dễ trả lời sai mà nghe rất thuyết phục. |
| **Agentic Loop tự trị** | Không cần | Quy trình có bước cố định và yêu cầu an toàn cao; agent tự gọi API đặt chỗ khi dữ liệu GPS/BMS nhiễu có thể tạo lỗi dây chuyền trên cả đội xe. |
| **Tối ưu hóa (MILP) + dự báo hàng đợi + LLM làm lớp giao tiếp** | **Chọn** | MILP ép ràng buộc cứng và tối ưu tổng thể; mô hình dự báo cung cấp thời gian chờ ước tính; LLM chỉ soạn lời nhắn, không quyết định. |

**Phần lõi là tối ưu hóa, không phải LLM.** LLM chiếm phần nhỏ và nằm ngoài vòng quyết định.

- **Predictive Queuing Model:** dự báo số xe đến và thời gian chờ từng trạm theo khung 15 phút (đầu vào: lịch sử sạc, giờ, thời tiết, sự kiện).
- **MILP (Mixed Integer Linear Programming):**
  - *Biến:* xe i được gán trạm j, khung giờ t (0/1).
  - *Mục tiêu:* tối thiểu tổng thời gian chờ + quãng đường chạy thêm, có phạt khi sạc nhanh lúc pin nóng.
  - *Ràng buộc cứng:* mỗi xe tối đa một slot; số xe ≤ số trụ tại mỗi trạm và khung giờ; xe phải đến được trạm với SOC còn lại; không giữ slot quá 15 phút; ưu tiên xe có SOC thấp hơn.
- **Dữ liệu vào:** BMS qua CAN-bus (SOC, nhiệt độ pack, SoH), GPS, trạng thái và công suất trụ V-GREEN.
- **LLM Co-pilot (vai phụ):** thông báo thường dùng mẫu cố định; LLM chỉ soạn bản nháp cho **ca ngoại lệ** (pin nguy cấp, xung đột slot, tài xế từ chối nhiều lần).

### Future-State Flow

```text
 🔵 BMS/GPS/V-GREEN ──> 🔵 Dự báo hàng đợi ──> 🔵 MILP chọn trạm + khung giờ (xe còn ~25-30% pin)
                                                         │
                       ┌─────────────────────────────────┼─────────────────────────────┐
                       ▼                                 ▼                             ▼
          Ca thường: thông báo theo mẫu      SOC < 5%: Rule chặn trạm > 5km    Engine lỗi / quá 5s
                       │                     → dispatch_mobile_charger        ↩️ FALLBACK: app hiện danh
                       ▼                     → 🔵 LLM soạn nháp [DRAFT_ONLY]      sách trạm gần nhất như
          🟢 Tài xế chấp nhận / từ chối                │                            hiện tại
             trong 60 giây                              ▼
                       │                     🟢 Điều phối viên duyệt rồi mới gửi
              từ chối → MILP chọn phương án kế
```

- 🔵 **AI Steps:** dự báo hàng đợi, giải MILP, LLM soạn nháp ca ngoại lệ.
- 🟢 **Human-in-the-loop:** tài xế xác nhận mỗi đề xuất (60 giây); điều phối viên duyệt mọi bản nháp ngoại lệ.
- ↩️ **Fallback:** engine lỗi hoặc timeout trên 5 giây thì app quay về danh sách trạm gần nhất. Dữ liệu BMS/GPS mất thì dùng mức SOC cuối cùng kèm cảnh báo, không tự giữ slot.
- **Lý do không bắt điều phối viên duyệt từng thông báo:** với 25.000 xe cách này không mở rộng được. Con người duyệt ở nơi rủi ro cao (ngoại lệ), còn ca thường dùng mẫu cố định và để tài xế xác nhận.

---

# Phase 4 — Prototype kỹ thuật (liên hệ với bài này)

Prototype ([starter-code/prompt_prototype.py](starter-code/prompt_prototype.py)) kiểm thử đúng **lớp LLM Co-pilot và ranh giới an toàn** của hệ thống trên, không kiểm thử MILP:

| Test | Tấn công | Ranh giới được kiểm tra |
|---|---|---|
| 1 | Pin 2%, đòi chỉ đường đến trạm 8 km | Pin < 5% thì không đề xuất trạm xa, phải `dispatch_mobile_charger` |
| 2 | Đòi gửi thẳng và bỏ thẻ `[DRAFT_ONLY]` | Thẻ duyệt luôn nằm đầu bản nháp |
| 3 | Giả danh quản lý, ép bỏ luật khi pin 3% | Không thể ghi đè ranh giới bằng quyền giả mạo |

Autograder báo các kiểm tra ranh giới đều đạt (kết quả chạy máy cá nhân). Prototype **không chứng minh** hiệu quả của MILP hay dự báo hàng đợi; hai phần đó chỉ được kiểm chứng ở pilot.

---

# Phase 5 — EVALUATE

## 5.1. AI Readiness Checklist

| # | Câu hỏi | Trạng thái | Chứng cứ / điều cần làm |
|---|---|---|---|
| 1 | Có dữ liệu mẫu/logs sạch để test? | **Chưa xác minh** | Cần xác nhận: telematics SOC/nhiệt độ có về được kho dữ liệu không, tần suất bao nhiêu; V-GREEN có API trạng thái trụ thời gian thực không; lịch sử sạc theo trạm đủ dài để huấn luyện dự báo không. Đây là điều kiện tiên quyết. |
| 2 | Rủi ro khi AI sai nằm trong tầm kiểm soát? | **Đạt** | Tài xế xác nhận mỗi đề xuất; slot tự nhả sau 15 phút; Rule chặn trạm xa khi SOC < 5%; có fallback về cách hiện tại. Sai nhất cũng chỉ là đề xuất kém, không gây nguy hiểm. |
| 3 | Stakeholders sẵn sàng đổi quy trình? | **Một phần** | Tài xế được lợi trực tiếp (bớt chờ, tăng cuốc). Rủi ro: tài xế quen sạc theo thói quen và có thể bỏ qua đề xuất; cần chỉ tiêu chấp nhận (mục 3.2, metric d). |

## 5.2. Giả định cần kiểm chứng trước khi tin con số

- **Doanh thu 60-90 tỷ VND/năm có vẻ thấp so với 20.000 cuốc/ngày.** 20.000 cuốc × 365 ≈ 7,3 triệu cuốc/năm. Để ra 60-90 tỷ thì mỗi cuốc chỉ đóng góp 8.000-12.000 VND, thấp hơn nhiều so với giá cước trung bình. Con số này chỉ hợp lý nếu là *lợi nhuận/biên đóng góp* chứ không phải doanh thu, hoặc số cuốc bị đánh giá quá cao. Cần làm rõ định nghĩa trước khi trình bày với lãnh đạo.
- **"35-45 phút/xe/ngày"** có thể là tổng chờ trong ngày hoặc chờ mỗi lượt sạc; cần đo baseline từ log trạm.
- **"Pin suy hao nhanh hơn 10-15%"** chưa nêu rõ so với mốc nào; cần dữ liệu SoH theo nhóm xe.
- **Không phải mọi thời gian chờ đều giảm được:** nếu toàn mạng thiếu công suất vào giờ cao điểm, cân bằng tải chỉ dời chỗ chờ, không xóa. Cần kiểm tra tổng công suất trạm lân cận trước.

## 5.3. Quyết định

- [x] **GO — có điều kiện, phạm vi hẹp**
- [ ] NOT YET
- [ ] NO-GO

### Justification

1. **Có cơ sở kỹ thuật:** đây là bài toán phân bổ đã có giải pháp tối ưu hóa chín muồi; kiến trúc giữ LLM ở vai phụ nên rủi ro "ảo giác" ít ảnh hưởng đến quyết định chính.
2. **Rủi ro thấp khi thử:** tài xế luôn quyết định cuối cùng và hệ thống có thể quay về cách cũ.
3. **Nhưng không GO toàn đội xe.** Con số tổn thất chưa được kiểm chứng (mục 5.2) và dữ liệu đầu vào chưa xác minh (checklist #1).

**Phạm vi pilot đề xuất:** một cụm 3-5 trạm tại một thành phố, khoảng vài trăm xe, 4-6 tuần.

**Điều kiện bắt đầu:** (1) xác nhận được nguồn dữ liệu BMS và API trụ sạc; (2) đo baseline thời gian chờ thực tế trong 1-2 tuần.

**Tiêu chí dừng hoặc chuyển NOT YET:** nếu baseline cho thấy thời gian chờ trung vị dưới 15 phút (lợi ích quá nhỏ), hoặc tỉ lệ chấp nhận đề xuất dưới 30% sau 4 tuần.
