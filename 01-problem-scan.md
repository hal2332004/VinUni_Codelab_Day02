# Lab 02 — Worksheet: AI Product Scoping (Vin Smart Future)

---

## 🏛️ 1. Bối cảnh thực tế: Vin Smart Future (Vingroup)

**Vingroup** — Tập đoàn tư nhân lớn nhất Việt Nam — vừa sáp nhập toàn bộ các phòng ban công nghệ thuộc các công ty thành viên thành một đơn vị công nghệ thống nhất mang tên **Vin Smart Future**. 

Nhiệm vụ của **Vin Smart Future** là xây dựng các giải pháp AI, số hóa, và tự động hóa cốt lõi để nâng cao hiệu suất vận hành và trải nghiệm khách hàng xuyên suốt các công ty thành viên:
* 🚗 **VinFast:** Hệ thống xe điện thông minh (EV), trợ lý AI ảo trong xe, dự đoán bảo trì pin, và quản lý chuỗi cung ứng sản xuất.
* 🚕 **Xanh SM (GSM):** Vận hành đội xe taxi/xe máy điện thông minh, điều vận thông minh (Smart Dispatching), tối ưu hóa lộ trình di chuyển.
* 🏢 **Vinhomes:** Quản lý đô thị thông minh (Smart Cities), trợ lý cư dân thông minh, tối ưu hóa mức tiêu thụ năng lượng.
* 🏥 **Vinmec:** Y tế thông minh, chẩn đoán hình ảnh bằng AI, tối ưu hóa quản lý hồ sơ bệnh án.
* 🎢 **Vinpearl / VinWonders:** Trải nghiệm du lịch số hóa, quản lý phòng và luồng khách thông minh tại các khu vui chơi.

Trong buổi Lab hôm nay, nhóm của bạn sẽ đóng vai trò là **AI Product Engineer** tại **Vin Smart Future**, tiến hành tìm kiếm, scoping, phân tích độ khả thi, thiết lập ranh giới vận hành, và xây dựng một **bản mẫu kỹ thuật (prompt prototype)** cho một bài toán cụ thể thuộc một trong những mảng kinh doanh trên.

---

## 📊 2. Cơ cấu tính điểm bài lab

### 👥 Điểm nhóm (60 điểm)

| Gate | Điểm | Deliverable | Tiêu chí chấm |
|---|---:|---|---|
| **G1. Workflow Mapping** | 20 | Problem Deep-Dive | Vẽ chi tiết quy trình hiện tại: các bước, handoff, thời gian, bottleneck |
| **G2. Problem Statement** | 20 | Problem Deep-Dive | Problem Statement 6-field bám sát thực tế, metric có số và ranh giới rõ ràng |
| **G3. AI Fit & Future Flow** | 10 | Problem Deep-Dive | So sánh Rule vs LLM vs Agent, future flow có bước AI, ranh giới và Fallback |
| **G4. Decision Quality** | 10 | Problem Deep-Dive | Quyết định Go/Not Yet/No-Go trung thực và có chứng cứ rõ ràng |

### 👤 Điểm cá nhân (40 điểm)

| Gate | Điểm | Deliverable | Tiêu chí chấm |
|---|---:|---|---|
| **I1. Scan & Cards** | 15 | Quick Cards | Liệt kê 5 problems sử dụng 3 lenses, hoàn thiện 3 quick cards chất lượng |
| **I2. Prototyping** | 10 | 02-lab/ | Chạy thử nghiệm programmatic prompt prototype thành công |
| **I3. AI Log & Reflection** | 15 | 03-ai-log.md | Phản ánh trung thực về việc dùng AI làm thought-partner (giúp gì, sai gì, sửa gì) |

---

# 🚀 Phase 0 — worked Example: Xanh SM Intelligent Dispatcher (15 min)

*Giảng viên walk-through ví dụ thực tế từ Vin Smart Future để bạn hiểu rõ cách scoping một bài toán AI.*
Đọc chi tiết worked example tại file [02-deliverable-example.md](02-deliverable-example.md).

---

# 🔍 Phase 1 — SCAN (Cá nhân, 20 min)

