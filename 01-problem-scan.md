# 01 — Problem Scan & Quick Cards (luongvh)

> Con số bên dưới là **ước tính của cá nhân để scoping**, chưa phải số liệu nội bộ Vingroup đã kiểm chứng.

---

# 🔍 Phase 1 — SCAN

| # | Subsidiary | Lens | Mô tả ngắn bài toán |
|---|------------|------|---------------------|
| 1 | **Xanh SM** | Pain từ người khác | Tài xế khiếu nại cước phí/thu nhập bị tính sai hoặc thiếu; nhân viên hỗ trợ phải tra thủ công lịch sử chuyến (GPS, giá, khuyến mãi) rồi soạn phản hồi (~15 phút/khiếu nại, tài xế chờ 1-2 ngày). AI/rule đối chiếu dữ liệu chuyến, AI soạn nháp giải thích, nhân viên duyệt trước khi gửi. |
| 2 | **VinFast** | Lặp lại | Bộ phận tài chính đối chiếu hằng tuần dữ liệu sạc từ trạm đối tác với hóa đơn; hàng nghìn dòng, lệch số liệu phải tra tay. AI/rule gom các dòng lệch và giải thích nguyên nhân khả dĩ. |
| 3 | **Vinhomes** | Tốn thời gian | Ban quản lý đọc từng phản ánh của cư dân trên app (mất nước, hỏng đèn, ồn ào), phân loại và chuyển đúng bộ phận; phản hồi thường chậm vài giờ. AI phân loại, gắn mức khẩn và soạn nháp phản hồi. |
| 4 | **Vinmec** | AI có thể tốt hơn | Bác sĩ tự viết tóm tắt hồ sơ xuất viện từ bệnh án, xét nghiệm và ghi chú (~20-30 phút/bệnh nhân). AI trích xuất và soạn bản nháp để bác sĩ sửa và ký duyệt. |
| 5 | **Vinpearl** | Tốn thời gian | Nhân viên đặt phòng đọc email đặt đoàn từ công ty lữ hành (số phòng, ngày, yêu cầu riêng) rồi tra quỹ phòng và soạn báo giá. AI trích xuất yêu cầu và soạn nháp báo giá, nhân viên duyệt. |

**Ghi chú lens:** #1 và #4 chạm nhiều lens cùng lúc; lens chính được ghi ở cột 3.

---

# 🃏 Phase 2 — QUICK-ASSESS: 3 Quick Problem Cards

