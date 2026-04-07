---
name: brainstorming
description: "MANDATORY — STOP and activate BEFORE writing ANY code for new features, significant changes, or multi-file modifications. You MUST ask clarifying questions, explore alternatives, and get explicit design approval first. Do NOT skip this step. Do NOT start coding without approval."
---

# Brainstorming Ideas Into Designs

> Adapted from [superpowers:brainstorming](https://github.com/obra/superpowers)

## The Iron Rule

```
NEVER start coding before the design is approved.
```

Even "simple" changes benefit from 2 minutes of design thinking.

## Anti-Pattern: "This Is Too Simple To Need A Design"

If you're thinking this, you're wrong. Every feature that touches multiple files, modifies data flow, or affects UX deserves a brief design.

## The Process

### 1. Understand the Request
- What problem does this solve?
- Who needs it? (the user managing Kyberwave library)
- What's the current workaround?

### 2. Ask Clarifying Questions
- Don't assume. Ask about edge cases, scope, and priorities
- Example: "Should this work for both album and staging tracks?"
- Example: "What should happen when the LLM API is down?"

### 3. Explore Alternatives
- Present 2-3 approaches with trade-offs
- Consider: complexity, performance, future extensibility
- Reference existing patterns in OAC Studio

### 4. Present Design in Digestible Chunks
Break the design into sections short enough to actually read:
- **Data model changes** (if any) — new columns, new tables
- **API changes** — new endpoints, modified endpoints
- **UI changes** — new pages, modified pages, new components
- **Integration points** — how it connects to existing features

### 5. Validate
- Get explicit approval before proceeding
- If the user has concerns, iterate on the design

## After the Design

1. Save design document to `docs/plans/YYYY-MM-DD-<feature>-design.md`
2. Use the writing-plans skill to create an implementation plan
3. Execute the plan

## OAC Studio Design Checklist

- [ ] Does this affect the database schema? → Plan migration in `database.py`
- [ ] Does this need a new API endpoint? → Which router? (`library`, `staging`, `playlists`, `mv_maker`, `prompt_gen`)
- [ ] Does this need a new frontend page? → Register in `router.js`, add nav link in `index.html`
- [ ] Does it affect the audio player? → Check `player.js` integration
- [ ] Does it need LLM integration? → Which models? Check `llm_providers.py`
- [ ] Does it interact with the file system? → Windows path handling, Google Drive latency
- [ ] Does it affect existing features? → Review `TECHNICAL.md` for dependencies
- [ ] Is it in the `ROADMAP.md`? → Reference the relevant phase/item

## Red Flags

| Thought | Reality |
|---------|---------|
| "This is just a simple change" | Simple changes break complex systems. Design first. |
| "I already know what to do" | Knowing ≠ designing. Write it down. |
| "The user just wants it done fast" | Fast without design = rework later. |
| "Let me just start coding" | STOP. Design first, always. |
