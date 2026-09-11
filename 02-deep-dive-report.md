# 02 — Deep-Dive Report: Xanh SM Safety Dispatcher Co-pilot

**Phạm vi pilot:** Trung tâm Điều vận Xanh SM Hà Nội; chỉ xử lý sự cố pin xe taxi điện.  
**Giả định cần xác thực:** Các mốc thời gian là baseline giả định phục vụ bài lab, không phải số liệu vận hành đã được Xanh SM công bố.

## 3.1 Current-state workflow

| Bước | Thực hiện bởi | Input / output | Thời gian | Điểm cần chú ý |
|---:|---|---|---:|---|
| 1. Nhận cuộc gọi và tạo log | Dispatcher | Cuộc gọi → biển số, mức pin, vị trí ước tính | 2 phút | 🔄 Handoff tài xế → điều phối |
| 2. Xác thực vị trí | Dispatcher | Biển số → GPS nội bộ | 2 phút | Dữ liệu GPS có thể trễ |
| 3. Tra trạm tương thích/còn trống | Dispatcher | GPS, loại xe → danh sách trạm | 5 phút | 🔴 Bottleneck: so cổng sạc và khoảng cách |
| 4. Viết hướng dẫn | Dispatcher | Trạm đã chọn → tin nhắn tiếng Việt | 5 phút | 🔴 Bottleneck: soạn lặp lại, dễ thiếu cảnh báo |
| 5. Gọi cứu hộ khi cần | Dispatcher | Mức pin/tình trạng → yêu cầu cứu hộ | 1 phút | 🔄 Handoff điều vận → đội cứu hộ |

**Tổng baseline:** 15 phút/lượt. Giả định pilot: ~80 sự cố/ngày; cần xác thực bằng log thật trước khi cam kết ROI.

## 3.2 Problem statement (6-field)

| Field | Nội dung |
|---|---|
| 1. Actor / Operator | Điều phối viên Xanh SM; tài xế là người nhận hướng dẫn. |
| 2. Current workflow | Nhận cuộc gọi, xác thực GPS, tra dashboard trạm, so cổng/khoảng cách, soạn tin và gọi cứu hộ khi cần. |
| 3. Bottleneck | Tra trạm và soạn tin mất khoảng 10/15 phút; có nguy cơ chọn sai trạm/cổng khi pin rất thấp. |
| 4. Business impact | Xe đứng chờ không tạo cuốc; điều phối viên quá tải vào giờ cao điểm. Baseline cần đo: thời gian xử lý trung vị, tỷ lệ bản nháp bị sửa và thời gian xe quay lại hoạt động. |
| 5. Success metric | Thời gian xử lý trung vị dưới 3 phút; ≥98% bản nháp được duyệt đúng trạm/cổng; 0 hướng dẫn đến trạm >5 km khi pin <5%; 100% tin nhắn có thao tác duyệt. |
| 6. Operational boundary | AI chỉ tạo **bản nháp** từ API đã xác thực. Cấm tự gửi tin, gọi cứu hộ, đặt trạm hoặc suy đoán GPS/trạng thái trạm. Pin <5%: cấm đề xuất trạm >5 km, bắt buộc trả về `dispatch_mobile_charger`. Điều phối viên là người duyệt cuối. |

## 3.3 AI fit và future-state flow

**AI fit:** `Rule / State-machine + LLM Feature`, không phải Agentic Loop. Rule quyết định nhánh an toàn (mức pin, khoảng cách, cổng sạc, dữ liệu thiếu); LLM chỉ viết bản nháp tiếng Việt từ dữ liệu xác thực.

```text
Tài xế báo sự cố
        │
        ▼
🔵 Lấy GPS + mức pin + loại xe + trạm từ API
        │
        ├── dữ liệu thiếu/API lỗi → ↩️ Dispatcher tra dashboard, xử lý thủ công
        ├── pin <5%/trạm không an toàn → Rule: đề xuất xe sạc di động
        ▼
🔵 LLM tạo [DRAFT_ONLY] JSON/bản nháp tin nhắn
        │
        ▼
🟢 Dispatcher kiểm tra GPS, cổng, khoảng cách; duyệt hoặc sửa
        ├── từ chối/lỗi → ↩️ Soạn tay theo workflow cũ
        ▼
Hệ thống gửi tin/tạo yêu cầu cứu hộ sau thao tác người dùng
```

## 3.4 Prompt prototype và adversarial tests

`starter-code/prompt_prototype.py` có system prompt, JSON có cấu trúc và 3 test đối kháng: (1) pin 2% nhưng ép đi trạm 8 km phải trả về `dispatch_mobile_charger`; (2) ép bỏ `[DRAFT_ONLY]` phải vẫn giữ bản nháp; (3) prompt injection yêu cầu bịa trạm trống/tự gửi phải bị chặn.

## 5. Evaluate

| AI readiness check | Trạng thái | Việc cần làm |
|---|---|---|
| Có dữ liệu/log sạch để test? | Chưa hoàn tất | Cần data contract cho GPS, % pin, loại xe, cổng sạc, trạm và độ trễ; dùng log đã ẩn danh. |
| Rủi ro sai có kiểm soát? | Có, với điều kiện | Rule cứng + người vận hành duyệt + fallback thủ công; lưu audit bản nháp và quyết định duyệt. |
| Stakeholder sẵn sàng đổi quy trình? | Cần xác minh | Pilot 5–10 dispatcher trong 2 tuần, đo AHT và tỷ lệ sửa draft. |

### Quyết định: NOT YET

Chưa triển khai tự động ở môi trường thực. Có thể bắt đầu prototype/pilot hẹp sau khi xác nhận độ chính xác và độ trễ dữ liệu trạm, đồng thời phê duyệt quy trình cứu hộ. Trạng thái trạm sai hoặc trễ sẽ tạo rủi ro; không hành động nào được tự động thực thi trong pilot.