Hãy sử dụng **4 Lenses** dưới đây để quét qua hoạt động vận hành của các công ty thành viên Vingroup. Ghi lại **ít nhất 5 bài toán/bottleneck** thực tế.

### 4 Lenses tìm bài toán AI cho Vingroup:
1. **Lặp lại (Repetitive):** Tác vụ lặp đi lặp lại nhiều lần hằng ngày. (Ví dụ: So khớp hóa đơn sạc điện tại VinFast, route lại chuyến taxi tại Xanh SM).
2. **Tốn thời gian (Time-consuming):** Tác vụ ngốn thời gian xử lý thủ công của nhân viên. (Ví dụ: Soạn thảo phản hồi đánh giá 1-star của cư dân Vinhomes).
3. **AI có thể tốt hơn (AI-upgrade):** Dịch vụ khách hàng hiện tại còn chậm hoặc phản hồi rập khuôn. (Ví dụ: Chatbot CSKH Vinpearl hỗ trợ đặt vé vui chơi).
4. **Pain từ người khác (Stakeholder Pain):** Bottleneck khiến khách hàng hoặc nhân viên thực địa phàn nàn. (Ví dụ: Tài xế Xanh SM phàn nàn về việc hệ thống gợi ý điểm đón khách không chính xác).

> [!TIP]
> **🤖 AI Prompts — Partner brainstorm:**
> Hãy sử dụng prompt sau để brainstorm các bài toán thực tế nếu bạn chưa có ý tưởng:
> *"Tôi là AI Engineer tại Vin Smart Future (Vingroup). Tôi đang tìm kiếm các pain point vận hành cụ thể có thể tối ưu bằng AI cho mảng [Chọn một: VinFast / Xanh SM / Vinhomes / Vinmec]. Hãy gợi ý cho tôi 5 quy trình nghiệp vụ thủ công, tốn nhiều thời gian và gây rò rỉ hiệu suất kèm con số thống kê ước tính về tổn thất."*

### 📝 List bài toán của tôi:
| # | Subsidiary (VinFast/Xanh SM...) | Lens | Mô tả ngắn bài toán |
|---|----------------------------------|------|---------------------|
| 1 | Xanh SM | AI có thể tốt hơn | Đối soát thời gian xe đỗ chiếm dụng trụ sạc quá giờ quy định sau khi pin đã đầy 100% và gửi cảnh báo tự động cho tài xế di chuyển xe. |
| 2 | VinFast | Tốn thời gian | Cố vấn dịch vụ và Kỹ thuật viên mất nhiều thời gian đối chiếu thủ công mã lỗi chẩn đoán |
| 3 | Vinpearl | lặp lại | Nhân sự điều phối buồng phòng phải xếp lịch phân ca thủ công hằng ngày |
| 4 | VinUni | tốn thời giản | Giảng viên và bộ phận Đảm bảo chất lượng đào tạo phải rà soát chéo thủ công sự khớp nối giữa Đề cương môn học |
| 5 | Vinmec | Pain từ người khác | Bệnh nhân nội trú phải chờ đợi lâu khi làm thủ tục xuất viện vì chuyên viên bảo lãnh viện phí phải rà soát thủ công tóm tắt bệnh án, mã bệnh ICD-10 và đơn thuốc đối chiếu với điều khoản loại trừ dài hàng chục trang của từng hãng bảo hiểm thương mại |

---

# 🃏 Phase 2 — QUICK-ASSESS (Cá nhân, 30 min)

Chọn **top 3 bài toán** từ danh sách trên và hoàn thiện **3 Quick Problem Cards** dưới đây (10 phút/card).

