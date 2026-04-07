# Project Context — hnb-rabear.github.io

## Mô tả
GitHub Pages site chứa tài liệu kỹ thuật và presentations cho team IKame.

## Stack
- **Static site**: GitHub Pages (hnb-rabear.github.io)
- **Presentations**: Reveal.js (CDN) — dark theme, custom CSS
- **Docs**: Markdown → rendered pages

## Cấu trúc chính
```
data_management_in_unity/   → Reveal.js slide deck (15 slides)
project_basic_setup/        → Tài liệu kiến trúc MVP, SheetX, RCore
```

## Kiến trúc Data Management (chủ đề chính)
- **Layered Architecture**: View → Presenter → Data Handler → Data Model
- **RCore Framework**: JObjectData (Model), JObjectModel<T> (Handler), JObjectModelCollection
- **SheetX Tool**: Google Sheets/Excel → JSON + C# IDs + ScriptableObject
- **JObjectDB System**: Persistence layer (JSON ↔ PlayerPrefs)

## Project tham chiếu
- **Goods-Jam** (`E:\Projects\IKame\Goods-Jam`): Game thực tế dùng kiến trúc này
  - PlayerData (partial: .cs, .Inventory.cs, .Avatar.cs, .Misc.cs)
  - PlayerModel : JObjectModel<PlayerData>
  - SaveDataCollection: 7 models (player, reward, settings, quest, competition, store, battlePass)
