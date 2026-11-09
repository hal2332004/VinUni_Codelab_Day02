# Lab 02 — Báo cáo phân tích sâu

## Lập lịch sạc và điều hướng chống tắc nghẽn trạm sạc

**Smart EV Fleet Charging Scheduling · Đề tài nhóm**

### 1. Phạm vi và bằng chứng

Nhóm đề xuất hỗ trợ điều phối đội taxi điện bằng cách gợi ý **xe nào nên sạc, tại trạm nào và vào khung giờ nào**. Phạm vi thử nghiệm là một cụm trạm ở Hà Nội, trên xe sắp cần sạc và đang rảnh hoặc đã trả khách. Thử nghiệm bắt đầu bằng phát lại dữ liệu lịch sử hoặc mô phỏng, sau đó mới cân nhắc chạy song song với quy trình thật để quan sát.

Trang V-Green xác nhận mạng lưới và bản đồ trạm sạc công cộng. VinFast có tài liệu mô tả cách nhận biết trụ khả dụng và theo dõi phiên sạc. Những tài liệu này xác nhận bối cảnh dịch vụ. Quyền truy cập dữ liệu BMS, trạng thái từng cổng theo thời gian thực, lượng khách ngoài đội xe và khả năng giữ chỗ vẫn cần xác nhận với đơn vị vận hành. [V-Green](https://vgreen.net/vi/tram-sac-o-to-dien) · [VinFast](https://vinfastauto.com/vn_vi/node/6997)

### 2. Quy trình hiện tại và điểm nghẽn

Đây là **quy trình giả định cần đối chiếu với tài xế và điều phối viên**. Theo kịch bản của nhóm, tài xế thường bắt đầu tìm nơi sạc khi SOC xuống dưới 20%. Mốc này dùng để mô tả kịch bản, còn ngưỡng năng lượng an toàn phải theo cấu hình được phê duyệt cho từng dòng xe.

| Bước | Người hoặc hệ thống thực hiện | Công việc và điểm chuyển giao | Thời gian minh họa |
|---|---|---|---:|
| 1 | Tài xế, màn hình xe | Màn hình xe hiển thị SOC. Tài xế quyết định tìm trạm khi xe cần sạc. | ~2 phút |
| 2 | Tài xế, ứng dụng hoặc bản đồ | Ứng dụng cung cấp danh sách trạm. Tài xế chọn trạm dựa trên vị trí và thông tin có tại thời điểm tra cứu. | ~3 phút |
| 3 | Tài xế | Di chuyển tới trạm đã chọn. Mức sử dụng trạm có thể thay đổi trước khi xe đến. | ~8 phút |
| 4 | Tài xế, trạm sạc | Kiểm tra cổng tương thích. Nếu hết cổng, tài xế xếp hàng hoặc quay lại bước chọn trạm nếu còn đủ điều kiện di chuyển. | **30–50 phút chờ tại hub đông** |
| 5 | Tài xế, xe và trụ sạc | Bắt đầu phiên sạc khi có cổng và hệ thống xe/trụ cho phép. Khi kết thúc, tài xế quay lại phục vụ. | Phụ thuộc phiên sạc |

Điểm nghẽn nằm ở **bước 4**. Nhiều xe có thể cùng chọn hub quen thuộc trong khi các trạm khác còn công suất. Với nhánh chờ tại hub, thời gian từ lúc bắt đầu tìm trạm đến lúc có cổng là **43–63 phút/lượt** theo các giả định trên. Mốc này chưa tính thời gian sạc và phần đường vòng nếu đổi trạm. Sơ đồ tương ứng nằm trong [04-workflow-diagram.png](04-workflow-diagram.png).

### 3. Problem Statement · Sáu trường

| Trường | Phân tích của nhóm |
|---|---|
| **Actor / Operator** | Tài xế cần sạc là người ra quyết định tại xe. Điều phối viên theo dõi đội xe, duyệt đề xuất và xử lý ngoại lệ. |
| **Current Workflow** | Tài xế xem SOC, chọn trạm, di chuyển, kiểm tra cổng khi tới nơi rồi chờ hoặc chọn lại. Sau phiên sạc, xe quay lại phục vụ. |
| **Bottleneck** | Quyết định của từng xe có thể dồn vào cùng một trạm. Trạng thái trạm lúc tra cứu có thể khác lúc xe đến. |
| **Business Impact** | Chờ và đi vòng làm tăng thời gian xe rời phục vụ, đặc biệt ở các khung giờ nhu cầu cao. Cần đo thêm cuốc thực sự bị bỏ lỡ và chi phí vận hành để quy đổi tác động tài chính. |
| **Success Metric** | Mục tiêu pilot là giảm **ít nhất 25% P90 thời gian chờ**, giảm **ít nhất 10% thời gian rời phục vụ trung bình**, và **0 đề xuất vi phạm ràng buộc an toàn cứng** trong bộ kiểm thử. |
| **Operational Boundary** | Hệ thống chỉ đề xuất trạm và khung giờ. Điều phối viên duyệt trước khi gửi gợi ý. Chỉ hiển thị đã giữ chỗ sau khi nhà vận hành trạm xác nhận. Hệ thống lập lịch không gửi lệnh điều khiển xe, thay đổi giới hạn BMS hoặc buộc tài xế sạc. |

#### Kiểm tra các ước tính tác động

| Đầu vào của nhóm | Cách dùng trong báo cáo |
|---|---|
| **30–50 phút tại hub và 35–45 phút/xe/ngày** | Giả thuyết cần đo. Hai con số mô tả hai mẫu số khác nhau. Mốc đầu là phút chờ của một lượt tại hub đông, mốc sau là phút chờ tích lũy trên một xe trong một ngày. |
| **25.000 xe hoạt động** | Nếu mọi xe đều phát sinh 35–45 phút chờ/ngày, kịch bản cho **14.583–18.750 giờ xe/ngày**. Ở mốc 40 phút là khoảng **16.667 giờ xe/ngày**. Nếu chỉ một phần đội xe chịu tắc nghẽn, dùng số xe thực sự chịu ảnh hưởng. |
| **~20.000 cuốc/ngày và 60–90 tỷ đồng/năm** | Chưa đủ dữ liệu để kết luận. Với 15.000 giờ xe và 20.000 cuốc, phép quy đổi ngầm dùng 45 phút/cuốc và giả định toàn bộ thời gian lấy lại đều chuyển thành cuốc có khách. Cần đo tỷ lệ giờ lấy lại rơi vào lúc có cầu, số cuốc tăng thêm, phần doanh thu hoặc biên đóng góp trên mỗi cuốc và số ngày vận hành. |
| **Pin suy hao nhanh hơn 10–15%** | Chưa có căn cứ áp dụng cho đội xe này. Loại pin, chế độ sạc, nhiệt độ và cách đo tuổi thọ đều ảnh hưởng đến kết quả. Pilot ngắn hạn theo dõi cảnh báo nhiệt và số đề xuất bị BMS chặn, chưa quy đổi thành phần trăm tuổi thọ hay lợi ích tài chính. [Nghiên cứu thực nghiệm về sạc nhanh và tuổi thọ pin](https://www.sciencedirect.com/science/article/pii/S0306261920317268) |

### 4. AI Fit và quy trình đề xuất

| Phương án | Vai trò trong thiết kế |
|---|---|
| **Rule / State Machine** | Dùng làm baseline và fallback. Lọc trạm theo khả năng tới nơi, loại cổng và trạng thái được xác minh, rồi chọn trạm khả dụng gần nhất theo ETA. Dễ giải thích nhưng có thể dồn nhiều xe vào một trạm. |
| **Dự báo hàng đợi + MILP** | Phương án nhóm đề xuất. Mô hình dự báo thời gian chờ tại thời điểm đến từ trạng thái cổng, các phiên đang sạc và lưu lượng dự kiến. MILP phân bổ nhiều xe vào các trạm và khung giờ có công suất, đồng thời cân đối thời gian đi, chờ và sạc. Một nghiên cứu về đội xe gọi chuyến sử dụng tối ưu nguyên hỗn hợp có xét tắc nghẽn. Kết quả nghiên cứu đó không phải số liệu của nhóm. [Ma, Connors và Viti](https://orbilu.uni.lu/handle/10993/63841) |
| **LLM Feature / Agentic Loop** | Ngoài phạm vi pilot. Lõi bài toán dùng dữ liệu có cấu trúc và cần nghiệm đáp ứng ràng buộc. Giải thích đề xuất cho điều phối viên có thể dùng mẫu câu từ ETA, thời gian chờ dự báo và lý do lọc trạm. |

**Luồng tương lai.** Nhận SOC, vị trí, trạng thái xe và cờ giới hạn sạc qua nguồn dữ liệu chỉ đọc được cấp quyền. Nhận trạng thái cổng và dấu thời gian từ phía trạm nếu có tích hợp. Lọc phương án không khả thi, dự báo hàng đợi khi đến, giải MILP theo cửa sổ cuốn, rồi hiển thị trạm và giờ đề xuất để **điều phối viên duyệt**. Tài xế nhận gợi ý và có thể từ chối. Khi cổng hỏng, xe chậm hoặc nhu cầu thay đổi, tính lại các lịch chưa bắt đầu và giữ các phiên sạc đang diễn ra.

**Mô hình tối ưu ở mức prototype.** Biến nhị phân (x_{i,s,t}) bằng 1 khi xe (i) được đề xuất bắt đầu sạc tại trạm (s) ở khung (t). Với mỗi xe đến hạn sạc trong cửa sổ, mô hình chọn một lịch hoặc đánh dấu ngoại lệ. Hàm mục tiêu trước hết giảm số xe đến hạn chưa có lịch, sau đó giảm tổng phút xe rời phục vụ, gồm thời gian đi tới trạm, chờ và sạc dự kiến. Tính thời gian chờ từ ETA tới giờ bắt đầu đề xuất, có xét hàng đợi dự báo của khách ngoài đội để tránh cộng trùng thời gian chờ. Mỗi phiên chiếm công suất qua toàn bộ các khung liên tiếp của phiên, sau khi tính các cổng đang bận và nhu cầu khách ngoài đội. Chỉ xét cổng tương thích, phương án xe tới nơi còn mức năng lượng dự phòng theo chính sách được duyệt, xe không đang chở khách, và BMS/xe cho phép sạc. Xe không có phương án hợp lệ được đưa vào danh sách ngoại lệ cho điều phối viên.

**Fallback.** Khi dữ liệu cổng đã cũ hoặc thiếu, mất dữ liệu SOC, solver hết thời gian hoặc không tìm được phương án khả thi, hệ thống báo rõ lý do và chuyển sang điều phối thủ công. Rule chọn trạm gần chỉ chạy khi vẫn xác minh được dữ liệu và các điều kiện cứng. Khi có cảnh báo nhiệt hoặc pin nghiêm trọng, chuyển điều phối viên xử lý theo quy trình của đội xe và hướng dẫn của nhà sản xuất.

### 5. Thiết kế kiểm chứng và quyết định

**So sánh trên cùng kịch bản** với cùng tập xe, nhu cầu sạc và sự kiện trạm. So sánh quy trình hiện tại nếu có log, rule chọn trạm gần đủ điều kiện và phương án dự báo + MILP. Trong mô phỏng, cho mỗi phương án cập nhật lại hàng đợi theo chính quyết định của nó. Tách ngày dùng để xây dự báo khỏi ngày đánh giá, đồng thời xem riêng giờ cao điểm và tình huống cổng hỏng.

| KPI | Định nghĩa và mục tiêu |
|---|---|
| **P90 thời gian chờ** | Thời gian từ lúc đến trạm tới khi bắt đầu sạc, tính trên các lượt thực sự bắt đầu sạc. Mục tiêu giảm ≥25% so với baseline, với điều kiện tỷ lệ bỏ chờ không tăng. Ghi riêng lượt đổi trạm hoặc bỏ chờ để theo dõi sai lệch do các lượt chưa hoàn tất. |
| **Thời gian rời phục vụ** | Từ lúc xe chuyển sang đi sạc đến lúc sẵn sàng nhận cuốc trở lại, gồm đường vòng, chờ và phiên sạc. Mục tiêu giảm trung bình ≥10%. |
| **Vi phạm ràng buộc cứng** | Đếm đề xuất có cổng không tương thích, xe không đủ dự phòng tới trạm hoặc trái tín hiệu được phép sạc. Mục tiêu 0 trong tập kiểm thử. |
| **Chỉ số theo dõi thêm** | Tỷ lệ xe không được phân lịch, số lần đổi trạm, tỷ lệ tài xế chấp nhận, lỗi dự báo thời gian chờ và thời gian giải MILP. |

| AI Readiness Checklist | Trạng thái |
|---|---|
| Dữ liệu sạch, có thể ghép giữa xe và trạm | **Chưa xác nhận.** Cần log SOC, vị trí, dấu thời gian đến/bắt đầu/kết thúc sạc, cổng khả dụng và thông tin khách ngoài đội ở mức phù hợp. |
| Rủi ro có thể kiểm soát | **Đã thiết kế bước duyệt và fallback.** Cần thử ca dữ liệu cũ, cổng hỏng, SOC thiếu và solver không có nghiệm. |
| Người vận hành sẵn sàng sử dụng | **Chưa xác nhận.** Cần trao đổi với tài xế, điều phối viên, đội phụ trách BMS và đơn vị vận hành trạm. |

**Quyết định — NOT YET đối với pilot trực tiếp.** Nhóm có thể bắt đầu mô phỏng và đo baseline. Chỉ chuyển sang thử nghiệm có giám sát khi xác nhận quyền truy cập dữ liệu, quy trình duyệt và ngưỡng an toàn, đồng thời đạt các KPI trên tập đánh giá. Nếu rule đạt chất lượng tương đương với chi phí vận hành thấp hơn, ưu tiên rule.
