# Action Items & Session Log

## TODO
- [x] **Slide 14 (Editor Tools → Demo Transition)**: Làm lại thành transition slide cho live demo
- [x] Final review toàn bộ slides — so sánh với docs, không phát hiện lỗi nghiêm trọng
- [x] **Trang bị thuật ngữ chuyên ngành**: Tinh chỉnh lại nội dung để nâng cao tính kỹ thuật và chuyên nghiệp
- [x] **Kịch bản thuyết trình (Transcript)**: Soạn thảo chi tiết 30-45 phút cho 15 slides
- [ ] **Docs: index.md §Nhược điểm**: Limitations hiện tại bị bịa — cần viết lại dựa trên kinh nghiệm thực tế
- [ ] Commit memory files + push

---

## Session Log

### 2026-04-08 (tối) — Professional Audit & Transcript
- Thực hiện Audit chuyên sâu về thuật ngữ Engineering (Traceability, God Object, Cascading failures, Data Gateway).
- Đồng nhất hệ thống biến trong code ví dụ: đổi `coin` -> `coins`, `live` -> `lives`, chuẩn hoá `purchasedIapIds`.
- Chốt kịch bản thuyết trình (Transcript) chi tiết 30-45 phút, tích hợp Demo Unity & SheetX.
- Giải quyết vấn đề kiểu dữ liệu TimeStamp (giữ `int` do giới hạn Unity Serialization).

### 2026-04-08 — Tinh chỉnh Narrative & Terminology (Slides)
- Đổi cách tiếp cận Bối cảnh & Tiến hóa bằng "Pain-Point-Driven" narrative: nhấn mạnh vào Scale, Tight Coupling, God Class.
- Chuẩn hóa thuật ngữ ở Slide 4 (Separation of Concerns, Single Point of Access, Event-Driven).
- Sửa đổi các Slide Insight (10 & 13) để phản ánh thực tế về "tight coupling" thay vì hứa hẹn "0 file cũ bị sửa".
- Thiết lập lại Slide 6 thành "Patterns Roadmap" với định hướng đặt câu hỏi dẫn dắt vào bài.
- Thống nhất hàm SetCurrency tại Slide 5 để khớp thật tế.

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
