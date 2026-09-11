# Problem Scan — Vin Smart Future

> Bài tập tình huống, chưa khảo sát doanh nghiệp. Mọi thời gian, khối lượng và mục tiêu dưới đây là giả định để scoping, không phải số liệu vận hành đã xác minh.

## 1. Quét cơ hội bằng 4 lenses

| # | Đơn vị | Lens | Bài toán |
|---|---|---|---|
| 1 | Xanh SM | Tốn thời gian | Điều phối viên tra cứu thông tin và soạn hướng dẫn khi tài xế báo pin thấp. |
| 2 | Vinhomes | Lặp lại | Nhân viên đọc và chuyển phản ánh cư dân đến bộ phận phụ trách. |
| 3 | Xanh SM | Pain từ người khác | Nhân viên khó tổng hợp lý do hủy chuyến từ ghi chú tự do của khách và tài xế. |
| 4 | VinFast | Lặp lại | Kế toán đối chiếu bảng phiên sạc với hóa đơn đối tác. |
| 5 | Vinpearl | AI-upgrade | Nhân viên đọc email đặt phòng đoàn, trích ngày ở và số phòng để soạn phản hồi. |

## 2. Ba Quick Problem Cards

### Card 1 — Soạn nháp hỗ trợ sự cố pin Xanh SM

- **Actor:** Điều phối viên; tài xế là người chịu thời gian chờ.
- **Workflow giả định:** Nhận báo cáo (2 phút) → tra GPS/pin (2 phút) → tra trạm phù hợp (5 phút) → soạn hướng dẫn (5 phút) → xác nhận/chuyển đội hỗ trợ khi cần (1 phút).
- **Bottleneck:** Tra cứu và soạn tin, 10/15 phút mỗi lượt.
- **AI hỗ trợ:** Soạn nháp tiếng Việt từ dữ liệu đã xác minh; code xử lý điều kiện pin và dữ liệu trạm.
- **Metric mục tiêu:** Trung vị tổng thời gian từ 15 xuống ≤5 phút/lượt; 100% tin được người duyệt; ≥98% nháp không cần sửa thông tin thực tế trên bộ đánh giá.
- **Architecture:** LLM Feature kết hợp rule; không tự điều xe hoặc gửi tin.

### Card 2 — Phân loại phản ánh cư dân Vinhomes

- **Actor:** Nhân viên CSKH và tổ kỹ thuật tòa nhà.
- **Workflow giả định:** Nhận phản ánh (1 phút) → đọc nội dung (2 phút) → xác định tòa/nhóm xử lý (2 phút) → tạo và chuyển ticket (1 phút).
- **Bottleneck:** Đọc và phân loại mất 4 phút; phản ánh nhiều vấn đề dễ chuyển sai.
- **AI hỗ trợ:** Đề xuất nhãn từ danh sách cho phép; rule tra đơn vị theo tòa.
- **Metric mục tiêu:** Trung vị xử lý từ 6 xuống ≤2 phút; ≥95% nhãn đúng trên 100 mẫu đã gán nhãn.
- **Architecture:** LLM Feature; thiếu tòa hoặc nhiều vấn đề thì chuyển người xem. Không tự trả lời tranh chấp hoặc cam kết bồi thường.

### Card 3 — Phân tích lý do hủy chuyến Xanh SM

- **Actor:** Chuyên viên vận hành.
- **Workflow giả định:** Xuất ghi chú đã ẩn danh (1 phút) → đọc (3 phút) → gán lý do (2 phút) → tổng hợp báo cáo (1 phút), tính trên mỗi ghi chú.
- **Bottleneck:** Đọc và phân loại 5 phút; mô tả không thống nhất.
- **AI hỗ trợ:** Phân loại theo taxonomy cố định và trích câu làm bằng chứng.
- **Metric mục tiêu:** Trung vị từ 7 xuống ≤2 phút/ghi chú; macro-F1 ≥0,90 trên 100 mẫu được người gán nhãn độc lập.
- **Architecture:** LLM Feature xử lý theo lô; không kết luận lỗi tài xế hoặc tự áp dụng chế tài.

## 3. Chọn bài toán

Chọn Card 1 để nối tiếp prototype trong starter code và khảo sát ranh giới rõ ràng: draft, pin thấp, dữ liệu thiếu. Đây là lựa chọn phục vụ bài lab, chưa có bằng chứng lợi ích cao hơn hai bài còn lại.

Card 2 cần taxonomy và danh sách phụ trách tòa. Card 3 có rủi ro thấp hơn và là phương án dự phòng nếu không tiếp cận được dữ liệu sự cố pin. Cả ba cần baseline trước khi cam kết hiệu quả.

## 4. Phản biện quyết định

1. Chưa có dữ liệu chứng minh soạn tin là bottleneck: cần đo riêng thời gian tra cứu và soạn nháp.
2. Mẫu tin cố định có thể giải quyết tốt hơn LLM: phải so sánh với template trên cùng bộ dữ liệu.
3. Tỷ lệ nháp đúng không đủ chứng minh an toàn: cần người kiểm tra nội dung và chặn thực thi độc lập với prompt.
