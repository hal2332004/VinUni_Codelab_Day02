# Lab 02 — Problem Scan & Quick Problem Cards

**Họ và tên** — Nguyễn Trung Hải  
**MSSV** — 2A202603728

Các nguồn công khai dưới đây xác nhận dịch vụ hoặc kênh tiếp nhận liên quan. Điểm nghẽn, quy trình xử lý nội bộ và thời gian ở các card là **giả thuyết cần xác minh**. Các ngưỡng dùng để thiết kế thử nghiệm, chưa phải kết quả đo.

## Phase 1 — Scan

| # | Đơn vị | Lens | Vấn đề cần khảo sát | Dữ liệu cần kiểm tra |
|---:|---|---|---|---|
| 1 | Vinhomes | Tốn thời gian | Khi một yêu cầu hỗ trợ trên Vinhomes Resident mô tả nhiều vấn đề hoặc thiếu chi tiết, nhân viên tiếp nhận có thể phải đọc lại và hỏi thêm để chuyển phiếu đúng bộ phận. [1] | Thời gian phân loại mỗi phiếu, tỷ lệ phiếu phải hỏi lại hoặc chuyển lại. |
| 2 | VinWonders | AI-upgrade | Khi khách hỏi về đổi ngày hoặc hủy vé bằng mô tả tự do, nhân viên có thể phải hỏi lại thông tin và tra điều khoản trước khi trả lời, nhất là khi khách nêu tình huống ngoại lệ. [2] | Số lượt hỏi bổ sung, thời gian soạn phản hồi và tỷ lệ phản hồi đúng điều khoản. |
| 3 | VinFast | Lặp lại | Khi khách muốn thay đổi lịch bảo dưỡng, họ cần hủy lịch cũ và đặt lịch mới theo hướng dẫn công khai. Cần kiểm tra khách có lặp lại việc nhập hoặc tra thông tin đã có khi đặt lại hay không. [3] | Thời gian hoàn thành, trường phải nhập lại và tỷ lệ khách bỏ dở khi đặt lại. |
| 4 | Xanh SM (Green SM) | Khó khăn của người dùng | Khi hành khách báo vấn đề sau chuyến đi nhưng mô tả thiếu thông tin nhận diện chuyến, việc xác minh chuyến có thể làm kéo dài khâu tiếp nhận khiếu nại. [4] | Số lượt hỏi bổ sung và tỷ lệ xác định được chuyến ngay trong lần tiếp nhận đầu. |
| 5 | Vinmec | Lặp lại | Khi khách gọi hỏi cách đặt lịch khám, họ có thể mất thêm một lượt trao đổi nếu chưa biết cơ sở cần liên hệ hoặc kênh đặt lịch trực tuyến. Phạm vi chỉ gồm hướng dẫn hành chính. [5] | Tỷ lệ cuộc gọi phải chuyển đầu mối và số lượt trao đổi để khách tìm đúng kênh đặt lịch. |

## Phase 2 — Quick-Assess

### Card 1 — Phân loại yêu cầu hỗ trợ cư dân

- **Bài toán và đơn vị** — Tại Vinhomes, nhân viên Ban Quản lý có thể mất thời gian xác định bộ phận xử lý khi yêu cầu hỗ trợ chứa nhiều vấn đề hoặc thiếu thông tin.
- **Actor** — Nhân viên tiếp nhận yêu cầu tại Ban Quản lý tòa nhà. Cư dân là người chịu ảnh hưởng nếu phiếu chuyển chậm hoặc nhầm.
- **Quy trình hiện tại cần xác minh**
  1. Cư dân gửi yêu cầu hỗ trợ qua ứng dụng.
  2. Nhân viên đọc nội dung và kiểm tra thông tin cần để xử lý.
  3. Nhân viên hỏi bổ sung nếu thiếu thông tin, rồi chọn bộ phận phụ trách.
  4. Nhân viên chuyển phiếu cho bộ phận đã chọn.
- **Điểm nghẽn** — Bước 2–3 đối với phiếu có mô tả mơ hồ hoặc nhiều vấn đề. Giả thuyết ban đầu để kiểm tra là **3 phút/phiếu** cho phần đọc, phân loại và quyết định có cần hỏi thêm. Đo từ lúc mở phiếu đến khi xác nhận nhãn hoặc quyết định hỏi bổ sung, chưa tính thời gian chờ cư dân trả lời.
- **Điểm can thiệp** — Đề xuất nhãn bộ phận từ nội dung đã loại thông tin định danh và đánh dấu thông tin còn thiếu. Nhân viên duyệt hoặc sửa nhãn trước khi chuyển phiếu. Trường hợp không đủ căn cứ thì tiếp tục phân loại thủ công.
- **Chỉ số mục tiêu** — Giảm ít nhất **30% thời gian phân loại trung vị** so với cách thủ công và đạt **ít nhất 95% nhãn cuối cùng đúng** trên bộ phiếu đã gán nhãn, sau khi nhân viên duyệt. So sánh hai cách trên cùng tập phiếu và tính cả thời gian duyệt.
- **Quick Architecture** — **LLM Feature + Rule**. LLM đọc mô tả tự do. Rule giới hạn nhãn trong danh sách bộ phận được phép. Nếu phần lớn phiếu đã có danh mục rõ, thử đối chiếu với cách định tuyến bằng rule.

### Card 2 — Soạn nháp phản hồi về đổi ngày hoặc hủy vé

