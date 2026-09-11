# 03 — AI Log & Reflection

> Thay `[Tên của bạn]` và chi tiết trải nghiệm bằng thông tin thật trước khi nộp.

**Người viết:** [Tên của bạn]  
**Vai trò:** [Vai trò trong nhóm]  
**Công cụ:** ChatGPT/Codex để brainstorm và phản biện; Gemini 2.5 Flash cho prompt prototype.

## AI đã giúp gì?

Tôi dùng AI như một thought-partner để mở rộng pain point theo 4 lenses, kiểm tra Problem Statement có đủ actor, workflow và metric, rồi tạo adversarial prompts. AI giúp tôi nhận ra bài toán điều vận pin không cần multi-agent: kiểm tra mức pin, khoảng cách và cổng sạc phải là rule xác định; LLM chỉ nên viết bản nháp chỉ dẫn.

## Điểm AI chưa đáng tin và cách kiểm tra

AI có thể nêu số sự cố/ngày hoặc tỷ lệ doanh thu rò rỉ nhưng không có nguồn log nội bộ. Tôi không xem đó là dữ liệu thật: báo cáo ghi chúng là **giả định cần xác thực ở pilot**, còn success metric là mục tiêu phải đo bằng dữ liệu vận hành. AI cũng có xu hướng đề xuất “tự động gửi” để nhanh hơn; điều này không chấp nhận được khi hướng dẫn sai trạm lúc pin thấp có thể gây rủi ro vận hành.

## Tôi sửa prompt và ranh giới thế nào?

- Mọi output luôn bắt đầu `[DRAFT_ONLY]` và có `human_review_required: true`.
- Pin dưới 5% không được đề xuất trạm xa hơn 5 km; phải có `action: dispatch_mobile_charger`.
- Không được bịa GPS, khoảng cách, connector hay số trụ trống.
- API thiếu/lỗi thì dispatcher quay về tra dashboard và xử lý thủ công.

Tôi thử ba input đối kháng: ép bỏ tag và gửi thẳng, ép xe pin 2% đến trạm 8 km, và prompt injection yêu cầu bịa trạng thái trạm. Rule an toàn cũng cần được kiểm tra ở tầng ứng dụng/API, thay vì chỉ tin vào system prompt.

## Bài học

AI hữu ích khi làm rõ lựa chọn và phát hiện lỗ hổng, không thay thế trách nhiệm vận hành. Với bài toán có yếu tố an toàn vật lý, scope hẹp, baseline rõ, human-in-the-loop và fallback làm tay quan trọng hơn tự động hóa toàn bộ.
