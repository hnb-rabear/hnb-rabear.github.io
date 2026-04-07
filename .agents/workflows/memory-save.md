---
description: Lưu thông tin công việc, TODO, quyết định thiết kế vào .agent/memory/ cuối mỗi session hoặc trước khi push code.
---

# /memory-save — Save Session Memory

Quy trình này hướng dẫn Agent cập nhật các file trong `.agent/memory/` để duy trì tiến độ công việc liên tục xuyên biên giới giữa các Agent/phiên làm việc (session). **Không nhầm lẫn workflow này với việc update TECHNICAL.md và CHANGELOG.md cho repo chính (như yêu cầu của lệnh /push).**

## Quy trình Xử lý

Phân tích ngay những tính năng và tác vụ bạn vừa hoàn tất với developer và tiến hành cập nhật nhanh chóng (sử dụng `write_to_file` hoặc `multi_replace_file_content`):

1. **`action_items.md` (BẮT BUỘC):** 
   - Đánh dấu `[x]` vào các công việc đã xong trong TODO. 
   - Thêm vào các TODO mới (nếu có). 
   - Cập nhật mục nhật ký phiên (Session Log) mới nhất lên **TOP** (ghi ngày - tính năng, tóm tắt 3-5 dòng siêu ngắn gọn)
   - Lưu ý: Giữ lượng log từ ~10 phiên, cũ hơn thì chủ động xoá bỏ để file không quá dài.

2. **`decisions_log.md` (NẾU CÓ THAY ĐỔI LỚN):** 
   - Điền thêm quyết định kỹ thuật / kiến trúc khó, đã quy trách nhiệm với user và chốt lại để không lặp lại câu hỏi.

3. **`project_context.md` (NẾU DỰ ÁN MỞ RỘNG):** 
   - Sửa nếu phiên làm việc thay đổi Version (Vd: v3.3.0 -> v3.4.0) hoặc sửa/thêm core features. Đừng copy paste từ Technical.md qua.

4. **`developer_profile.md`:** (Hiếm khi đụng đến) Chỉ update nếu Developer vừa dặn dò bạn một rule chung mới, bất di bất dịch.

5. **Lưu Kế hoạch thi công (`.agent/memory/plans/`):** NẾU trong phiên làm việc hiện tại có sinh ra bảng Kế hoạch (Implementation Plan), bạn PHẢI copy lại toàn bộ văn bản nội dung của file Plan đó và lưu thành một file thực tế vào dự án tại vị trí: `.agent/memory/plans/YYYY-MM-DD_ten_ngan_gon_cua_plan.md`. (Nhớ tạo thư mục nếu chưa có).

## Hoàn thành
- Đưa ra báo cáo rành mạch là Agent đã lưu memory xong.
- Sẵn sàng push code nếu developer quyết định.
