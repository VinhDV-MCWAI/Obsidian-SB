---
chu_de: ket-noi-ai
ap_dung: [may-moi]
muc_do: nen-tang
cap_nhat: 2026-10-26
---
# Kết nối Playbook với AI (Claude Code)

Mục tiêu: AI ở **mọi dự án** biết Playbook tồn tại, chỉ đọc khi cần, và không thêm file nào vào repo của dự án.

## Cách làm: một "kỹ năng" cấp người dùng

Claude Code đọc kỹ năng ở `~/.claude/skills/<tên>/SKILL.md` cho mọi dự án trên máy. Lúc thường chỉ nạp **dòng mô tả** (~150 token); nội dung chỉ nạp khi việc khớp mô tả hoặc khi gõ `/playbook`. **Đã cài** trên máy này; sửa kỹ năng thì sửa cả file `SKILL.md` lẫn khối dưới đây cho giống nhau.

Nội dung `~/.claude/skills/playbook/SKILL.md` (sửa `PLAYBOOK_DIR` cho đúng máy):

```markdown
---
name: playbook
description: Owner's personal engineering playbook (technology-agnostic standards, guardrail checklists and "my choice" decisions). Use whenever a task touches auth, permissions, user input / validation, DB schema changes or deletes, money or concurrency, file upload, API errors / response format, release / config / secrets, or logging of personal data — even a small one; also for multi-part features, when setting up or re-joining a project, and when the owner asks for a playbook batch update or an end-of-project CV summary. Skip small, clear tasks that touch none of these.
---

Playbook directory: PLAYBOOK_DIR=/mnt/c/Users/Dell/Downloads/Obsidian-SB/Playbook (Vietnamese, Markdown, Obsidian vault).

Order of reference: the project's own code and docs → `.me/` → the playbook → your own knowledge → the internet. Go to the next step only when the previous one is not enough.

1. First read `01 Cách làm việc/Khi nào dùng Playbook.md` and decide: does the task touch a guardrail topic (auth, permissions, user input, DB changes or deletes, money / concurrency, file upload, errors / API responses, release / config / secrets, logs with personal data, sending out mail / webhooks / third-party calls, background jobs, private data reaching public pages / search / exports)? If yes, the matching checklist is mandatory. Otherwise pick level 0–3 from that file; level 0 means do not use the playbook at all. Planning is your call; the playbook only informs it, except that DB / auth / permission / dependency / API-contract / irreversible changes always need the owner's approval first. State the decision in one line at the top of the reply ("Playbook: level 1 — …" or "Playbook: not used — …").
2. New project, or no `.me/` yet, and the owner asks to set it up: follow `02 Tiếp cận dự án mới/Thiết lập theo loại dự án.md` (scan structure only, report the project type and wait for confirmation; create `.me/`, add `.me/` to `.git/info/exclude`).
3. Read `.me/Hồ sơ dự án.md` and `.me/Bản đồ.md` if they exist. The project's own rules (its CLAUDE.md, team conventions, `.me/`) win over the playbook; honour the items it switches off. If the project already has a complete workflow of its own, skip the playbook's workflow files and use only its topic checklists.
4. Open files straight from the tables in `Khi nào dùng Playbook.md`; read `Mục lục cho AI.md` only at level 2–3. Never read the whole folder. If `.me/Hồ sơ dự án.md` lists what the playbook is used for in this project, stay within that list (plus guardrails).
5. Plan as you see fit; stop for the owner's approval on the changes listed in step 1. Model fit (see "Model có hợp với việc không" in `Khi nào dùng Playbook.md`): you cannot switch the session model yourself. Rule: the lowest model tier that can do the job (tiers in `Hướng dẫn sử dụng.md` section 2: fast / balanced = default / strongest only when needed). If the running model is below the tier the task needs, stop before working and suggest the lowest sufficient tier (not automatically the strongest) with a prompt to paste; if it is far above what the task needs, say so in one line; if you show signs of struggling (repeated mistakes, contradictions, missed guardrail lines), say so and suggest one tier up. Otherwise say nothing. Delegate wide scans, searches and mechanical sub-tasks to a fast-model sub-agent when the tool supports it — including the structure scan when setting up a heavy project.
6. Before reporting done, go through every line of the chosen checklists (done / not applicable / skipped with reason / not done), as in `01 Cách làm việc/Đối chiếu checklist và minh chứng.md`. "Done" needs a checkable location (file, test, command). In the reply list only not-done and skipped lines, plus done lines about security, permissions, data or money with their location; summarise the rest as counts.
7. Learned something reusable (technology, approach, solution, common mistake)? Append ONE generalised line to `Hộp đề xuất/Ghi chú.md` — no company, client, system structure, vulnerability, code or numbers; no web search; do not edit other playbook files. Project-only facts go to `.me/Ghi chú dự án.md`. If the note is genuinely useful, say so in one line at the end of the reply.
8. Never write playbook or `.me/` content into the project's repository.
9. Playbook batch updates and the end-of-project CV summary run only when the owner asks (`01 Cách làm việc/Ghi chú và cập nhật theo đợt.md`, `01 Cách làm việc/Thống kê công việc cho CV.md`).
```

## Ở máy công ty

- Copy `Playbook/` vào một chỗ cá nhân **ngoài mọi repo của công ty**, sửa `PLAYBOOK_DIR`.
- Ghi chú mới trong `Hộp đề xuất/Ghi chú.md` của bản copy → chép về Obsidian khi chạy đợt cập nhật.
- Kiểm tra chính sách công ty về dùng AI với source của công ty trước.

## Thử đã kết nối chưa

Trong một dự án: *"Dùng playbook: tạo form đổi email."* AI phải nói đã đọc mục lục, mở `Validate - Email`, và cuối việc có bảng đối chiếu.

## Công cụ AI khác

Nội dung trên là chỉ dẫn thuần, dán được vào phần quy tắc **cá nhân** của công cụ khác — không đặt vào file của repo.
