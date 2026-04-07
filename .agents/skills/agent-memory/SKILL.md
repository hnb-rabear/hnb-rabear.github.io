---
name: agent-memory
description: Read and update project memory files at start/end of every session. Memory lives in .agent/memory/ and syncs across machines via git.
---

# Agent Memory System

## MANDATORY: Start of Session (khi user nói "đọc memory")
1. Read ALL files in `.agent/memory/` directory:
   - `project_context.md` — TL;DR của dự án (stack, kiến trúc, version)
   - `developer_profile.md` — Developer preferences, coding style, workflow rules
   - `action_items.md` — TODO list + mini session log (phiên gần nhất)
   - `decisions_log.md` — Past decisions (KHÔNG hỏi lại những điều đã chốt)

2. Read project docs:
   - `TECHNICAL.md` — Tài liệu kỹ thuật đầy đủ (patterns, API, CSS conventions)
   - `CHANGELOG.md` — Chỉ đọc entry mới nhất (biết version hiện tại + thay đổi gần nhất)

## MANDATORY: End of Session (khi user nói "lưu memory" hoặc trước khi push)
1. Update `action_items.md` — Mark completed items, add new TODOs, add session log entry at TOP
2. Update `decisions_log.md` — Log any new architectural/design decisions made
3. Update `developer_profile.md` — ONLY if new preferences discovered (rare)
4. Save **Implementation Plan**: Nếu phiên có tạo ra bản kế hoạch (Implementation Plan), phải lưu/copy nội dung bản kế hoạch đó thành file markdown mới trong thư mục `.agent/memory/plans/YYYY-MM-DD_plan_name.md`

## Rules
- Memory files are in **Vietnamese** (developer's preferred language)
- New entries go at the **TOP** of each file (reverse chronological)
- Every entry needs a **timestamp** (date at minimum)
- **Never delete** old entries casually — but **trim periodically**:
  - `action_items.md`: giữ ~10 session gần nhất, xóa cũ hơn (git giữ history)
  - `decisions_log.md`: giữ ~20 decisions gần nhất
  - `project_context.md` và `developer_profile.md`: overwrite, không append
- Keep entries **concise** — key facts only, not full transcripts
- Do NOT duplicate TECHNICAL.md content into memory (TECHNICAL.md is the single source of truth for project architecture)
- Do NOT duplicate CHANGELOG.md content (CHANGELOG is the single source of truth for version history)

## Why This System Exists
Developer works across multiple computers. Antigravity's built-in Knowledge Items
are machine-local and don't sync. These files live in the git repo and travel
with the project to any machine.
