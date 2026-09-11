# 01 — Problem Scan & Quick Assessment

**Nhóm:** [Điền tên nhóm]  
**Thành viên:** [Điền họ tên]  
**Bối cảnh:** Vin Smart Future phối hợp cùng VinFast Care.
**Lưu ý:** Các thời gian và tỷ lệ là giả định để thiết kế pilot; nhóm cần xác thực lại bằng dữ liệu nội bộ.

## Phase 1 — SCAN

| # | Công ty | Lens | Bài toán vận hành |
|---:|---|---|---|
| 1 | Vinpearl | Pain từ người khác | Review tiêu cực khẩn cấp không được phát hiện kịp thời do quản lý đọc thủ công. |
| 2 | VinFast | Tốn thời gian | Tư vấn viên tra tình trạng trạm sạc, loại cổng và quãng đường rồi soạn hướng dẫn sạc cho chủ xe. |
| 3 | VinFast | Lặp lại | Kế toán đối chiếu hóa đơn sạc từ đối tác với log giao dịch định kỳ. |
| 4 | Vinhomes | Lặp lại | Phản ánh cư dân bị chuyển sai đội vận hành vì nội dung tự do và thiếu thông tin. |
| 5 | VinFast | AI có thể tốt hơn | Khách hàng mô tả tiếng Việt, ví dụ: “xe đi qua gờ giảm tốc kêu cụp cụp ở bánh trước”; hệ thống đề xuất phân loại mã lỗi kỹ thuật ban đầu. |

## Phase 2 — QUICK-ASSESS

### Card 1 — VinFast: phân loại mã lỗi kỹ thuật ban đầu

| Hạng mục | Nội dung |
|---|---|
| Bài toán | Từ mô tả tiếng Việt của khách, đề xuất **nhóm mã lỗi kỹ thuật ban đầu** và câu hỏi làm rõ để tư vấn viên chuyển đúng nhóm kỹ thuật. Ví dụ: “xe đi qua gờ giảm tốc kêu cụp cụp ở bánh trước”. |
| Actor | Khách hàng VinFast gửi mô tả; tư vấn viên VinFast Care kiểm tra và chuyển yêu cầu; kỹ thuật viên nhận ticket đã duyệt. |
| Workflow hiện tại | (1) Nhận cuộc gọi/ticket → (2) đọc mô tả tự do → (3) hỏi thêm triệu chứng → (4) tra tài liệu và chọn nhóm lỗi → (5) chuyển xưởng/chuyên gia. |
| Bottleneck | Bước 2–4 mất khoảng 8 phút/ticket; mô tả mơ hồ hoặc từ địa phương dễ khiến ticket chuyển sai nhóm. |
| AI hỗ trợ | Đề xuất 1–3 nhóm mã lỗi có khả năng nhất, mức độ tự tin, câu hỏi làm rõ và bản tóm tắt cho kỹ thuật viên. Tư vấn viên phải duyệt trước khi chuyển ticket. |
| Metric | ≥85% ticket được tư vấn viên duyệt đúng nhóm lỗi ngay lần đầu; thời gian phân loại trung vị từ 8 phút xuống dưới 2 phút; 0 ticket được AI tự chẩn đoán hoặc tự đặt lịch sửa chữa. |
| Architecture | **Rule + LLM Feature:** rule nhận diện tình huống khẩn cấp; LLM phân loại ngôn ngữ và tạo bản nháp, không dùng agent tự trị. |

### Card 2 — VinFast: trợ lý hướng dẫn sạc an toàn

| Hạng mục | Nội dung |
|---|---|
| Bài toán | Rút ngắn thời gian tư vấn khi chủ xe cần tìm phương án sạc phù hợp và an toàn. |
| Actor | Chủ xe VinFast, tư vấn viên VinFast Care, đội hỗ trợ sạc di động. |
| Workflow hiện tại | (1) Nhận yêu cầu → (2) xác thực GPS/mức pin/loại xe → (3) tra trạm/cổng sạc → (4) soạn hướng dẫn → (5) chuyển hỗ trợ khi pin nguy cấp. |
| Bottleneck | Bước 3–4 mất khoảng 10/15 phút/lượt. |
| AI hỗ trợ | Tạo bản nháp hướng dẫn từ dữ liệu API đã xác thực; tư vấn viên kiểm tra và duyệt trước khi gửi. |
| Metric | Giảm thời gian xử lý trung vị từ 15 xuống dưới 3 phút. |
| Architecture | Rule + LLM Feature. |

### Card 3 — VinFast: đối chiếu hóa đơn sạc đối tác

| Hạng mục | Nội dung |
|---|---|
| Bài toán | So khớp hóa đơn đối tác với log sạc và phát hiện sai lệch để kế toán kiểm tra. |
| Actor | Kế toán vận hành, đối tác trạm sạc, quản lý tài chính. |
| Workflow hiện tại | (1) Nhận hóa đơn → (2) chuẩn hóa mã trạm/thời gian → (3) lọc log → (4) so khớp → (5) đánh dấu sai lệch. |
| Bottleneck | Chuẩn hóa mã và kiểm tra thủ công, khoảng 2–3 ngày mỗi kỳ. |
| AI hỗ trợ | Trích xuất trường từ hóa đơn không chuẩn; so khớp tiền vẫn dùng rule xác định. |
| Metric | ≥95% trường được trích xuất chính xác; giảm 50% thời gian chuẩn hóa. |
| Architecture | Rule-based pipeline là chính; OCR/LLM chỉ trợ lý trích xuất. |

## Quyết định chọn bài toán

Nhóm chọn **Card 1 — Phân loại mã lỗi kỹ thuật ban đầu**. Bài toán có lượng ngôn ngữ tự do lớn nên LLM có lợi thế hơn form/rule thuần túy; đồng thời rủi ro có thể kiểm soát vì AI chỉ đề xuất nhóm lỗi, không chẩn đoán, không kết luận nguyên nhân và không chỉ dẫn sửa xe. Card 2 phù hợp để làm sau; Card 3 chủ yếu phù hợp rule-based hơn AI.
