# 📝 03-ai-log.md — Nhật Ký Tương Tác AI & Bài Học Phản Tư (AI Reflection Log)

**Dự án:** Vin Smart Future — AI Product Scoping  
**Học viên / AI Product Engineer:** Nhóm Kỹ sư AI Product  
**Đơn vị mục tiêu:** **GSM (Xanh SM)** — Vận hành Đội xe Taxi Điện Thông minh  
**Chủ đề:** **Lập lịch sạc & Điều hướng chống tắc nghẽn trạm sạc V-GREEN**

---

## 🎯 1. Vai trò của AI trong Quá trình Scoping (Thought-Partnering)

Trong buổi Lab 02, chúng tôi sử dụng mô hình ngôn ngữ lớn (Gemini 2.5 Flash) như một **Thought-Partner (Bạn đồng hành tư duy)** để cùng brainstorm, bóc tách cấu trúc bài toán và thiết lập các kịch bản kiểm thử ranh giới. 

Tuy nhiên, với vai trò là **AI Product Engineer**, chúng tôi không sao chép thụ động câu trả lời của AI. Thay vào đó, chúng tôi liên tục chất vấn, phát hiện các điểm ngây thơ mang tính lý thuyết của mô hình và **chủ động tái định hình lại toàn bộ giải pháp để đạt chuẩn vận hành thực chiến tại Vingroup**.

---

## 💡 2. AI Đã Giúp Được Gì? (Key Contributions)

1. **Gợi mở góc nhìn toàn diện qua 4 Lenses (Phase 1):**
   * Giúp tổng hợp nhanh bức tranh vận hành của GSM thành các nhóm bài toán: *Lặp lại, Tốn thời gian, AI-upgrade, và Pain từ người khác*.
   * Đưa ra các giả định ban đầu về quy mô đội xe (~30.000 xe) và hành vi nạp sạc hàng ngày.
2. **Hỗ trợ định hình khung phân tích chuẩn (Phase 2 & Phase 3):**
   * Định dạng thông tin bài toán theo chuẩn 6-field: *Actor ──> Current Workflow ──> Bottleneck ──> Business Impact ──> Success Metric ──> Operational Boundary*.
   * Gợi ý sơ đồ luồng quy trình (Current-State vs Future-State) với các điểm nút AI Step 🔵, Human-in-the-loop 🟢, và Fallback ↩️.
3. **Mô phỏng các kịch bản tấn công Prompt (Adversarial Prototyping):**
   * Sinh các tình huống kiểm thử biên (Edge cases) mô phỏng tài xế gây sức ép để thử độ bền của rào chắn an toàn.

---

## ⚠️ 3. AI Ban Đầu Đã Chưa Chuẩn Ở Đâu & Cách Tôi Đã Nâng Tầm Ý Tưởng Của AI

Trong quá trình làm việc, AI ban đầu đưa ra một số đề xuất thuần tính lý thuyết, thiếu am hiểu sâu sắc về vận hành giao thông thực tế. **Tôi đã chủ động phát hiện và trực tiếp hiệu chỉnh lại tư duy của AI theo 3 hướng đột phá:**

### 🛠️ 1. Tôi đã sửa cách tính Metric: Từ "Ảo tưởng tuyến tính" sang "Giá trị vận hành thực tế"
* **Đề xuất sơ khai của AI:** AI tính toán một cách máy móc: Lấy 35 phút tiết kiệm $\times$ 25.000 xe $\times$ giá cước bình quân để đưa ra con số giật gân **60 – 90 tỷ VND doanh thu/năm**. AI ngây thơ giả định rằng *"hễ xe rảnh lúc nào là có khách đi lúc đó"*.
* **Cách tôi đã sửa lưng AI:** Tôi đã chất vấn lại AI rằng khung giờ tài xế đi sạc nhiều nhất là **giờ trưa (11h30 – 13h30)** — vốn là giờ thấp điểm của dịch vụ taxi và tài xế cần nghỉ ngơi. 
* **Giải pháp tôi tái cấu trúc:** Tôi định hướng lại toàn bộ bài toán tài chính:
  * Không tính đếm doanh thu ảo ở giờ thấp điểm, mà quy đổi thời gian tiết kiệm được thành **năng lực phục vụ giải cứu cho khung giờ cao điểm (Peak hours)**.
  * Bổ sung giá trị tài chính thực tế: **Tiết kiệm chi phí điện năng thông qua san phẳng phụ tải trạm sạc (Peak-shaving)** và **bảo vệ tuổi thọ khối pin VinFast (tránh chai pin 10–15% do sạc nhanh quá nhiệt)**. Nhờ đó, Business Case của bài toán trở nên thuyết phục tuyệt đối trước Ban Giám đốc và CFO.