- **Bài toán và đơn vị** — Tại VinWonders, nhân viên chăm sóc khách hàng có thể phải đọc lại điều khoản và hỏi bổ sung khi yêu cầu đổi ngày hoặc hủy vé có mô tả thiếu thông tin hoặc nêu ngoại lệ.
- **Actor** — Nhân viên chăm sóc khách hàng phụ trách câu hỏi về vé đã mua.
- **Quy trình hiện tại cần xác minh**
  1. Nhân viên nhận câu hỏi về vé.
  2. Nhân viên kiểm tra loại vé, kênh mua và thông tin liên quan.
  3. Nhân viên đối chiếu điều khoản áp dụng, hỏi bổ sung hoặc chuyển cấp nếu cần.
  4. Nhân viên soạn và gửi phản hồi.
- **Điểm nghẽn** — Bước 3–4. Giả thuyết ban đầu là **5 phút/yêu cầu** cho phần tra điều khoản và soạn phản hồi. Kiểm tra bằng cách đo thời gian xử lý chủ động trên các câu hỏi mẫu, tách riêng ca thường và ca có ngoại lệ.
- **Điểm can thiệp** — Sau khi nhân viên có thông tin cần thiết, LLM soạn nháp từ điều khoản được cung cấp và chỉ rõ điều khoản dùng để trả lời. Thiếu thông tin hoặc có ngoại lệ thì chuyển cho nhân viên xử lý. Nhân viên duyệt trước khi gửi và quyết định mọi thao tác đổi, hủy hoặc hoàn tiền.
- **Chỉ số mục tiêu** — Giảm thời gian xử lý trung vị từ bước tra điều khoản đến khi phản hồi sẵn sàng gửi xuống **không quá 3 phút**, tính cả thời gian duyệt. Ít nhất **95% bản nháp** phù hợp với đáp án đã đối chiếu điều khoản trên bộ câu hỏi mẫu.
- **Quick Architecture** — **LLM Feature + Rule**. Rule xác định điều khoản hoặc chuyển cấp khi dữ liệu có cấu trúc. LLM chỉ chuyển kết quả đó thành bản nháp dễ hiểu. Có thể so sánh với phản hồi theo mẫu cố định trên cùng bộ câu hỏi.

### Card 3 — Giảm thao tác khi đặt lại lịch bảo dưỡng

- **Bài toán và đơn vị** — Tại VinFast, chủ xe có thể phải nhập lại dữ liệu hoặc tra lại thông tin khi hủy và tạo lịch bảo dưỡng mới.
- **Actor** — Chủ xe đã có lịch bảo dưỡng và muốn đổi thời gian.
- **Quy trình hiện tại cần xác minh**
  1. Chủ xe mở lịch bảo dưỡng đã đặt.
  2. Chủ xe hủy lịch cũ theo hướng dẫn công khai.
  3. Chủ xe tạo lịch mới, chọn địa điểm và khung giờ.
  4. Chủ xe gửi yêu cầu mới và chờ xác nhận.
- **Điểm nghẽn** — Bước 3 nếu thông tin xe hoặc loại dịch vụ phải nhập lại. Giả thuyết ban đầu là **2 phút/lượt** cho thao tác tạo lại yêu cầu. Cần quan sát luồng đặt lịch để xác định trường nào đã tự điền và trường nào thực sự bị lặp.
- **Điểm can thiệp** — Trên bản mẫu, điền sẵn thông tin được phép tái sử dụng từ lịch cũ. Chủ xe kiểm tra lại nội dung và chọn giờ mới từ các khung còn trống. Nếu khung giờ vừa hết chỗ, hệ thống yêu cầu chọn lại. Việc hủy lịch vẫn cần xác nhận của chủ xe.
- **Chỉ số mục tiêu** — Giảm ít nhất **30% thời gian thao tác trung vị** từ khi mở lịch cũ đến khi gửi yêu cầu mới, chưa tính thời gian chờ xưởng xác nhận. Kiểm tra **100% ca thử có khung giờ hết chỗ** đều không gửi được yêu cầu với khung giờ đó.
- **Quick Architecture** — **Rule / State Machine**. Luồng có dữ liệu và trạng thái rõ, gồm lịch cũ, thông tin xe, khung giờ và xác nhận của người dùng.

## Đề xuất đưa vào Deep-Dive

**Ưu tiên Card 1.** Bài toán có một bước can thiệp cụ thể là gợi ý bộ phận phụ trách. Nhóm có thể so sánh định tuyến bằng rule và gợi ý bằng LLM trên cùng bộ phiếu đã gán nhãn, với nhân viên kiểm tra trước khi chuyển phiếu. Việc cần làm trước là xác minh quy trình với người phụ trách và thu thập phiếu mẫu đã ẩn thông tin định danh để đo thời gian phân loại và tỷ lệ chuyển đúng hiện tại.

## Nguồn đối chiếu

- [1] [Vinhomes Resident — tính năng gửi yêu cầu hỗ trợ](https://apps.apple.com/vn/app/vinhomes-resident/id6450522818)
- [2] [VinWonders — thông tin đặt vé và điều kiện sử dụng](https://booking.vinwonders.com/contact/)
- [3] [VinFast — đặt lịch dịch vụ bảo dưỡng](https://shop.vinfastauto.com/vn_vi/dat-lich-dich-vu-bao-duong.html)
- [4] [Green SM — kênh tiếp nhận hỗ trợ và khiếu nại](https://www.greensm.com/vn-vi/news/so-dien-thoai-tong-dai-taxi-xanh-sm-tren-toan-quoc-hotline-24-7)
- [5] [Vinmec — hướng dẫn đặt lịch khám](https://www.vinmec.com/vie/bai-viet/huong-dan-dat-lich-kham-tai-vinmec-vi)
