# Nộp evidence cho mandate AI qua Jira

Áp dụng cho **track AIO** (các directive AI: #6, #7, …). Mentor chấm mandate **dựa trên Jira ticket** — không có ticket, hoặc ticket không có bằng chứng chạy được, thì coi như chưa làm.

> Team ngoài track AI nộp theo `JIRA_EXPORT.md` như bình thường.

---

## Mỗi mandate = 1 ticket (mandate nhiều mốc thì nhiều ticket)

**Tạo ticket:**
- **Summary (naming — theo đúng format này):**
  `[DIRECTIVE #N<stage>][<TF>] <tên ngắn>`
  - `#N` = số mandate, khớp tiêu đề memo (`# [DIRECTIVE #N]`).
  - `<stage>` = chữ thường `a`/`b`/`c`… khi mandate chia nhiều chặng; **bỏ đi** nếu mandate 1 chặng.
  - `[<TF>]` = chỉ thêm khi mandate **dành riêng 1 Task Force** (stretch); mandate chung cho mọi TF thì bỏ.
  - `<tên ngắn>` ≤ ~8 từ, mô tả chặng.
  - Ví dụ: `[DIRECTIVE #6] AI trust & safety` · `[DIRECTIVE #7a] Detection · implement + phân tích` · `[DIRECTIVE #7b] Detection · chạy thật + đo đạc` · `[DIRECTIVE #11B][TF1] RCA + red-team`.
- **Label (bắt buộc):** `ai-mandate` **và** `m<N>` (vd `m7`) — để lọc chung + lọc theo từng mandate.
- **Assignee:** người đại diện nộp. Làm chung nhiều người thì ghi tên đồng đội ở `Description` (không quy trách nhiệm 1 người, nhưng phải có 1 người đứng ra nộp).
- **Priority:** set theo deadline (mandate đang chạy → High).
- **(nếu board dùng Epic):** 1 Epic cho mỗi mandate, các chặng `a`/`b` là ticket con.

**Evidence dồn vào comment — đủ 4 thứ:**
1. **Link PR/commit** phần code làm mandate (nối ticket ↔ repo).
2. **Cách chạy lại (repro):** lệnh/script để mentor tự bật lại — ví dụ `make detect-demo`, hoặc "bơm flagd X → xem alert ở …".
3. **Bằng chứng chạy thật:** ảnh/log cho thấy tính năng **chạy end-to-end** (detector kêu / guardrail chặn / loop rollback… tùy mandate).
4. **Link ADR ký tên** — chọn phương pháp gì, đánh đổi gì.

**Đóng ticket** khi đủ 4 thứ trên, **trước deadline**. Thiếu mục 3 (bằng chứng chạy thật) → mentor để ticket mở, hỏi lại, **chưa tính** dù code đã có.

> Một số chặng cố tình chấm **như doc** (implement + phân tích, chưa cần chạy thật) — ví dụ `#7a`. Với chặng đó, mục 3 thay bằng **phần phân tích viết trong ticket**; deadline chạy thật rơi vào chặng sau.

---

## Mandate nhiều mốc → nhiều ticket

Một mandate có thể có nhiều chặng, mỗi chặng 1 ticket + 1 deadline riêng. Ví dụ **#7 (detection)**:

| Ticket | Nội dung | Chấm kiểu | Hạn |
|---|---|---|---|
| `[DIRECTIVE #7a]` | implement (link PR) + phân tích **≥3 metrics** (mỗi metric: baseline/ngưỡng bất thường) + phương pháp | như **doc** — chưa cần chạy thật | T7 18/07 |
| `[DIRECTIVE #7b]` | chạy thật e2e (**ảnh alert**) + số precision/recall + alert theo mức ảnh hưởng | **bằng chứng chạy được** | T7 25/07 |

Đội đã có sẵn phần đầu thì làm gọn `#7a`, tập trung `#7b`.

---

> Nguyên tắc gốc (từ `JIRA_EXPORT.md`): **thứ gì không để lại dấu vết trong Jira/repo thì coi như không có.** Mandate AI thêm một điều: **phải có bằng chứng chạy được**, không chỉ link code.

---

## English

# Submitting AI mandate evidence via Jira

Applies to the **AIO track** (AI directives: #6, #7, …). Mentors grade a mandate **from the Jira ticket** — no ticket, or a ticket with no working evidence, counts as not done.

> Teams outside the AI track submit via `JIRA_EXPORT.md` as usual.

### One mandate = one ticket (multi-stage mandate = multiple tickets)

**Create the ticket:**
- **Summary (naming — follow this exact format):**
  `[DIRECTIVE #N<stage>][<TF>] <short title>`
  - `#N` = mandate number, matching the memo title (`# [DIRECTIVE #N]`).
  - `<stage>` = lowercase `a`/`b`/`c`… when the mandate splits into stages; **omit** for single-stage mandates.
  - `[<TF>]` = add only when the mandate is **for one Task Force** (stretch); omit for all-TF mandates.
  - `<short title>` ≤ ~8 words describing the stage.
  - Examples: `[DIRECTIVE #6] AI trust & safety` · `[DIRECTIVE #7a] Detection · implement + analysis` · `[DIRECTIVE #7b] Detection · live run + measurement` · `[DIRECTIVE #11B][TF1] RCA + red-team`.
- **Label (required):** `ai-mandate` **and** `m<N>` (e.g. `m7`) — for global + per-mandate filtering.
- **Assignee:** the person submitting. If done by several people, list teammates in `Description` (no single-person blame, but one person owns submission).
- **Priority:** set by deadline (active mandate → High).
- **(if the board uses Epics):** one Epic per mandate, stages `a`/`b` as child tickets.

**Pile the evidence into a comment — all 4 items:**
1. **PR/commit link** for the mandate code (ties ticket ↔ repo).
2. **How to reproduce (repro):** command/script for the mentor to re-run — e.g. `make detect-demo`, or "inject flagd X → see the alert at …".
3. **Proof it runs:** screenshot/log showing the feature **running end-to-end** (detector fires / guardrail blocks / loop rollback… depending on the mandate).
4. **Signed ADR link** — method chosen, trade-offs.

**Close the ticket** when all 4 are present, **before the deadline**. Missing item 3 (working proof) → the mentor leaves the ticket open, asks back, and it **does not count** even if the code exists.

> Some stages are deliberately graded **as a doc** (implement + analysis, no live run yet) — e.g. `#7a`. For those, item 3 is replaced by the **analysis written in the ticket**; the live-run deadline falls in the later stage.

### Multi-stage mandate → multiple tickets

A mandate can have several stages, each its own ticket + deadline. Example **#7 (detection)**:

| Ticket | Content | Grading style | Due |
|---|---|---|---|
| `[DIRECTIVE #7a]` | implement (PR link) + analysis of **≥3 metrics** (each: baseline/anomaly threshold) + method | as a **doc** — no live run yet | Sat 18/07 |
| `[DIRECTIVE #7b]` | live e2e run (**alert screenshot**) + precision/recall + impact-based alerting | **working proof** | Sat 25/07 |

Teams that already have the first part can keep `#7a` light and focus on `#7b`.

> Root principle (from `JIRA_EXPORT.md`): **anything that leaves no trace in Jira/repo counts as nonexistent.** AI mandates add one more: **you must show working proof**, not just a code link.
