# [DIRECTIVE #4] Tầng AI phải an toàn và đáng tin

**Từ:** Ban Trust & Safety / Pháp chế - TechX Corp
**Hiệu lực:** 14/07/2026 · hoàn tất & nộp trước **16/07/2026**
**Áp dụng:** nhóm AIO của mỗi Task Force (TF chịu trách nhiệm giao)

---

## Bối cảnh
Đợt rà soát tầng AI (tóm tắt review) phát hiện ba rủi ro với khách:
- (a) nội dung review có thể **chèn chỉ dẫn độc** khiến tóm tắt sai / lệch;
- (b) **PII của khách** có thể lọt vào tóm tắt và cả telemetry;
- (c) tóm tắt có thể **sai sự thật** so với review gốc.

Không được để những thứ này tới tay khách.

## Yêu cầu
1. **Guardrail chống prompt-injection.** Nội dung review là dữ liệu **không tin cậy** - chèn chỉ dẫn kiểu "bỏ qua hướng dẫn trên…" không được làm đổi hành vi tóm tắt, và không được để lộ system prompt.
2. **Chặn PII.** Tóm tắt không phơi PII của khách, và **PII cũng không được lọt vào trace / telemetry**.
3. **Không hiển thị tóm tắt sai.** Có **eval độ trung thực** (so tóm tắt với review nguồn); tóm tắt sai / bịa thì chặn hoặc fallback (review thô hoặc cache) - không show cho khách.
4. **Không treo trang khi AI lỗi/chậm.** LLM lỗi / 429 / chậm thì fallback, không kéo tụt trang sản phẩm.

BTC sẽ **tự bắn probe** (review chèn injection, review có PII) và **bật lỗi tóm tắt sai** để kiểm.

## Ràng buộc
- Chạy thật trong hệ thống của TF, không mockup; không phá SLO / ngân sách.
- Không đụng / vô hiệu hóa flagd (Luật chơi) - BTC dùng flag để mô phỏng lỗi AI.

## Phải nộp
- **Cách để mentor tự bắn probe** (endpoint AI + vài ca thử injection / PII) và **eval** cho thấy tóm tắt sai bị chặn. Mentor tự xác nhận: injection không đổi được output, PII bị lọc (cả trong summary lẫn trace), tóm tắt sai không tới khách.

## Được nhìn ở trụ nào
Chính là **trụ AI (AIO)** - chất lượng, an toàn (guardrail), độ tin cậy của tầng AI. Chạm thêm **Security** (PII, injection) và **Auditability** (không rò rỉ qua telemetry).

> Directive bắt buộc, nhóm AIO của TF chịu trách nhiệm. Điểm nằm ở chỗ tầng AI **coi input của khách là không tin cậy** và **không bao giờ đưa thông tin sai / PII tới khách**.
