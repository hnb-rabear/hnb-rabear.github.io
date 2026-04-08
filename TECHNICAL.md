# TECHNICAL Documentation — hnb-rabear.github.io

## Overview
Repository này lưu trữ tài liệu kỹ thuật và các slide thuyết trình kiến trúc dành cho team IKame.

## Architecture Stack
- **Site Engine**: GitHub Pages (Jekyll rendered from Markdown).
- **Presentations**: [Reveal.js](https://revealjs.com/) tích hợp via CDN.
- **Styling**: Custom Vanilla CSS (styles.css) và dark themes cho slides.

## Key Modules & Content
- `data_management_in_unity/`: Hệ thống slide và kịch bản thuyết trình về quản lý dữ liệu trong Unity.
- `project_basic_setup/`: Hướng dẫn kiến trúc MVP, hệ thống Layered Data.
- `sheetx/`: Tài liệu về công cụ chuyển đổi Google Sheets sang C# và JSON.

## Data Management Patterns (RCore/JObjectDB)
Dự án tập trung vào các pattern cốt lõi:
1. **Separation of Concerns**: Tách biệt Data Model (POCO) và Data Handler (Logic).
2. **Single Point of Access**: Mọi thay đổi dữ liệu phải qua Data Gateway.
3. **Event-Driven**: Giao tiếp giữa các module thông qua hệ thống Event.
4. **Lifecycle Hooks**: Tự động hóa quá trình Init, Load, Update và Save.

## Directory Structure
```
/
├── data_management_in_unity/  # Slides & Transcript
├── project_basic_setup/       # Architecture Docs
├── LocalizationX/             # Localization resources
├── styles.css                 # Global site styles
├── index.md                   # Home page
└── TECHNICAL.md               # This document
```
