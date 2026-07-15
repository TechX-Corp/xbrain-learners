# [DIRECTIVE #8] Managed service phải chịu được sự cố thật - failover + restore drill

**Từ:** Ban Vận hành (SRE) & Kiểm toán - TechX Corp
**Hiệu lực:** khi nhận · hoàn tất & nộp trước **[BTC điền hạn]**
**Áp dụng:** TF đã chạy trên managed service (hoặc ngay sau khi hoàn tất Directive #7)

---

## Bối cảnh
Đưa store lên managed mới chỉ là bước một. Managed service **không tự nhiên chống lỗi** nếu bạn để single-instance, không bao giờ test failover, và app không biết tự kết nối lại. Rất nhiều đội "lift-and-shift" lên managed rồi tưởng xong - đến khi có sự cố thật mới lộ ra chưa bật Multi-AZ, chưa có backup dùng được, app treo khi endpoint đổi. Đợt này SRE + kiểm toán sẽ **ép tầng managed của bạn qua sự cố thật** để xem nó có thực sự chịu được.

## Yêu cầu
1. **HA thật, không single-instance.** RDS **Multi-AZ**, ElastiCache có replica + tự failover, MSK **multi-broker**. Chứng minh cấu hình - không để mặc định "một node cho rẻ".
2. **Failover drill (chấm tại chỗ).** BTC hoặc nhóm **kích một failover thật trong giờ vận hành** - RDS reboot-with-failover, ElastiCache primary fail, hoặc mất một MSK broker. Trong lúc đó: **checkout giữ SLO, không mất dữ liệu, app tự phục hồi** (retry / reconnect / re-resolve endpoint), không cần can thiệp tay.
3. **Backup + PITR restore drill.** Mô phỏng xóa/hỏng dữ liệu → **khôi phục về một điểm thời gian** (point-in-time recovery). Đo **RPO / RTO** thật bằng số liệu.
4. **Least-privilege + audit truy cập.** Truy cập DB/cache/queue bằng **IAM auth** (hạn chế password tĩnh), endpoint riêng tư, và **mọi truy cập/thay đổi để lại dấu vết** truy được về người.

## Ràng buộc
- Trong ngân sách (Multi-AZ đắt hơn - cân nhắc và giải thích đánh đổi).
- Không phá SLO khi drill; không đụng / vô hiệu hóa flagd (Luật chơi).

## Phải nộp
- **Chịu failover drill trước mặt mentor:** mentor xem **SLO không rớt + dữ liệu không mất** khi bạn/BTC kích failover một trong ba store.
- **Bằng chứng PITR restore:** khôi phục thành công + số liệu **RPO/RTO**.
- Cấu hình **HA + IAM/encryption + audit truy cập** cho cả ba store.

## Được nhìn ở trụ nào
Chính là **Reliability** (HA / DR / failover / restore). Chạm mạnh **Security** (IAM auth, encryption, private), **Auditability** (audit truy cập + change trail) và **Cost Optimization** (đánh đổi Multi-AZ).

> Directive cho đội đã ở tầng managed. Điểm nằm ở chỗ managed của bạn **thực sự sống sót qua một sự cố thật** - failover không rớt khách, và dữ liệu khôi phục được - chứ không chỉ "đã dùng managed".
