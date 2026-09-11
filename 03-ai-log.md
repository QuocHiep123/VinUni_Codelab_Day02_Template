# AI Log & Reflection

> Bản ghi dựa trên phiên làm việc này; phần reflection là bản nháp để người học đọc và chỉnh theo trải nghiệm của mình. Không khẳng định đã khảo sát doanh nghiệp hoặc chạy Gemini thành công.

## Quá trình sử dụng AI

| Yêu cầu | AI hỗ trợ | Giới hạn / điều chỉnh |
|---|---|---|
| Đọc 01, 02, 03 | Tóm tắt yêu cầu và bài mẫu | Bài mẫu không phải bằng chứng số liệu thực tế. |
| Hoàn thành Task 1 theo slide | Viết vai trò, draft-only, quy tắc pin, JSON và thiếu dữ liệu | Chọn policy bảo thủ: mọi pin <5% đều đề xuất hỗ trợ; đây là quy tắc prototype. |
| Hoàn thành Task 2 | Gọi Gemini bằng google-genai, truyền system instruction | Kiểm tra cú pháp không chứng minh API hoạt động. |
| Rà yêu cầu còn thiếu | Phát hiện Python ghi ≥2 tests nhưng worksheet ghi ≥3 | Ưu tiên worksheet; bộ test tăng lên 4 ca. |
| Hoàn thiện | Kiểm tra JSON, action, người duyệt; lưu kết quả; soạn báo cáo | Automatic pass chỉ xác nhận cấu trúc/action, vẫn cần đọc nội dung. |

## AI giúp gì?

AI giúp chuyển yêu cầu tự nhiên thành prompt và code, làm rõ input/output, đưa ra tình huống giả mạo quyền quản trị và yêu cầu bịa dữ liệu. Phần so sánh rule/template/LLM giúp giới hạn AI ở soạn nháp.

## Điểm chưa đúng hoặc chưa đủ và cách sửa

- Bộ kiểm tra ban đầu chỉ tìm từ khóa nên có thể báo pass dù output vẫn nguy hiểm. Đã đổi sang kiểm tra đầu dòng, JSON, trường dữ liệu và action kỳ vọng.
- Không được coi nhãn draft là cơ chế ngăn gửi tin: báo cáo bổ sung yêu cầu phân quyền và duyệt trong hệ thống nghiệp vụ.
- Không dùng các con số trong bài mẫu như kết quả khảo sát: toàn bộ baseline được ghi là giả định.
- Chưa có API key nên không viết kết quả test thành công hoặc kết luận GO dựa trên prompt.

## Trạng thái thực nghiệm

Đã viết 4 adversarial tests. Chưa chạy Gemini do thiếu API key trong môi trường phiên làm việc. Kết quả tự động sẽ được lưu tại `starter-code/prototype-results.json` sau khi chạy.

Đã cài google-genai trong `.venv`; kiểm tra validator với output đúng, sai prefix, JSON lỗi, sai action, thiếu duyệt và dòng thừa. Kiểm tra lời gọi SDK bằng mock xác nhận truyền đúng system instruction/input và từ chối phản hồi rỗng. Các kiểm tra này thành công; chạy script thật dừng với thông báo thiếu API key. Đây không phải kết quả đánh giá Gemini.

Sau lần chạy thật, bổ sung: ngày chạy, số ca pass/fail/error, output vi phạm, đánh giá nội dung của người review, prompt trước/sau khi sửa. Không coi test giả lập là bằng chứng về hành vi Gemini.

## Reflection dự thảo

Bài học chính là phải phân biệt yêu cầu viết trong prompt với ranh giới do code và quyền hệ thống thực thi. Một JSON đúng không đảm bảo nội dung đúng. Trước khi dùng thực tế, cần đo baseline, đối chiếu với template và có người chịu trách nhiệm duyệt. Quyết định hiện tại là NOT YET cho pilot vì còn thiếu dữ liệu và kết quả đánh giá.
