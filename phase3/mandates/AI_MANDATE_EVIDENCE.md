# Nộp evidence cho mandate AI qua Jira

Áp dụng cho **track AIO** (các directive AI: #6, #7, …). Mentor chấm mandate **dựa trên Jira ticket** — không có ticket, hoặc ticket không có bằng chứng chạy được, thì coi như chưa làm.

> Team ngoài track AI nộp theo `JIRA_EXPORT.md` như bình thường.

---

## Mỗi mandate = 1 ticket (mandate nhiều mốc thì nhiều ticket)

**Tạo ticket:**
- **Summary:** `[DIRECTIVE #N] <tên mandate>` — ví dụ `[DIRECTIVE #7a] Detection · baseline + e2e`.
- **Assignee:** người đại diện nộp. Làm chung nhiều người thì ghi tên đồng đội ở `Description` (không quy trách nhiệm 1 người, nhưng phải có 1 người đứng ra nộp).
- **Priority:** set theo deadline (mandate đang chạy → High).
- **Label:** `ai-mandate` (để lọc riêng khỏi ticket vận hành thường).

**Evidence dồn vào comment — đủ 4 thứ:**
1. **Link PR/commit** phần code làm mandate (nối ticket ↔ repo).
2. **Cách chạy lại (repro):** lệnh/script để mentor tự bật lại — ví dụ `make detect-demo`, hoặc "bơm flagd X → xem alert ở …".
3. **Bằng chứng chạy thật:** ảnh/log cho thấy tính năng **chạy end-to-end** (detector kêu / guardrail chặn / loop rollback… tùy mandate).
4. **Link ADR ký tên** — chọn phương pháp gì, đánh đổi gì.

**Đóng ticket** khi đủ 4 thứ trên, **trước deadline**. Thiếu mục 3 (bằng chứng chạy thật) → mentor để ticket mở, hỏi lại, **chưa tính** dù code đã có.

---

## Mandate nhiều mốc → nhiều ticket

Một mandate có thể có nhiều chặng, mỗi chặng 1 ticket + 1 deadline riêng. Ví dụ **#7 (detection)**:

| Ticket | Nội dung | Hạn |
|---|---|---|
| `[DIRECTIVE #7a]` | baseline ≥3 service + detector kêu e2e | T7 18/07 |
| `[DIRECTIVE #7b]` | số precision/recall + alert theo mức ảnh hưởng | T7 25/07 |

Đội đã có sẵn phần đầu thì tập trung ticket chặng sau.

---

> Nguyên tắc gốc (từ `JIRA_EXPORT.md`): **thứ gì không để lại dấu vết trong Jira/repo thì coi như không có.** Mandate AI thêm một điều: **phải có bằng chứng chạy được**, không chỉ link code.
