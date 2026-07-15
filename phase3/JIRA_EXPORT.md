# Cách export Jira ra CSV để nộp cho mentor chấm

Mentor sẽ chấm phần việc của em **dựa trên Jira**. File export chỉ chụp lại đúng những gì đang có trong Jira, nên làm sai một bước là mất thông tin — và **phần việc không quy được về tên em thì không được tính**. Làm theo ba bước dưới, mất khoảng 10 phút.

---

## Bước 1 — Dọn nhanh trước khi export  *(~5 phút)*

Kiểm nhanh trên board, sửa những chỗ này trước khi export:

- [ ] **Mỗi ticket có `Priority`** (Highest / High / Medium / Low). Trống → mentor không thấy nhóm ưu tiên gì.
- [ ] **Việc quyết định không làm:** chuyển ticket sang trạng thái đóng của nhóm (Done / Cancel / Closed — tùy board), **và viết một comment mở đầu bằng `[KHÔNG LÀM]` nêu lý do**. Mentor đọc lý do ở comment, không phải ở tên trạng thái. Bỏ đúng việc có lý do là **điểm cộng**; đóng ticket không giải thích trông như "kế hoạch sai, làm lại".
- [ ] **Ticket em làm:** dán **link PR / commit** vào comment của ticket — ví dụ `https://github.com/.../pull/42`. Đây là cách mentor nối ticket với code em viết.
- [ ] **`Assignee` đúng người thật sự làm** ticket đó.
- [ ] **Ticket em own:** phần `Description` ghi ngắn — làm gì, quyết định gì (2–3 dòng là đủ).

> Không kịp sửa hết cũng không sao — cứ dán link PR vào comment và điền Priority là đủ để chấm. Đừng viết lại lịch sử git.

---

## Bước 2 — Export CSV **(all fields)**

> ⚠️ **Bước quan trọng nhất.** Phải chọn **all fields**, KHÔNG chọn **current fields** — `current fields` chỉ xuất mấy cột đang hiện trên màn hình, thiếu `Description`, `Comment` (mất luôn link GitHub em dán), `Priority`… và file đó chấm không được.

1. Sidebar bên trái → **Filters** → **Search work items**. Trang **"All work"** (danh sách tất cả ticket) mở ra.
2. Ô tìm kiếm, bấm chế độ **JQL** rồi gõ: `project = <mã nhóm>` (ví dụ `project = SCRUM`).
   Muốn giữ thứ tự thì thêm `ORDER BY created DESC`. Bấm **Enter** để lọc.
3. Góc **trên cùng bên phải** → nút **`...`** (ba chấm, nằm sau nút *Share*) → chọn **Export CSV (all fields)**.
   (Nếu trong `...` không thấy, thử nút **`Apps ▾`** ngay cạnh đó.)
4. File `.csv` tải về.

> Không thấy nút export? Báo lại — có thể tài khoản em thiếu quyền, để PM hoặc admin nhóm export giúp.

Jira cho export tới 10.000 ticket một lần, nên một nhóm vài chục ticket thì thoải mái.

---

## Bước 3 — Kiểm file trước khi nộp

Mở file CSV vừa tải, kiểm có đủ **bốn cột** sau (mở bằng Excel, xem dòng tiêu đề):

- [ ] **`Description`** và **`Comment`** — nếu thiếu → em đã export nhầm `current fields`, làm lại Bước 2. (File all-fields thường có mấy chục cột; file current-fields chỉ vài cột cơ bản.)
- [ ] **`Priority`** không trống hàng loạt.
- [ ] **`Assignee`** đúng người.
- [ ] **`Assignee Id`** (chuỗi dài kiểu `712020:...`). Cột này giúp mentor nối đúng người dù tên em viết khác nhau ở chỗ khác.

---

## Nộp

- **Đặt tên file:** `<mã nhóm>_W<tuần>_<ngày>.csv`
  ví dụ `CDO08_W1_2026-07-14.csv`.
- **Nộp qua:** [điền chỗ nộp — link drive / commit vào repo `jira/` / …]

---

## Vì sao phải đúng — để phần việc của em được tính đủ

| Làm sai | Hậu quả |
|---|---|
| Export `current fields` | Mất cột Comment → mất link PR → mentor không nối được ticket với code em viết → **phần việc không tính** |
| `Priority` trống | Nhóm bị coi như **chưa ưu tiên** |
| Đóng ticket không có comment lý do | Mất tín hiệu "dám bỏ đúng việc" — vốn là điểm cộng |
| Ticket không có link PR trong comment | Mentor thấy ticket đóng nhưng không có bằng chứng → hỏi lại / không tính |

Nói gọn: **thứ gì không để lại dấu vết trong Jira/repo thì coi như không có.** Mười phút dọn và export đúng đảm bảo mọi việc em làm đều có tên em trên đó.
