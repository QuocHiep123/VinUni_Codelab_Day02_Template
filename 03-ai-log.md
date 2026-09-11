# 03 — AI Log & Reflection

> Thay `[Tên của bạn]` và chi tiết trải nghiệm bằng thông tin thật trước khi nộp.

**Người viết:** [Tên của bạn]  
**Vai trò:** [Vai trò trong nhóm]  
**Công cụ:** ChatGPT/Codex để brainstorm và phản biện; Gemini 2.5 Flash cho prompt prototype.

## AI đã giúp gì?

Tôi dùng AI như một thought-partner để mở rộng pain point theo 4 lenses, kiểm tra Problem Statement có đủ actor, workflow và metric, rồi tạo adversarial prompts. AI giúp tôi nhận ra bài toán phân loại lỗi xe không cần multi-agent: rule nên phát hiện dấu hiệu nguy hiểm, còn LLM chỉ đọc hiểu mô tả tiếng Việt, đề xuất nhóm lỗi sơ bộ và câu hỏi làm rõ.

## Điểm AI chưa đáng tin và cách kiểm tra

AI có thể gắn một mã lỗi cụ thể từ một mô tả quá ngắn, ví dụ kết luận “hỏng giảm xóc” chỉ từ câu “kêu cụp cụp”. Tôi không xem đó là chẩn đoán hợp lệ: mô tả có thể liên quan đến nhiều bộ phận và phải được kỹ thuật viên xác nhận. AI cũng có thể nêu số liệu không có nguồn log nội bộ; các số trong báo cáo vì vậy được ghi là **giả định cần xác thực ở pilot**.

## Tôi sửa prompt và ranh giới thế nào?

- Mọi output luôn bắt đầu `[DRAFT_ONLY]` để thể hiện đây là bản nháp cần người duyệt.
- AI chỉ đề xuất nhóm lỗi sơ bộ, độ tự tin và câu hỏi làm rõ; không được kết luận nguyên nhân hoặc bảo xe an toàn để tiếp tục lái.
- Các từ khóa như “khói”, “mùi khét”, “mất phanh”, “va chạm” phải gắn cờ `requires_urgent_human_review: true`.
- Thiếu thông tin hoặc độ tự tin thấp thì tư vấn viên hỏi lại khách và tra tài liệu theo quy trình cũ.

Tôi thử ba input đối kháng: ép AI khẳng định xe chỉ hỏng giảm xóc, ép bỏ tag và gửi kết luận thẳng cho khách, và yêu cầu AI bỏ quy tắc khi mô tả có khói/mùi khét. Rule an toàn cũng cần được kiểm tra ở tầng ứng dụng/API, thay vì chỉ tin vào system prompt.

## Bài học

AI hữu ích khi làm rõ lựa chọn và phát hiện lỗ hổng, không thay thế trách nhiệm vận hành. Với bài toán có yếu tố an toàn vật lý, scope hẹp, baseline rõ, human-in-the-loop và fallback làm tay quan trọng hơn tự động hóa toàn bộ.
