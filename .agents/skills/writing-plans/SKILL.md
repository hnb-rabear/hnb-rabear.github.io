---
name: writing-plans
description: Use when planning implementation of a feature, refactoring, or multi-step change — creates bite-sized tasks with exact file paths, code, and verification steps
---

# Writing Plans

> Adapted from [superpowers:writing-plans](https://github.com/obra/superpowers)

## Overview

Write comprehensive implementation plans assuming the engineer has zero context. Document everything: which files to touch, exact code, how to verify. Plans are bite-sized tasks (2-5 minutes each).

## Principles

- **DRY** — Don't Repeat Yourself
- **YAGNI** — You Aren't Gonna Need It
- **Frequent commits** — Commit after each logical step

## Save Plans To

```
docs/plans/YYYY-MM-DD-<feature-name>.md
```

## Plan Document Header

Every plan MUST start with:

```markdown
# [Feature Name] Implementation Plan

**Goal:** [One sentence — what this builds]

**Architecture:** [2-3 sentences about approach]

**Files affected:**
- Create: `exact/path/to/file.py`
- Modify: `exact/path/to/existing.py`

---
```

## Task Structure

Each task should be self-contained:

````markdown
### Task N: [Component Name]

**Files:**
- Create: `backend/routers/new_router.py`
- Modify: `backend/main.py` (add router mount)
- Modify: `frontend/src/pages/new-page.js`

**Step 1: Backend — Create API endpoint**

```python
@router.get("/api/new-endpoint")
async def new_endpoint(db: AsyncSession = Depends(get_db)):
    result = await db.execute(select(Model))
    return result.scalars().all()
```

**Step 2: Frontend — Create page module**

```javascript
export async function render(container) {
    const data = await api.get('/api/new-endpoint');
    container.innerHTML = `...`;
}
```

**Verify:**
1. `python main.py` — starts without errors
2. Open `http://localhost:5174/#/new-page` — page renders
3. Check Network tab — API call succeeds
````

## Scope Check

If the feature spans multiple independent subsystems, break into separate plans. Each plan should produce working, testable software on its own.

## OAC Studio Conventions

- **Backend routers** go in `backend/routers/` — mount in `main.py`
- **Frontend pages** go in `frontend/src/pages/` — register in `router.js`
- **API prefix**: `/api/` — frontend uses relative URLs via Vite proxy
- **State**: Global `state.js` object, no reactive system
- **Styling**: All CSS in `frontend/src/style.css` using Cyberpunk design tokens
- **Database**: Async SQLAlchemy — use `db: AsyncSession = Depends(get_db)`
