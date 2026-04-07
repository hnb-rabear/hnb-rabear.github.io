# Action Items & Session Log

## TODO
- [x] **Slide 14 (Editor Tools → Demo Transition)**: Làm lại thành transition slide cho live demo
- [x] Final review toàn bộ slides — so sánh với docs, không phát hiện lỗi nghiêm trọng
- [ ] **Docs: index.md §Nhược điểm**: Limitations hiện tại bị bịa — cần viết lại dựa trên kinh nghiệm thực tế
- [ ] Commit memory files + push

---

## Session Log

### 2026-04-07 (chiều) — Slide 14 + Full Review
- Slide 14: redesign từ "Editor Tools" (screenshots) → "Demo: Dự án thực tế" (4 cards roadmap cho live demo)
- Full review 15 slides vs docs (index.md, dynamic_player_data.md, static_config_data.md, project_basic_setup): OK, không lỗi nghiêm trọng
- Phát hiện: §Nhược điểm trong docs là fabricated → cần rewrite
- Quyết định: giữ code simple trong slides, không thêm Limitations slide, LiveOps Template ngoài scope

### 2026-04-07 — Hoàn thiện slides Data Management
- Replace ALL fabricated code bằng code thật từ Goods-Jam (PlayerData, PlayerModel, SetCurrency, DispatchEvent, OnPostLoad, partial files, SaveDataCollection)
- Sửa nội dung sai: team size 3-5→2-3, trade-offs giả→6 engineering benefits thật, xóa AutoFill/Goods-Jam references
- Thêm: OnRemoteConfigFetched lifecycle hook, MVP overview ở Takeaways
- Khởi tạo Agent Memory system (4 files)
- Đọc brainstorming + writing-plans skills → sẵn sàng áp dụng cho Slide 14
