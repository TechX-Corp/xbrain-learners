# [DIRECTIVE #7] Sự cố phải tự lộ ra - dựng mắt cho hệ thống

**Từ:** Ban Vận hành (SRE) - TechX Corp
**Hiệu lực:** ngay khi nhận · nộp qua **2 ticket**: `#7a` trước **thứ Bảy 18/07**, `#7b` trước **thứ Bảy 25/07**
**Áp dụng:** nhóm AIO của mọi Task Force

---

## Bối cảnh
Hiện muốn biết hệ thống có đang khoẻ hay không, phải có người ngồi mở Grafana soi bằng mắt - nghĩa là sự cố chỉ lộ ra khi khách đã kêu. Với một service có SLA, như vậy là quá muộn. Các bạn có sẵn cả kho telemetry (metric/log/trace) mà chưa có "đôi mắt" tự động nào đọc nó. Nhiệm vụ đợt này: dựng đôi mắt đó - hệ tự phát hiện bất thường và báo, trước khi user phản ánh.

## Yêu cầu
1. **Phát hiện bất thường trên nhiều tín hiệu** - không chỉ ngưỡng tĩnh: theo dõi latency / error rate / saturation / queue / cost… dựa trên telemetry thật. **Sàn = univariate**: mỗi *service × 1 tín hiệu* có baseline + luật bất thường riêng. Gộp nhiều tín hiệu lại thành một mô hình chung (**multivariate / tương quan**) là **bonus**, không bắt buộc.
2. **Có baseline "biết thế nào là bình thường"** - lập baseline theo từng service để không báo nhầm vào lúc tải cao bình thường.
3. **Cảnh báo có ý nghĩa, không spam** - báo theo mức độ ảnh hưởng (ưu tiên triệu chứng user-visible + burn-rate error budget), không phải mỗi cái gợn là kêu.
4. **Chạy được end-to-end** - bơm một bất thường vào là detector **kêu ra**, nhìn thấy được (alert/log/dashboard). Phần chạy thật + đo đạc nộp ở chặng sau (#7b); chặng đầu (#7a) chỉ cần implement + phân tích.

## Định nghĩa Hoàn thành cho #7a (hạn 18/07) — implement + phân tích
Không cần chạy thật tuần này. Đạt khi trong Jira thể hiện được:
1. **Đã bắt tay implement** detector + baseline (link PR/commit cho thấy code có thật).
2. **Phân tích (dạng doc trong ticket):** chọn **≥ 3 metrics** từ (các) service trọng yếu (vd p95 latency của checkout, error-rate của cart, saturation của product-catalog…); với **mỗi metric**: vì sao chọn, baseline "bình thường" là khoảng nào, thế nào thì coi là bất thường; phương pháp phát hiện định dùng.
3. **ADR ký tên.**
> Chạy thật e2e + ảnh alert minh chứng + số precision/recall để chặng **#7b** (25/07).

## Ràng buộc
- Không kéo tải/độ trễ hệ thống vì việc thu thập-đo (đo phải nhẹ).
- Trong ngân sách hiện tại - đừng dựng thêm cụm nặng để "cho oách".
- Không đụng / vô hiệu hóa cơ chế sự cố (flagd) - xem Luật chơi trong RULES.

## Phải nộp
Nộp qua **2 Jira ticket** riêng (cách ghi evidence xem `AI_MANDATE_EVIDENCE.md`):

- **`[DIRECTIVE #7a]` Detection · implement + phân tích — hạn T7 18/07** *(chấm như doc, chưa cần chạy thật)*
  - **Link PR/commit** cho thấy đã implement detector + baseline.
  - **Phân tích trong ticket:** **≥ 3 metrics** đã chọn (từ service trọng yếu) + với mỗi metric: vì sao chọn, baseline "bình thường", ngưỡng bất thường; phương pháp phát hiện.
  - **ADR ký tên.**
- **`[DIRECTIVE #7b]` Detection · chạy thật + đo đạc — hạn T7 25/07**
  - **Ảnh/log detector kêu e2e** khi bơm 1 sự cố (mentor tự bật hoặc bơm qua flagd) + cách chạy lại.
  - **Số precision/recall/lead-time** đo trên **một bộ sự cố có nhãn** (mentor bơm K sự cố + có giai đoạn bình thường), KHÔNG phải per-service: recall = bắt được / K; precision = lần kêu đúng / tổng lần kêu; lead-time = từ lúc sự cố bắt đầu tới lúc kêu.
  - **Cảnh báo theo mức ảnh hưởng** (burn-rate, không spam) + mở rộng thêm service.

> Đội đã có detection chạy sẵn thì làm gọn `#7a` và tập trung vào `#7b`.

## Được nhìn ở đâu
Trụ **AI** (AIOps): dùng AI/thống kê để vận hành. Chạm **Reliability** (phát hiện sớm giữ SLO) và **Operational Excellence** (giảm thời gian tới lúc biết có sự cố - MTTD).

> Directive bắt buộc nhóm AIO toàn TF. Điểm nằm ở chỗ: sau đợt này, sự cố **tự lộ ra qua cảnh báo** chứ không đợi người soi - và chứng minh bằng một lần bơm sự cố thấy nó kêu đúng.
