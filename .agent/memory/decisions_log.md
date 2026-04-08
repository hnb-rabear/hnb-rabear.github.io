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

### DEC-007: Narrative & Terminology
- **Quyết định**: Sử dụng thuật ngữ chuyên môn (Spaghetti, God Class, Decoupled, Separation of Concerns, Single Point of Access, Event-Driven) và ngôn ngữ phản ánh đúng đau đầu thực tế (ví dụ: "Code dính chặt").
- **Lý do**: Cân bằng giữa đối tượng Junior (học thuật ngữ) và Senior (quen thuộc chuẩn ngành), tạo tính chuyên nghiệp và tính thực tế cho bài thuyết trình.

### DEC-008: Professional Terminology & Traceability (Audit)
- **Quyết định**: Nâng cấp toàn bộ văn phong và thuật ngữ sang chuẩn Senior (Data Gateway, Traceability, Cascading failures, God Object). Đồng nhất các ví dụ code mâu thuẫn (số nhiều `coins`, `lives`, `purchasedIapIds`). Giữ nguyên kiểu dữ liệu `int` cho Timestamp.
- **Lý do**: Tăng tính chuyên nghiệp và "credibility" cho bài thuyết trình. Việc dùng `int` cho timestamp là do giới hạn của Unity Serialization thực tế, phản ánh kinh nghiệm production thay vì lý thuyết suông.

### DEC-009: Không sử dụng TECHNICAL.md và CHANGELOG.md
- **Quyết định**: Không duy trì các file tài liệu kỹ thuật phụ (`TECHNICAL.md` và `CHANGELOG.md`) ở root.
- **Lý do**: Yêu cầu trực tiếp từ người dùng (User preference). Bối cảnh kỹ thuật sẽ được duy trì thông qua Agent Memory và nội dung chính của site.