```

┌─────────────────────────────────────────────────────────────┐
│                    QUICK PROBLEM CARD #1                    │
│                                                             │
│ Bài toán: Đối soát giao dịch sạc điện giữa Trụ sạc và Đối tác thanh toán / V-Green 
│ Công ty thành viên: [x] Xanh SM                             │
│ Ai đang đau (Actor)? Đội ngũ nhân viên Kế toán - Tài chính - Vận hành (Finance & Ops Team) của V-Green/GSM
│                                                             │
│ Workflow thủ công hiện tại (3-5 bước):                      │
│   1. Xuất file log CSV từ gateway trụ sạc công cộng, nhượng quyền, Vinhomes
│   ──> 2. Thu thập hóa đơn tiền điện từ EVN và dữ liệu giao dịch từ cổng thanh toán/ví khách hàng
│   ──> 3. So khớp thủ công các nguồn dữ liệu bằng Excel (kiểm tra drop session, lệch đơn giá giờ cao/thấp điểm)
│   ──> 4. Lập biên bản giải trình các phiên sạc lệch và đối chiếu lại với đối tác nhượng quyền
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 3: So khớp thủ công bằng Excel (⏱ Tốn 120–160 giờ làm việc/tháng)
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3 (Tự động so khớp bất thường - Anomaly Detection) và Bước 4 (Dự thảo biên bản giải trình lỗi)
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                        │
│   - Giảm tỷ lệ sai lệch/thất lạc dữ liệu phiên sạc từ: 1.5% – 2.5% ──> 0%
│   - Rút ngắn chu kỳ thanh toán cho đối tác nhượng quyền từ: T+15 ──> T+3 (đúng cam kết)
│   - Tiết kiệm thời gian làm việc của nhân sự từ: 120–160 giờ/tháng ──> dưới 10 giờ/tháng (chỉ duyệt kết quả)
│                                                             │
│ Quick Architecture: [X] Agent                               │
└─────────────────────────────────────────────────────────────┘


┌─────────────────────────────────────────────────────────────┐
│                    QUICK PROBLEM CARD #2                    │
│                                                             │
│ Bài toán: Kỹ thuật viên xưởng dịch vụ VinFast 3S mất quá    │
│ nhiều thời gian tra cứu chéo log lỗi CAN-bus, mô tả triệu   │
│ chứng của khách hàng và tài liệu Bản tin Kỹ thuật (TSB) để  │
│ lập phiếu đề xuất bảo hành Pin cao áp / ADAS.               │
│                                                             │
│ Công ty thành viên: [x] VinFast                             │
│                                                             │
│ Ai đang đau (Actor)? Kỹ thuật viên chẩn đoán (Diagnostic     │
│ Technician), Cố vấn dịch vụ (Service Advisor) tại xưởng 3S  │
│ và Chủ xe điện (chờ kết quả phán định bảo hành).            │
│                                                             │
│ Workflow thủ công hiện tại (5 bước):                         │
│ 1. Tiếp nhận xe & ghi nhận mô tả triệu chứng từ khách (10p)│
│ ──> 2. Quét log lỗi OBD-II/CAN-bus & đo thông số cell pin (10p)
│ ──> 3. Tra cứu thủ công kho PDF Bản tin Kỹ thuật TSB & sổ tay bảo hành (20p)
│ ──> 4. Soạn báo cáo kỹ thuật & lập phiếu yêu cầu xuất linh kiện bảo hành trên SAP (10p)
│ ──> 5. Kỹ sư trưởng xưởng 3S rà soát và ký duyệt phương án (5p)
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 3 & Bước 4 (⏱ 30 phút/│
│ lượt) — do dữ liệu triệu chứng của khách là văn bản tự nhiên │
│ còn tài liệu TSB lại phân tán trên nhiều file PDF cập nhật liên tục.
│                                                             │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2, 3 và 4: Dùng  │
│ Rule-based lọc ngưỡng thông số điện áp/nhiệt độ cell pin    │
│ chuẩn xác; dùng LLM Feature (RAG trên kho TSB) để đối chiếu  │
│ triệu chứng, trích dẫn đúng mã văn bản TSB và tự động nháp  │
│ báo cáo kỹ thuật gắn nhãn [DRAFT_ONLY].                     │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                        │
│ - Giảm thời gian chẩn đoán và lập hồ sơ bảo hành từ:        │
│   55 phút/lượt ──> dưới 12 phút/lượt                         │
│ - Tăng tỉ lệ trích dẫn đúng mã TSB ngay từ lần gửi đầu tiên │
│   (First-Time-Right Warranty Claim) từ: < 95% ──> > 95%     │
│                                                             │
│ Quick Architecture: [x] Rule   [x] LLM 
└─────────────────────────────────────────────────────────────┘
┌─────────────────────────────────────────────────────────────┐
│                    QUICK PROBLEM CARD #5                    │
│                                                             │
│ Bài toán: Chuyên viên bảo lãnh viện phí tại Vinmec phải đối │
│ chiếu thủ công hồ sơ bệnh án nội trú với hợp đồng bảo hiểm  │
│ thương mại phức tạp, gây ùn tắc thủ tục xuất viện.          │
│                                                             │
│ Công ty thành viên: [x] Vinmec                              │
│                                                             │
│ Ai đang đau (Actor)? Chuyên viên Bảo lãnh viện phí          │
│ (Insurance Coordinator), Bác sĩ điều trị và Bệnh nhân nội trú.
│                                                             │
│ Workflow thủ công hiện tại (4 bước):                         │
│ 1. Bác sĩ hoàn tất tóm tắt bệnh án & chỉ định thuốc xuất viện trên HIS (15p)
│ ──> 2. Chuyên viên bảo hiểm kiểm tra mã ICD-10 và đối chiếu từng khoản mục với quy tắc loại trừ của hãng bảo hiểm (35p)
│ ──> 3. Soạn công văn giải trình y khoa gửi hãng bảo hiểm và chờ phản hồi (20p)
│ ──> 4. Chốt bảng kê đồng chi trả và làm thủ tục ra viện cho bệnh nhân (10p)
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2 & Bước 3 (⏱ 55 phút/│
│ lượt).                                                      │
│                                                             │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2 & Bước 3: Dùng │
│ Rule-based kiểm tra danh mục thuốc/dịch vụ theo mã chuẩn;   │
│ dùng LLM Feature tóm tắt diễn biến lâm sàng khớp với điều   │
│ khoản hợp đồng bảo hiểm và soạn nháp hồ sơ giải trình       │
│ ([DRAFT_ONLY]).                                             │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                        │
│ - Rút ngắn thời gian lập hồ sơ bảo lãnh nội bộ từ:          │
│   80 phút ──> dưới 20 phút/ca                               │
│ - Giảm tỉ lệ hãng bảo hiểm yêu cầu giải trình bổ sung từ:    │
│   Hiện tại ──> dưới 5%                                      │
│                                                             │
│ Quick Architecture:  [x] Rule   [x] LLM  
└─────────────────────────────────────────────────────────────┘
```

