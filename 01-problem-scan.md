# 01 — Problem Scan & Quick Assessment

**Nhóm:** [Điền tên nhóm]  
**Thành viên:** [Điền họ tên]  
**Bối cảnh:** Vin Smart Future phối hợp cùng Xanh SM tại Hà Nội.  
**Lưu ý về dữ liệu:** Thời gian và tỷ lệ dưới đây là giả định để thiết kế pilot; nhóm cần thay bằng số liệu log nội bộ khi có.

## Phase 1 — SCAN

| # | Công ty | Lens | Bài toán vận hành |
|---:|---|---|---|
| 1 | Xanh SM | Tốn thời gian | Điều phối viên xử lý cuộc gọi xe sắp cạn pin: tra GPS, tra trạm sạc và soạn hướng dẫn thủ công. |
| 2 | VinFast | Lặp lại | Nhân viên tài chính đối chiếu hóa đơn sạc của đối tác với log giao dịch hằng tuần. |
| 3 | Vinhomes | AI-upgrade | Phản ánh cư dân gửi tự do qua app bị phân loại/routing chậm, sai ban quản lý tòa nhà. |
| 4 | Vinpearl | Pain từ người khác | Quản lý đọc thủ công review đa nền tảng nên phàn nàn khẩn cấp không được phát hiện kịp thời. |
| 5 | Vinmec | Tốn thời gian | Bác sĩ tổng hợp bệnh án, xét nghiệm và ghi chú để soạn tóm tắt xuất viện. |

## Phase 2 — QUICK-ASSESS

### Card 1 — Xanh SM: xử lý sự cố pin thực địa

| Hạng mục | Nội dung |
|---|---|
| Bài toán | Rút ngắn thời gian đưa ra hướng dẫn an toàn khi tài xế báo pin thấp hoặc hết pin. |
| Actor | Tài xế báo sự cố; điều phối viên xử lý; đội cứu hộ/sạc di động nhận yêu cầu đã duyệt. |
| Workflow hiện tại | (1) Tài xế gọi tổng đài → (2) điều phối viên ghi biển số, mức pin → (3) tra GPS → (4) tra trạm sạc → (5) soạn/gửi hướng dẫn hoặc gọi cứu hộ. |
| Bottleneck | Bước 4–5: tra trạm phù hợp và soạn chỉ dẫn, ước tính 10 trên 15 phút/lượt. |
| AI hỗ trợ | Tạo **bản nháp** chỉ dẫn từ dữ liệu API đã xác thực; điều phối viên kiểm tra và duyệt trước khi gửi. |
| Metric | Giảm thời gian xử lý trung vị từ 15 phút xuống dưới 3 phút; ít nhất 98% bản nháp được duyệt là đúng trạm và đúng cổng sạc. |
| Architecture | **Rule + LLM Feature:** rule kiểm tra pin/khoảng cách; LLM chỉ soạn ngôn ngữ, không dùng agent tự trị. |

### Card 2 — Vinhomes: phân loại phản ánh cư dân

| Hạng mục | Nội dung |
|---|---|
| Bài toán | Điều hướng phản ánh văn bản tự do tới đúng đội vận hành/tòa nhà. |
| Actor | Nhân viên CSKH, ban quản lý tòa, cư dân. |
| Workflow hiện tại | (1) Nhận ticket → (2) đọc nội dung/ảnh → (3) xác định loại việc và độ khẩn → (4) chọn đội xử lý → (5) phản hồi xác nhận. |
| Bottleneck | Phân loại phản ánh mơ hồ và độ khẩn, 4–6 phút/ticket; routing sai tạo thêm handoff. |
| AI hỗ trợ | Đề xuất nhóm xử lý, mức ưu tiên và bản nháp phản hồi; nhân viên duyệt hoặc sửa. |
| Metric | Ít nhất 85% ticket được đề xuất đúng nhóm xử lý trong dưới 10 giây; giảm 30% ticket chuyển sai sau pilot. |
| Architecture | **LLM Feature** + rule từ khóa khẩn; không tự đóng ticket hay cam kết phí. |

### Card 3 — VinFast: đối chiếu hóa đơn sạc đối tác

| Hạng mục | Nội dung |
|---|---|
| Bài toán | So khớp hóa đơn đối tác với log sạc và phát hiện sai lệch để kiểm tra. |
| Actor | Kế toán vận hành, đối tác trạm sạc, quản lý tài chính. |
| Workflow hiện tại | (1) Nhận hóa đơn → (2) chuẩn hóa mã trạm/thời gian → (3) lọc log giao dịch → (4) so khớp → (5) đánh dấu sai lệch. |
| Bottleneck | Chuẩn hóa mã và kiểm tra thủ công, 2–3 ngày mỗi kỳ. |
| AI hỗ trợ | Trích xuất trường từ hóa đơn không chuẩn; so khớp tiền/quyết toán vẫn là rule xác định. |
| Metric | Ít nhất 95% trường được trích xuất chính xác; giảm 50% thời gian chuẩn hóa dữ liệu. |
| Architecture | **Rule-based pipeline** là chính; OCR/LLM chỉ trợ lý trích xuất. |

## Quyết định chọn bài toán

Nhóm chọn **Card 1** vì đầu vào có cấu trúc (GPS, mức pin, loại xe và trạng thái trạm), tác động thời gian thực và rủi ro có thể kiểm soát bằng rule cứng cùng bước duyệt của người vận hành. Card 2 cần pilot dữ liệu ticket; Card 3 chủ yếu phù hợp rule-based hơn AI.