Top 3 chọn từ bảng SCAN: **#1 (Xanh SM — khiếu nại cước phí tài xế), #3 (Vinhomes — phản ánh cư dân), #4 (Vinmec — tóm tắt xuất viện).**

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                       │
│                                                             │
│ Bài toán: Tài xế Xanh SM khiếu nại cước phí/thu nhập bị     │
│ tính sai; nhân viên hỗ trợ tra lịch sử chuyến thủ công.     │
│ Công ty thành viên: [x] Xanh SM                             │
│                                                             │
│ Ai đang đau (Actor)? Tài xế (mất thu nhập, chờ 1-2 ngày),   │
│ Nhân viên hỗ trợ tài xế (quá tải, lặp lại nhiều lần/ngày).  │
│                                                             │
│ Workflow thủ công hiện tại:                                 │
│   1. Tài xế gửi khiếu nại qua app/hotline                   │
│   → 2. Nhân viên mở hệ thống, tìm đúng chuyến xe            │
│   → 3. Đối chiếu quãng đường GPS, giá, phụ phí, khuyến mãi  │
│   → 4. Xác định sai ở đâu, tính lại chênh lệch              │
│   → 5. Soạn phản hồi giải thích/xin duyệt hoàn tiền         │
│                                                             │
│ Bước tốn thời gian/lỗi nhất: Bước 3-4 (⏱ ~10-15 phút/lượt,  │
│ dễ tính sót phụ phí hoặc khuyến mãi)                        │
│ AI nhảy vào: Bước 3-5 — rule tính lại cước từ dữ liệu       │
│ chuyến; LLM soạn NHÁP phản hồi dễ hiểu. Người duyệt mọi     │
│ khoản hoàn tiền; AI không tự quyết định hoàn tiền.          │
│                                                             │
│ Metric: Thời gian xử lý 1 khiếu nại từ ~15 phút ──> < 4     │
│ phút; thời gian phản hồi tài xế từ 1-2 ngày ──> < 4 giờ.    │
│                                                             │
│ Quick Architecture: [ ] No AI  [x] Rule  [x] LLM  [ ] Agent │
│ (Rule tính lại cước chính xác, LLM chỉ soạn lời giải thích) │
└─────────────────────────────────────────────────────────────┘
```

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                       │
│                                                             │
│ Bài toán: Ban quản lý Vinhomes phân loại, chuyển bộ phận    │
│ và phản hồi thủ công từng phản ánh của cư dân trên app.     │
│ Công ty thành viên: [x] Vinhomes                            │
│                                                             │
│ Ai đang đau (Actor)? Cư dân (chờ phản hồi lâu), nhân viên   │
│ ban quản lý/CSKH (quá tải, phản hồi rập khuôn).             │
│                                                             │
│ Workflow thủ công hiện tại:                                 │
│   1. Cư dân gửi phản ánh qua app                            │
│   → 2. Nhân viên đọc và phân loại (điện/nước/ồn/an ninh)    │
│   → 3. Chuyển đúng bộ phận kỹ thuật/an ninh                 │
│   → 4. Soạn phản hồi cho cư dân                             │
│   → 5. Theo dõi đến khi xử lý xong                          │
│                                                             │
│ Bước tốn thời gian/lỗi nhất: Bước 2-3 (⏱ ~5-10 phút/phản   │
│ ánh, dễ chuyển nhầm bộ phận, phản hồi chậm vài giờ)         │
│ AI nhảy vào: Bước 2-4 — phân loại, gắn mức khẩn, soạn nháp  │
│ phản hồi; nội dung liên quan phí/pháp lý thì chuyển người.  │
│                                                             │
│ Metric: 85% phản ánh được phân loại đúng bộ phận trong      │
│ < 30 giây; thời gian phản hồi đầu tiên giảm ~12h ──> < 1h.  │
│                                                             │
│ Quick Architecture: [ ] No AI  [x] Rule  [x] LLM  [ ] Agent │
│ (Rule cho từ khóa khẩn cấp, LLM cho phân loại/soạn nháp)    │
└─────────────────────────────────────────────────────────────┘
```

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                       │
│                                                             │
│ Bài toán: Bác sĩ Vinmec tự viết tóm tắt hồ sơ xuất viện từ  │
│ bệnh án, xét nghiệm và ghi chú, tốn nhiều thời gian.        │
│ Công ty thành viên: [x] Vinmec                              │
│                                                             │
│ Ai đang đau (Actor)? Bác sĩ điều trị (quá tải giấy tờ),     │
│ bệnh nhân (chờ làm thủ tục xuất viện lâu).                  │
│                                                             │
│ Workflow thủ công hiện tại:                                 │
│   1. Bác sĩ mở bệnh án điện tử, đọc lại diễn biến           │
│   → 2. Tổng hợp kết quả xét nghiệm, thuốc, chẩn đoán        │
│   → 3. Viết tóm tắt xuất viện                               │
│   → 4. Dặn dò, hẹn tái khám                                 │
│   → 5. Ký duyệt và in/gửi bệnh nhân                         │
│                                                             │
│ Bước tốn thời gian/lỗi nhất: Bước 2-3 (⏱ ~20-30 phút/bệnh  │
│ nhân, dễ sót thông tin khi tổng hợp thủ công)               │
│ AI nhảy vào: Bước 2-3 — trích xuất dữ kiện và soạn NHÁP;    │
│ bác sĩ bắt buộc đọc, sửa và ký. AI không tự kê thuốc/chẩn   │
│ đoán.                                                       │
│                                                             │
│ Metric: Giảm thời gian soạn từ ~25 phút ──> dưới 8 phút;    │
│ 0 lỗi sót thuốc/dị ứng sau khi bác sĩ duyệt.                │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘
```

---

## 🗳️ Đề xuất bài toán cho Deep-Dive của nhóm

**Card #1 (Xanh SM — khiếu nại cước phí tài xế)**, vì dữ liệu chuyến có cấu trúc (tính lại bằng rule được, ít rủi ro bịa số), rủi ro kiểm soát được bằng HITL ở khâu duyệt hoàn tiền. Card #3 (Vinmec) rủi ro y tế cao nên cần nhiều ràng buộc hơn; Card #2 (Vinhomes) có yếu tố pháp lý/phí cần chuyển người.