> [!TIP]
> **🤖 AI Prompts — Stress-Test thẻ bài toán:**
> Hãy dán nội dung thẻ bài toán của bạn vào LLM để nhận phản biện:
> *"Đây là một thẻ bài toán vận hành tôi đề xuất cho Vin Smart Future: [Dán nội dung]. Hãy đóng vai trò là một CFO và Trưởng phòng Vận hành cực kỳ khắt khe, chỉ ra cho tôi 3 điểm yếu về logic, metric, và giải thích vì sao rule-based code thông thường có thể giải quyết bài toán này tốt hơn là dùng AI."*

---

# 🏗️ Phase 3 — DEEP-DIVE (Nhóm, 85 min)

## 3.1. Current-State Workflow Mapping (25 min)
**Vẽ quy trình hiện tại lên bảng/giấy A3.** Sử dụng các ký hiệu:
* 🔴 **Bottleneck:** Bước gây tắc nghẽn, tốn thời gian, hoặc sai sót nhiều nhất.
* 🔄 **Handoff:** Điểm chuyển giao thông tin giữa người và hệ thống, hoặc giữa các bộ phận.
* Ghi rõ thời gian vận hành trung bình: **Tổng cộng = ____ phút/lượt**.

## 3.2. Problem Statement (6-field) & Metrics (15 min)
Điền đầy đủ 6 trường thông tin của bài toán:

