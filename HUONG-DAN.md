# Hướng dẫn sử dụng (tiếng Việt)

Repo này là bản sao của [mattpocock/skills](https://github.com/mattpocock/skills). Đây **không phải một app để chạy**, mà là một bộ **"skill"** (các file `SKILL.md` viết bằng Markdown) dành cho các coding agent như Claude Code, Codex, GitHub Copilot, Cursor, Gemini CLI...

Mỗi skill là một bộ chỉ dẫn giúp agent làm việc có kỷ luật hơn: hỏi kỹ yêu cầu trước khi code, viết test trước (TDD), chia việc thành ticket, review code, gỡ bug có phương pháp...

## 1. Cài đặt

Chọn **một** cách cho mỗi agent (cài cả hai sẽ bị trùng skill).

### Cách A: Claude Code, bản gốc (tự cập nhật)

```bash
claude plugin install mattpocock-skills@claude-plugins-official
```

### Cách B: Claude Code, dùng bản sao của bạn (`pichu124/skills`)

Dùng cách này nếu bạn muốn tự sửa skill theo ý mình. Trong Claude Code gõ:

```
/plugin marketplace add pichu124/skills
/plugin install mattpocock-skills@mattpocock
```

(`mattpocock` là tên marketplace khai báo trong `.claude-plugin/marketplace.json`.)

### Cách C: Chép thẳng file vào máy (sửa trực tiếp được)

```bash
git clone https://github.com/pichu124/skills.git
cd skills
bash scripts/link-skills.sh
```

Script tạo symlink từng skill vào `~/.claude/skills` (Claude Code) và `~/.agents/skills` (Codex và các agent khác). Sau này chỉ cần `git pull` là skill được cập nhật.

### Agent khác (Cursor, Windsurf, OpenCode...)

```bash
npx skills@latest add mattpocock/skills -a cursor
```

Khi được hỏi chọn skill nào, nhớ chọn cả `setup-matt-pocock-skills`.

## 2. Thiết lập một lần cho mỗi project

Mở agent trong thư mục project của bạn và gõ:

```
/setup-matt-pocock-skills
```

Nó sẽ hỏi bạn 3 điều:

1. **Issue tracker**: lưu ticket ở đâu (GitHub Issues, GitLab, file Markdown trong `.scratch/`, hoặc Jira/Linear...). Làm một mình thì chọn **Local markdown** cho đơn giản.
2. **Nhãn triage**: cứ chọn **yes** để dùng nhãn mặc định.
3. **Nơi lưu tài liệu**: mặc định `GLOSSARY.md` và `docs/adr/` ở thư mục gốc.

Kết quả là các file `docs/agents/*.md` và một mục `## Agent skills` trong `CLAUDE.md` hoặc `AGENTS.md`.

> Lưu ý: nếu cài qua plugin, lệnh có thể hiện dưới dạng `/mattpocock-skills:setup-matt-pocock-skills`. Gõ `/` rồi tìm tên skill là thấy.

## 3. Quy trình làm việc chính: từ ý tưởng đến code

Không nhớ dùng skill nào thì cứ gõ **`/ask-matt`** và mô tả tình huống, nó sẽ chỉ đường.

| Bước | Lệnh | Làm gì |
|---|---|---|
| 1 | `/grill-with-docs` | Agent "tra hỏi" bạn về ý tưởng cho đến khi mọi chỗ mơ hồ được làm rõ. Đồng thời ghi thuật ngữ vào `GLOSSARY.md` và quyết định quan trọng vào ADR. |
| 2 | `/to-spec` | Gom cuộc trò chuyện thành một bản spec và đăng lên issue tracker. |
| 3 | `/to-tickets` | Chia spec thành các ticket nhỏ, mỗi ticket ghi rõ phụ thuộc vào ticket nào. |
| 4 | `/implement` | Code từng ticket: viết test trước (`/tdd`), rồi tự review (`/code-review`) trước khi commit. Gõ `/clear` giữa các ticket. |
| 4' | `/implement-spec` | Thay cho bước 4: làm cả spec trong một lần, chạy nhiều subagent song song. |
| 5 | `/retro` | Sau khi xong, gợi ý cải thiện môi trường làm việc của agent (lint, coding standard, CLAUDE.md...). |

Việc nhỏ, làm trong một phiên là xong? Bỏ qua bước 2, 3: chạy `/grill-with-docs` rồi `/implement` luôn.

**Mẹo:** giữ bước 1 đến 3 trong **cùng một phiên** (đừng `/clear` hay `/compact` giữa chừng) để spec và ticket dựa trên đúng những gì đã bàn.

## 4. Các skill hay dùng khác

| Tình huống | Lệnh |
|---|---|
| Muốn được hỏi vặn về một kế hoạch, không cần repo (viết lách, kế hoạch kinh doanh...) | `/grill-me` |
| Có bug khó, lúc được lúc không | `/diagnosing-bugs` |
| Có nhiều bug report / feature request người khác gửi | `/triage` |
| Dự án quá lớn, chưa rõ hướng đi | `/wayfinder` |
| Muốn tìm chỗ nên refactor trong codebase | `/improve-codebase-architecture` |
| Chuyển việc sang phiên/agent khác | `/handoff` |
| Agent nói gì đó khó hiểu | `/wait-what` |
| Muốn học một kiến thức mới qua nhiều buổi | `/teach` |
| Cần hỏi ý kiến người khác bằng bảng câu hỏi | `/to-questionnaire` |

Một số skill (như `tdd`, `prototype`, `code-review`, `pr`, `research`) là **model-invoked**: agent tự gọi khi thấy phù hợp, bạn không cần nhớ. Bạn vẫn có thể gõ trực tiếp nếu muốn.

## 5. Ví dụ thực tế

```
Bạn:   /grill-with-docs
       Mình muốn thêm tính năng đăng nhập bằng Google cho app.
Agent: (hỏi lần lượt: lưu user ở đâu, xử lý user cũ thế nào, đăng xuất ra sao...)
Bạn:   (trả lời từng câu)
Bạn:   /to-spec
Bạn:   /to-tickets
Bạn:   /clear
Bạn:   /implement   (chỉ vào ticket đầu tiên)
```

Bạn có thể nói chuyện với agent bằng tiếng Việt bình thường.

## 6. Cấu trúc repo

```
skills/
  engineering/    skill dùng hằng ngày cho việc code (được đóng gói trong plugin)
  productivity/   skill dùng hằng ngày, không riêng cho code (được đóng gói trong plugin)
  misc/           ít dùng, không đóng gói
  in-progress/    bản thử nghiệm
  deprecated/     đã ngừng dùng
docs/             tài liệu giải thích từng skill (tiếng Anh)
.claude-plugin/   khai báo plugin cho Claude Code
scripts/          script tiện ích (link-skills.sh, list-skills.sh)
```

Muốn tự sửa một skill: mở `skills/<nhóm>/<tên-skill>/SKILL.md` và sửa nội dung Markdown.

## 7. Cập nhật từ bản gốc

```bash
git remote add upstream https://github.com/mattpocock/skills.git   # chỉ cần làm 1 lần
git fetch upstream
git merge upstream/main
```
