---
description: MANDATORY pre-push workflow. PHẢI dùng workflow này thay vì chạy `git push` trực tiếp. Đảm bảo TECHNICAL.md và CHANGELOG.md luôn được cập nhật trước khi push.
---

# /push — Push to Remote (with Doc Gate)

> **IRON RULE:** KHÔNG BAO GIỜ chạy `git push` trực tiếp. Luôn dùng `/push` workflow này.

## Quy trình

### Bước 1: Kiểm tra diff
// turbo
```bash
git log --oneline origin/main..HEAD
git diff --stat origin/main..HEAD
```

Đọc output → xác định files đã thay đổi.

### Bước 2: Doc Sync (skill: doc-sync-on-change)

Chạy checklist từ skill `doc-sync-on-change`:

1. **LIST** — Files nào đã thay đổi?
2. **MAP** — Sections nào trong TECHNICAL.md bị ảnh hưởng?
3. **UPDATE** — Cập nhật TECHNICAL.md
4. **VERIFY** — Đọc lại sections đã update, confirm chính xác

Nếu TECHNICAL.md đã được cập nhật trong commit trước → skip bước này nhưng phải XÁC NHẬN bằng cách đọc file.

### Bước 3: Changelog (skill: changelog-before-push)

Chạy quy trình từ skill `changelog-before-push`:

1. **CATEGORIZE** — Phân loại thay đổi (✨/🐛/⚡/🔧/📱)
2. **VERSION** — Xác định bump PATCH/MINOR/MAJOR
3. **DRAFT** — Viết draft entry
4. **APPROVE** — Trình bày cho user, chờ xác nhận
5. **WRITE** — Chèn vào CHANGELOG.md

Nếu CHANGELOG.md đã được cập nhật trong commit trước → skip nhưng phải XÁC NHẬN.

### Bước 4: Commit docs (nếu có thay đổi)

```bash
git add TECHNICAL.md CHANGELOG.md
git status
```

Nếu có thay đổi:
```bash
git commit -m "docs: update TECHNICAL.md and CHANGELOG.md for vX.Y.Z"
```

### Bước 5: Push

```bash
git push origin main
```

## Checklist trước khi push

- [ ] TECHNICAL.md đã phản ánh đúng code hiện tại?
- [ ] CHANGELOG.md có entry cho version mới?
- [ ] Version number khớp giữa CHANGELOG.md và TECHNICAL.md?
- [ ] User đã approve changelog draft?

## Red Flags — DỪNG NGAY

| Tình huống | Hành động |
|---|---|
| Sắp chạy `git push` mà chưa qua workflow | **DỪNG. Chạy /push** |
| "Chỉ là fix nhỏ, push nhanh" | Vẫn phải qua /push — fix nhỏ vẫn cần docs |
| "Docs đã update rồi" | XÁC NHẬN bằng cách đọc file, không đoán |
| User bảo "push luôn đi" | Vẫn phải check docs — draft ngắn hơn thôi |
