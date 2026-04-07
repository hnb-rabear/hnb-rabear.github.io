---
description: Đọc nội dung bộ nhớ Agent (Memory) + TECHNICAL.md và CHANGELOG.md để tải lại bối cảnh (context) đầu session.
---

# /memory-load — Load Session Memory

Quy trình này hướng dẫn Agent đọc các tài liệu quan trọng để khôi phục bối cảnh dự án, trạng thái công việc và các quy chuẩn làm việc trước khi thực hiện viết code hay sửa lỗi.

## Bước 1: Đọc các file Memory
Sử dụng công cụ `view_file` để đọc tuần tự 4 file trong thư mục `.agent/memory/`:
1. `project_context.md` — TL;DR tóm tắt dự án (stack, kiến trúc, tính năng chính).
2. `developer_profile.md` — Framework/ngôn ngữ/quy tắc UI được yêu cầu, cùng điều thích/ghét.
3. `action_items.md` — TODO list chưa xong và phiên làm việc gần nhất.
4. `decisions_log.md` — Tránh thảo luận lại những kiến trúc hay thiết kế đã chốt.

## Bước 2: Đọc tài liệu cốt lõi của dự án
1. Đọc lướt qua (view_file) `TECHNICAL.md` để nắm bao quát nhất kiến trúc nếu cần.
2. Đọc (view_file) vài dòng đầu của `CHANGELOG.md` để nắm số phiên bản hiện tại (version).

## Bước 3: Xác nhận
Gửi lại cho developer một tóm tắt **rất ngắn gọn** báo cáo rằng bạn đã nạp đủ bối cảnh, và đưa ra gợi ý/đề xuất bắt đầu ngay vào item trên cùng của file `action_items.md`.