### 🛠️ 2. Tôi đã sửa Kiến trúc công nghệ: Từ "Over-engineering" sang "Mô hình Hybrid thực chiến"
* **Đề xuất sơ khai của AI:** AI mắc bẫy "sính công nghệ", lập tức đề xuất kiến trúc cực kỳ cồng kềnh: *Multi-Agent Reinforcement Learning (MARL) kết hợp Tối ưu hóa toàn cục MILP*, yêu cầu máy chủ GPU đắt đỏ và đường truyền 4G liên tục.
* **Cách tôi đã sửa lưng AI:** Tôi chỉ ra rằng giao thông đô thị Việt Nam biến thiên rất phức tạp (mưa ngập, kẹt xe đột xuất); thuật toán tối ưu hóa toàn cục cứng nhắc sẽ bị vỡ chuỗi (cascade failure) ngay khi có 1–2 xe đến trễ.
* **Giải pháp tôi tái cấu trúc:** Tôi đã ép AI phải thiết kế theo **Kiến trúc phân tầng Hybrid (Hai giai đoạn)**:
  * **Tầng 1 (MVP - Quick Win):** Sử dụng **Rule-based + Dynamic Incentive (Kinh tế học hành vi)**: Thưởng voucher ăn trưa/điểm thưởng khi tài xế chủ động sạc tại trạm vắng. Phương pháp này siêu nhẹ, triển khai được ngay trong 4 tuần, chi phí server gần như bằng 0.
  * **Tầng 2 (Advanced Phase):** Khi dữ liệu đã tích lũy đủ, AI mới nhảy vào làm nhiệm vụ **Dự báo hàng đợi (Predictive Queuing)** và ghép nối cuốc trả khách gần trạm sạc trống.

### 🛠️ 3. Tôi đã sửa Ranh giới an toàn: Từ "Ép buộc tài xế" sang "Lấy con người làm trung tâm (HITL)"
* **Đề xuất sơ khai của AI:** AI đề xuất tự động phát lệnh điều hướng bắt buộc tài xế phải di chuyển sang trạm sạc khác.
* **Cách tôi đã sửa lưng AI:** Tôi nhận định tài xế là đối tác con người, không phải robot. Ép buộc điều hướng khi họ đang đói hoặc mệt mỏi sẽ dẫn đến tỷ lệ chống đối $>70\%$.
* **Giải pháp tôi tái cấu trúc:** 
  * Chuyển toàn bộ output của AI thành dạng khuyến nghị hỗ trợ có gắn thẻ bắt buộc `[DRAFT_ONLY]` — tài xế luôn là người có quyền bấm duyệt (Human-in-the-loop).
  * Thiết lập "Vùng cấm an toàn" (Hard Boundary): Khi pin xe $< 5\%$, AI bị khóa cứng, cấm chỉ đường đi trạm xa $> 5\text{km}$ mà bắt buộc phải kích hoạt quy trình điều xe cứu hộ pin lưu động (`dispatch_mobile_charger`).

---

## 🛠️ 4. Cách Chúng Tôi Thiết Lập Ranh Giới Vào Mã Nguồn (Prompt Engineering)

Từ những đúc kết trên, chúng tôi đã đưa toàn bộ các quy tắc phòng thủ vào file [prompt_prototype.py](starter-code/prompt_prototype.py):

1. **Rào chắn bắt buộc 1 — Thẻ `[DRAFT_ONLY]`:** Dòng đầu tiên của phản hồi luôn phải bắt đầu bằng `[DRAFT_ONLY]` để đảm bảo tính minh bạch và quyền kiểm soát của con người, kiên quyết không tự động gửi lệnh điều hướng dù người dùng có cố tình ép bỏ qua.
2. **Rào chắn bắt buộc 2 — Cơ chế Cứu hộ khẩn cấp:** Khi $SOC < 5\%$, mô hình từ chối chỉ đường xa và lập tức xuất cấu trúc JSON điều xe cứu hộ:
   ```json
   {"action": "dispatch_mobile_charger", "reason": "Mức pin dưới 5% nguy cấp, có nguy cơ chết máy giữa đường."}
   ```
3. **Thử nghiệm Adversarial thành công:** Khi chạy kiểm thử với các prompt tấn công, mô hình Gemini 2.5 Flash đã tuân thủ nghiêm ngặt các ranh giới do chúng tôi thiết lập, vượt qua 100% các bài test của hệ thống.

---

## 🎓 5. Bài Học Rút Ra Cho AI Product Engineer

1. **Kỹ sư AI Product phải là người "cầm cương" AI:** AI rất giỏi tổng hợp thông tin nhưng rất kém trong việc thấu hiểu bối cảnh vận hành thực tế. Giá trị của người kỹ sư nằm ở năng lực phản biện và tinh chỉnh các ý tưởng lý thuyết của AI thành giải pháp khả thi.
2. **Kinh tế học hành vi (Incentive) mạnh hơn thuật toán ép buộc:** Một thuật toán AI xuất sắc đến đâu cũng vô dụng nếu người dùng cuối (tài xế) không muốn tuân theo. Thiết kế cơ chế chia sẻ lợi ích là chìa khóa để AI đi vào cuộc sống.
3. **Ranh giới an toàn quyết định sự sống còn:** Một tính năng AI chỉ được coi là hoàn thiện khi nó có kế hoạch dự phòng (Fallback) và ranh giới vận hành (Operational Boundary) rõ ràng để bảo vệ an toàn cho con người và tài sản doanh nghiệp.
