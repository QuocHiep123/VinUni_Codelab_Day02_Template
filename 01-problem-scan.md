# 01 — Problem Scan & Quick Assessment

## Thành viên nhóm

| Họ và tên | Mã sinh viên |
|---|---|
| Đặng Quốc Hiệp | 2A202602755 |
| Nguyễn Hoàng Lê Nguyên | 2A202602472 |
| Giang Thế Vũ | 2A202602478 |
| Đỗ Mạnh Đoan | 2A202602839 |
| Nguyễn Mạnh Cường | 2A202602823 |

**Bài toán được chọn:** Xanh SM Battery Incident Safety Co-pilot
**Bối cảnh pilot:** Trung tâm điều vận Xanh SM, phạm vi sự cố pin xe taxi điện.

> **Lưu ý về dữ liệu:** Đây là bài scoping dựa trên tình huống lab và tham khảo ý tưởng từ `origin/nguyen`, `origin/vu`, `origin/nmc`. Mọi thời gian, khối lượng và tỷ lệ bên dưới là **giả định cần xác minh bằng log vận hành**, không phải số liệu Xanh SM đã công bố.

## Phase 1 — SCAN

Rubric yêu cầu quét tối thiểu 5 problems. Bảng này chỉ dùng để so sánh; sau Phase 2, toàn bộ bài chỉ phát triển **một** ý tưởng được chọn.

| # | Công ty | Lens | Bài toán vận hành |
|---:|---|---|---|
| 1 | **Xanh SM** | Tốn thời gian | Dispatcher phải tra GPS, mức pin, thông tin xe/trạm và soạn hướng dẫn khi tài xế báo pin thấp. |
| 2 | Vinhomes | Lặp lại | Nhân viên đọc phản ánh cư dân dạng tự do và route sang ban quản lý phù hợp. |
| 3 | Xanh SM | Stakeholder Pain | Chuyên viên vận hành đọc ghi chú không đồng nhất để phân loại nguyên nhân hủy chuyến. |
| 4 | VinFast | Lặp lại | Kế toán đối chiếu hóa đơn sạc của đối tác với log giao dịch. |
| 5 | VinFast | AI-upgrade | QA phân loại ticket bảo hành và tìm cụm mô tả lỗi tương tự để đề xuất review sớm. |

## Phase 2 — Ba Quick Problem Cards

### Card 1 — Xanh SM Battery Incident Safety Co-pilot

| Trường | Nội dung |
|---|---|
| **Bài toán** | Rút ngắn thời gian tạo hướng dẫn an toàn khi tài xế báo pin thấp hoặc hết pin. |
| **Actor** | Dispatcher là người xử lý; tài xế là người chờ hướng dẫn; đội hỗ trợ chỉ nhận yêu cầu đã duyệt. |
| **Workflow hiện tại** | Nhận báo cáo → ghi nhận xe/pin → tra GPS → tra dữ liệu trạm → soạn hướng dẫn hoặc chuyển hỗ trợ. |
| **Bottleneck** | Tra phương án và soạn phản hồi, giả định chiếm 8 trên tổng 15 phút/lượt. |
| **AI hỗ trợ** | LLM chỉ soạn `[DRAFT_ONLY]` từ dữ kiện đã xác minh; rule xử lý pin, dữ liệu thiếu và quyền phê duyệt. |
| **Metric pilot** | Median handling time ≤5 phút; ≥98% output hợp schema; 100% ca critical được block; 100% phải có người duyệt. |
| **Architecture** | **Rule + LLM Feature + Human-in-the-loop**, không dùng Agent tự trị. |

### Card 2 — Vinhomes Complaint Router

| Trường | Nội dung |
|---|---|
| **Bài toán** | Đề xuất nhãn và đội xử lý cho phản ánh cư dân dạng text. |
| **Actor** | Nhân viên CSKH, ban quản lý tòa nhà và cư dân. |
| **Workflow hiện tại** | Nhận ticket → đọc nội dung → xác định tòa/loại việc → chọn đội xử lý → phản hồi. |
| **Bottleneck** | Phân loại phản ánh mơ hồ; routing sai tạo thêm handoff. |
| **AI hỗ trợ** | Đề xuất nhãn từ allowlist; rule map tòa nhà và escalation. |
| **Metric pilot** | ≥85% đúng đội xử lý; giảm 30% ticket chuyển sai; cần dataset đã gán nhãn. |
| **Architecture** | LLM Feature + rule routing + HITL. |

### Card 3 — VinFast Warranty Cluster Reviewer

| Trường | Nội dung |
|---|---|
| **Bài toán** | Phân loại ticket bảo hành và gắn cờ cụm triệu chứng tương tự cho QA review. |
| **Actor** | CS Agent và QA Engineer. |
| **Workflow hiện tại** | Nhận mô tả lỗi → phân loại → xử lý từng case → QA tổng hợp định kỳ. |
| **Bottleneck** | Mô tả tự do khó gom nhóm; tín hiệu lặp có thể được phát hiện muộn. |
| **AI hỗ trợ** | LLM phân loại/tóm tắt; rule đếm ngưỡng; QA là người kết luận. |
| **Metric pilot** | Macro-F1 ≥0,90; cảnh báo trong ≤48 giờ sau khi đủ ngưỡng; 100% cảnh báo do QA duyệt. |
| **Architecture** | LLM Feature + rule clustering + HITL. |

## Quyết định: chọn duy nhất Card 1

| Tiêu chí (1–5) | Card 1 | Card 2 | Card 3 |
|---|---:|---:|---:|
| Phù hợp prompt prototype | 5 | 4 | 4 |
| Workflow/metric dễ kiểm tra | 5 | 4 | 3 |
| Ranh giới an toàn rõ | 5 | 4 | 5 |
| Phù hợp starter code/autograder | 5 | 2 | 2 |
| Thuận lợi tổng hợp bài nhóm | 5 | 3 | 3 |
| **Tổng** | **25** | **17** | **17** |

Hai nhánh `origin/nguyen` và `origin/vu` độc lập cùng chọn bài toán sự cố pin Xanh SM. Card 1 vì vậy được chọn làm **ý tưởng duy nhất cho Deep-Dive, prompt prototype và sơ đồ**. Card 2–3 chỉ là bằng chứng của bước scan, không phải các project song song.

### Phản biện trước khi chọn

1. Chưa có log chứng minh soạn tin là bottleneck; pilot phải đo riêng thời gian tra dữ liệu và thời gian viết.
2. Template cứng có thể rẻ và ổn định hơn LLM; phải A/B test trên cùng tập ca.
3. `[DRAFT_ONLY]` trong text không phải cơ chế phân quyền; production cần nút duyệt và backend không cho AI tự thực thi.
