# Athena Starter

**Agent Claude Code của bạn luôn biết mình đang ở luồng nào, đang ở bước nào trong plan, và còn việc gì chưa xong.**

Athena Starter là plugin cho Claude Code: tự điều hướng mỗi yêu cầu vào đúng luồng làm việc, ghi bảng tiến độ cho mọi việc nhiều bước, và chặn các lối tắt làm hỏng dự án thật. Kèm một app desktop hiển thị tất cả dưới dạng đồ thị, bảng Kanban và timeline.

[English](README.md) · Tiếng Việt

```
/plugin install athena-starter --marketplace zzgiabaozzbui/athena-starter
```

![Agent Observatory hiển thị toàn bộ plan 14 task](docs/images/observatory-full-plan.png)

*Plan ở trên là plan 14 task thật đã dựng nên bộ kit này, xem trong Agent Observatory: mỗi thẻ là một task với trạng thái, agent và token đã dùng; cạnh nối là phụ thuộc.*

## Tính năng

### 1. Tự động điều hướng theo luồng

Mỗi yêu cầu mới được xếp vào một trong **30 luồng** trước khi agent đọc hay sửa bất cứ file nào. Agent nhắc lại yêu cầu, đề xuất một luồng kèm các phương án gần nhất, nên hiểu sai được phát hiện bằng một cú click thay vì mười file đã sửa.

```
Bạn:    Người dùng xem được hồ sơ của phòng ban khác trên màn hình báo cáo.
Agent:  Bạn muốn người dùng chỉ thấy hồ sơ phòng mình. Chọn luồng nào?
          > Phân quyền · #12 (Khuyên dùng): CRITICAL, bạn duyệt đề xuất trước khi sửa, test thật bằng tài khoản thường
            Sửa bug · #6: viết test đỏ trước
            Sửa giao diện · #15: chỉ frontend
```

Mỗi luồng có mức rủi ro riêng: sửa một dòng phân quyền vẫn đi đủ quy trình, còn sửa một lỗi chính tả thì không.

| Nhóm | Luồng |
|---|---|
| **Từ ý tưởng đến bàn giao** | Yêu cầu mơ hồ (làm rõ phạm vi trước) · Tính năng lớn một phía · Tính năng cả backend + frontend · Review code · Commit / push / release |
| **Việc rủi ro cao** | Sự cố production · Sửa bug · Module CRUD mới · Schema / migration · Ghi dữ liệu database · Phân quyền · Endpoint công khai · Config dùng chung |
| **Việc nhỏ, không cần plan** | Lỗi chính tả / format / đổi tên · Code backend thường · Viết test · Sửa giao diện |
| **Độc lập** | Hỏi đáp · Nghiên cứu nhiều file · Mockup / prototype · Tài liệu · Giải conflict · Nâng cấp lớn · Tiến độ plan · Thuật ngữ và ADR · Kiến trúc (C4) · Kiểm thử bảo mật · Lộ trình dự án |

Đã biết luồng? Gõ `Flow: #6` hoặc gọi tên skill, agent bỏ qua câu hỏi.

### 2. Agent biết mình đang ở đâu: dự án, plan và task

Tiến độ được ghi vào file Markdown và JSON trong repo, không nằm trong chat. Hỏi bằng lời thường, agent trả lời từ các file đó:

| Bạn hỏi | Cấp | Trả lời từ |
|---|---|---|
| "Dự án làm đến đâu rồi? Còn thiếu gì?" | **Dự án** | `docs/du-an/lo-trinh.json`: mục tiêu, rồi các hạng mục (module hoặc giai đoạn) của từng mục tiêu, mỗi mục có trạng thái, người phụ trách, hạn và tiêu chí nghiệm thu |
| "Đang ở bước nào trong plan?" | **Plan** | `docs/artifact/<ngày-tên>/plan.md`: các task và phụ thuộc giữa chúng |
| "Còn việc gì? Việc gì bị chặn? Việc gì chờ tôi?" | **Task** | `progress.md`: mỗi task một dòng, trạng thái `TODO` · `DOING` · `CHỜ-USER` · `BLOCKED` · `DONE` · `SKIP` |
| "Bước tiếp theo là gì?" | **Task** | các task sẵn sàng, xếp theo số task đang chờ phía sau |

- **Không mất tiến độ khi nén context hay mở phiên mới.** Sau khi nén context, agent làm tiếp plan đang chạy từ `intent.md` và `progress.md` thay vì làm lại từ đầu; phiên mới cũng nối tiếp từ các file đó.
- **Việc nhiều bước luôn có plan trước.** `plan.md` và `progress.md` được viết và duyệt trước task 1, cập nhật sau mỗi task.
- **Phạm vi được ghi rõ.** `intent.md` liệt kê các file agent được sửa; ngoài danh sách phải được bạn đồng ý đích danh.

### 3. Theo dõi trực tiếp: Agent Observatory

Cùng các file đó hiển thị trên dashboard chạy local: tổng quan, Kanban, danh sách mission, đồ thị plan, timeline, lộ trình, thống kê agent và skill.

- **Bộ đếm "Cần bạn"** trên tiêu đề tab cho task đang chờ bạn hoặc bị chặn, có thể bật thông báo trình duyệt.
- **Token và chi phí theo task**, cảnh báo khi token tăng vọt, vượt ngân sách hoặc agent đứng im.
- **Cảnh báo quy trình**: task báo xong mà không chạy kiểm tra sau lần sửa cuối, một file bị sửa đi sửa lại, lệnh lỗi chạy lại mà không đổi gì.
- **Export Summary** ghi báo cáo mission vào thư mục của nó.

**[Tải bản cài Windows mới nhất](https://github.com/zzgiabaozzbui/athena-starter/releases/latest)** (`Agent-Observatory-Setup-*.exe`). Chỉ đọc, chạy local trên 127.0.0.1, không gửi gì ra ngoài. Hoặc cài dạng plugin: `/plugin install athena-observatory@athena`.

### 4. Hàng rào an toàn

- Bug fix và thay đổi hành vi bắt đầu bằng một test đỏ đúng lý do.
- Thay đổi rủi ro được một reviewer với context mới kiểm tra; "xong" phải có kết quả kiểm tra của chính lượt đó.
- Hook chặn `git commit --no-verify` và các sửa đổi nới lỏng config lint.
- Quét dữ liệu nhạy cảm trước mỗi commit; push, tag, release luôn chờ bạn đồng ý.

Chi tiết về 18 skill, 6 agent, 3 hook và cách tùy biến: xem [README tiếng Anh](README.md).

## Giấy phép

MIT; các nguồn bên thứ ba được ghi trong [`NOTICE`](NOTICE).
