# Decisions Log

## 2026-04-07 — Session: Slides Data Management

### DEC-001: Generic concepts vs Tool-specific names
- **Quyết định**: Slide dùng generic names (DataModel, DataHandler, DataManager) cho patterns. Tool-specific names (SheetX, JObjectDB) chỉ xuất hiện khi giới thiệu tool cụ thể.
- **Lý do**: Presentation dạy concepts, không phải demo framework.

### DEC-002: Code examples từ project thật
- **Quyết định**: Code trong slides phải lấy từ project thực tế (Goods-Jam), KHÔNG dùng fabricated code.
- **Lý do**: Code giả dễ sai sự thật, mất credibility.

### DEC-003: Không nhắc tên project
- **Quyết định**: Dù code lấy từ Goods-Jam, không nhắc tên project trong slides.
- **Lý do**: Slides là generic knowledge sharing, không phải project-specific demo.

### DEC-004: MVP chỉ giới thiệu sơ qua
- **Quyết định**: MVP (Model-View-Presenter) chỉ nêu 3 dòng ở Takeaways slide. Không mở rộng thành slides riêng.
- **Lý do**: Slide tập trung vào Data Management + SheetX. MVP sẽ là topic riêng.

### DEC-005: Không có trade-offs giả
- **Quyết định**: Xóa toàn bộ "nhược điểm" không liên quan đến data management (encrypt, cloud save, singleton mock...). Thay bằng 6 engineering benefits thật.
- **Lý do**: Trade-offs phải dựa trên thực tế, không bịa.

### DEC-006: CSS - No padding on sections
- **Quyết định**: Không dùng padding trên `.reveal .slides section`. Dùng margin trên child elements nếu cần spacing.
- **Lý do**: Padding conflict với Reveal.js center:true → gây horizontal layout shift.
