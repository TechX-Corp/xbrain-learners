# [DIRECTIVE #7] Sự cố phải tự lộ ra - dựng mắt cho hệ thống

**Từ:** Ban Vận hành (SRE) - TechX Corp
**Hiệu lực:** ngay khi nhận · hoàn tất & nộp trước **thứ Bảy 18/07/2026**
**Áp dụng:** nhóm AIO của mọi Task Force

---

## Bối cảnh
Hiện muốn biết hệ thống có đang khoẻ hay không, phải có người ngồi mở Grafana soi bằng mắt - nghĩa là sự cố chỉ lộ ra khi khách đã kêu. Với một service có SLA, như vậy là quá muộn. Các bạn có sẵn cả kho telemetry (metric/log/trace) mà chưa có "đôi mắt" tự động nào đọc nó. Nhiệm vụ đợt này: dựng đôi mắt đó - hệ tự phát hiện bất thường và báo, trước khi user phản ánh.

## Yêu cầu
1. **Phát hiện bất thường đa tín hiệu** - không chỉ ngưỡng tĩnh: bắt được bất thường trên latency / error rate / saturation / queue / cost… dựa trên telemetry thật đang chảy.
2. **Có baseline "biết thế nào là bình thường"** - lập baseline theo từng service để không báo nhầm vào lúc tải cao bình thường.
3. **Cảnh báo có ý nghĩa, không spam** - báo theo mức độ ảnh hưởng (ưu tiên triệu chứng user-visible + burn-rate error budget), không phải mỗi cái gợn là kêu.
4. **Chạy được end-to-end** - bơm một bất thường vào là detector **kêu ra**, nhìn thấy được (alert/log/dashboard). Tuần này chỉ cần **feature chạy thật**, chưa cần số precision/recall chính xác - phần đo đạc để đợt sau.

## Định nghĩa Hoàn thành (DoD - hạn 18/07)
Không cần phủ hết ~18 service. Tuần này đạt khi:
1. **Chọn ≥ 3 service trọng yếu** (ưu tiên user-facing / có SLA: frontend, checkout, cart, product-catalog…) - liệt kê rõ đội chọn cái nào và vì sao.
2. **Mỗi service đó có baseline cho ≥ 1 tín hiệu vàng** (latency **hoặc** error rate) - "bình thường" là khoảng nào.
3. **Bơm 1 sự cố vào 1 trong các service đó → detector kêu e2e**, nhìn thấy được (alert/log/dashboard).
> Mở rộng ra nhiều service hơn + nhiều tín hiệu hơn = điểm cao hơn, nhưng 3 service + 1 lần kêu e2e là **sàn đạt**. Số precision/recall để đợt sau.

## Ràng buộc
- Không kéo tải/độ trễ hệ thống vì việc thu thập-đo (đo phải nhẹ).
- Trong ngân sách hiện tại - đừng dựng thêm cụm nặng để "cho oách".
- Không đụng / vô hiệu hóa cơ chế sự cố (flagd) - xem Luật chơi trong RULES.

## Phải nộp
- Cho mentor **tự bật một sự cố** (hoặc để mentor bơm qua flagd) và tận mắt thấy detector **kêu ra** - chạy end-to-end được là đạt. Số precision/recall/lead-time chưa bắt buộc tuần này (để đợt sau tinh chỉnh).
- **ADR ký tên**: chọn phương pháp phát hiện gì, ngưỡng/baseline ra sao, vì sao.

## Được nhìn ở đâu
Trụ **AI** (AIOps): dùng AI/thống kê để vận hành. Chạm **Reliability** (phát hiện sớm giữ SLO) và **Operational Excellence** (giảm thời gian tới lúc biết có sự cố - MTTD).

> Directive bắt buộc nhóm AIO toàn TF. Điểm nằm ở chỗ: sau đợt này, sự cố **tự lộ ra qua cảnh báo** chứ không đợi người soi - và chứng minh bằng một lần bơm sự cố thấy nó kêu đúng.
