# Tầng AI - bạn đang vận hành & xây gì

Tài liệu onboarding cho nhóm AIO (hướng AIE). Đọc để hiểu bề mặt AI của sản phẩm và cái bạn phải dựng.

## 1. Bề mặt AI hiện có

| Thành phần | Vai trò | Ngôn ngữ | Phụ thuộc |
|---|---|---|---|
| `product-reviews` | Review sản phẩm + **tóm tắt AI** + hỏi-đáp AI | Python | postgresql, `llm` |
| `llm` | Backend model (sinh tóm tắt / trả lời) | Python | mock mặc định; LLM thật khi bạn cắm |

- Tính năng AI đang chạy: khi khách mở trang sản phẩm, `product-reviews` gọi `llm` để **sinh tóm tắt review**.
- `llm` mặc định là **mock**. Cắm model thật (gpt-4o-mini / Bedrock…) qua `deploy/values-aio-llm.yaml` + secret `llm-api-key` (xem GETTING_STARTED).
- Telemetry GenAI đã đi qua OpenTelemetry → collector → Prometheus / Jaeger / OpenSearch: bạn **quan sát được** latency, lỗi, nội dung (trace) của lời gọi AI.

## 2. Việc AIE - hai phần

### Phần A - Vận hành & nâng chất tính năng có sẵn (tóm tắt review)
- **Đúng đắn:** eval độ trung thực (tóm tắt phải khớp review gốc), **fallback** khi `llm` lỗi/chậm → **không bao giờ show tóm tắt sai** cho khách.
- **An toàn:** guardrail chặn prompt-injection nhét trong nội dung review, lọc PII, chặn lộ system prompt.
- **Chi phí/độ trễ:** cache tóm tắt theo sản phẩm, route model rẻ, giảm token, timeout/retry.

### Phần B - Tự dựng trợ lý AI agentic (BTC KHÔNG phát sẵn code agent)
Dựng một trợ lý biết **gọi công cụ** trên chính các service đang chạy để giúp khách. Bạn xây trên source có sẵn (`product-reviews`/`llm` + các rpc của service khác).

**Công cụ agent có thể gọi (đã có trong hệ thống):**
- `product-catalog`: list / get / **search** sản phẩm.
- `product-reviews`: review + tóm tắt của một sản phẩm.
- `cart`: xem / thêm / sửa giỏ.

**Trợ lý cần làm được (ví dụ):**
- *"Tìm tai nghe dưới $50 giao nhanh"* → search catalog + lọc → gợi ý.
- *"Pin sản phẩm này dùng bao lâu?"* → trả lời **grounded trên review thật** (RAG), có dẫn nguồn, không bịa.
- *"So sánh A với B"* → gom catalog + review 2 sản phẩm → ưu/nhược.
- *"Thêm 2 cái vào giỏ"* → gọi `cart`, **xác nhận trước khi checkout**.

**Ràng buộc an toàn (được chấm):**
- Chỉ gọi công cụ **trong allow-list**; **không** tự ý checkout / xoá giỏ / hành động ngoài phạm vi (guardrail **excessive-agency**).
- Trả lời **grounded**, không hallucinate; không lộ PII / system prompt.
- Có **giới hạn vòng lặp** cho agent (tránh loop vô hạn) + log/audit lời gọi tool.

## 3. Đánh giá dựa trên gì
- **Chạy thật** trong hệ thống của TF (build → ECR → deploy), không mockup.
- **Eval** chứng minh: độ trung thực tóm tắt, block-rate guardrail (injection/PII), và **task-success** của trợ lý (hoàn thành đúng tác vụ trong phạm vi), không chỉ "trả lời trôi chảy".
- Không phá SLO / ngân sách; quyết định lớn ghi **ADR ký tên**.

## 4. Bắt đầu từ đâu
1. Cắm `llm` sang model thật (`values-aio-llm.yaml`) → xem tính năng tóm tắt chạy thật.
2. Dựng eval + guardrail cho tóm tắt (Phần A) trước - đây là nền.
3. Xây trợ lý agentic (Phần B): chọn framework tool-calling của bạn, wire vào các rpc ở trên, thêm guardrail + eval task-success.
4. Đưa eval vào CI để mỗi thay đổi không làm rớt chất lượng.

> Đầu mối kỹ thuật (rpc/proto, cấu hình `llm`) nằm trong `techx-corp-platform/` (xem `src/product-reviews`, `src/llm`, và proto của các service). Khám phá source là một phần của việc tiếp quản.