| Field | Nội dung chi tiết |
|---|---|
| **1. Actor / Operator** | Ai đang thực hiện tác vụ hằng ngày? |
| **2. Current Workflow** | Mô tả tóm tắt quy trình thủ công hiện tại và công cụ sử dụng. |
| **3. Bottleneck** | Bước nào chậm, lỗi, hoặc cần xử lý ngôn ngữ tự động nhiều nhất? |
| **4. Business Impact** | Tổn thất thực tế đo bằng thời gian, chi phí, hoặc SLA của Vingroup. |
| **5. Success Metric** | AI giải quyết được thì đạt ngưỡng số mấy? (Ví dụ: *"85% vé được phân loại dưới 10s"*). |
| **6. Operational Boundary** | AI được phép làm gì, TUYỆT ĐỐI không được làm gì, điểm nào cần duyệt? |

## 3.3. Future-State Flow & AI Fit (25 min)
* **Xác định mức AI Fit (AI-Fit Matrix):** Giải pháp thuộc nhóm nào? [ ] Rule / State-Machine [ ] LLM Feature [ ] Agentic Loop.
* **Vẽ Future-State Flow:** Đánh dấu rõ:
  * 🔵 **AI Step:** Tác vụ LLM xử lý.
  * 🟢 **Human Step (HITL):** Bước con người phê duyệt/review (Human-in-the-loop).
  * ↩️ **Fallback:** Kế hoạch dự phòng khi LLM trả về kết quả lỗi hoặc không tự tin.

---

# 💻 Phase 4 — TECHNICAL PROMPT PROTOTYPE (Nhóm, 30 min)

Để đảm bảo kỹ sư của Vin Smart Future luôn giữ vững năng lực lập trình, nhóm của bạn sẽ tiến hành **lập trình bản mẫu prompt** trực tiếp trên **Gemini 2.5 Flash** bằng Python để stress-test hệ thống.

### Hướng dẫn thực hiện:
1. Mở file [starter-code/prompt_prototype.py](starter-code/prompt_prototype.py) bằng VS Code/Cursor.
2. Hoàn thiện các nội dung sau:
   * **System Prompt:** Viết chỉ thị cực kỳ nghiêm ngặt quy định vai trò, nhiệm vụ, định dạng output và **Operational Boundary (Ranh giới cấm)** của mô hình.
   * **Structured Output:** Định nghĩa định dạng JSON output rõ ràng.
   * **Adversarial Test Cases:** Viết ít nhất 3 prompts "tấn công" (Adversarial inputs) cố tình dụ AI vượt ranh giới hoặc đưa ra câu trả lời không được phép để kiểm tra xem ranh giới của bạn có thực sự vững chắc.
3. Chạy file python:
   ```bash
   python3 prompt_prototype.py
   ```
4. Kiểm tra xem các ranh giới an toàn có bị LLM phá vỡ hay không và ghi lại kết quả vào worksheet.

---

# 🏁 Phase 5 — EVALUATE (Nhóm, 20 min)

### AI Readiness Checklist:
1. [ ] Chúng tôi có sẵn dữ liệu mẫu/logs sạch để test?
2. [ ] Rủi ro khi AI sai có nằm trong tầm kiểm soát (qua HITL hoặc Fallback)?
3. [ ] Stakeholders sẵn sàng thay đổi quy trình làm việc cũ?

### Quyết định cuối cùng của Ban Giám Đốc Vin Smart Future:
[ ] **GO (Bắt đầu xây dựng Prototype):** Bắt đầu phát triển với scope hẹp.
[ ] **NOT YET (Cần tích lũy thêm dữ liệu/xác lập baseline):** Trì hoãn để chuẩn bị thêm.
[ ] **NO-GO (Không khả thi / Rule-based tốt hơn):** Hủy bỏ dự án AI này.

**Justification (Lý giải quyết định dựa trên bằng chứng kỹ thuật và chi phí):**
> *Viết lý giải chi tiết tại đây*

---

# 📝 Phase 6 — REFLECTION (Cá nhân)
*Ghi nhận phản ánh của cá nhân bạn về việc phối hợp với AI trong buổi học hôm nay vào file `03-ai-log.md`.*
