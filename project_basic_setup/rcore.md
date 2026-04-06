# RCore Framework

## Tổng Quan

**RCore** là một framework cho Unity tập trung vào các hệ thống và công cụ **thiết yếu** để hợp lý hóa các tác vụ phát triển game thông thường. Nó không đặt mục tiêu trở thành giải pháp toàn diện, mà cung cấp **nền tảng vững chắc** để nhanh chóng xây dựng dự án.

**Cài đặt:**
```
https://github.com/hnb-rabear/RCore.git?path=Assets/RCore/Main
```
> ⚠️ Yêu cầu cài **UniTask** trước: `https://github.com/Cysharp/UniTask.git?path=src/UniTask/Assets/Plugins/UniTask`

### Các Tính Năng Cốt Lõi

| Nhóm | Tính năng |
|---|---|
| **Core Systems** | Configuration, Audio, Event Dispatcher, Module Factory |
| **Data Management** | Config Collection (static data), JObjectDB (player data) |
| **UI** | Panel Stack System, Optimized Scroll Views, Custom Buttons/Toggles |
| **Utilities** | Object Pool, Timer, Big Number |
| **Services** | Ads, Firebase, IAP, Game Services, Notifications |
| **Editor** | Inspector Attributes, Editor Tools |

### Mục Lục

- [Cài đặt](#cài-đặt)
- [Core Systems](#core-systems): [Configuration](#configuration-system) · [Audio](#audio-system) · [Event](#event-system) · [Module Factory](#module-factory-system) · [Data Config](#data-config-management)
- [Data Systems](#data-systems): [JObjectDB](#jobjectdb-system)
- [Common Utilities](#common-utilities): [Pool](#pool-system) · [Timer](#timer-system) · [Big Number](#big-number-system)
- [UI System](#ui-system): [Panel](#panel-system) · [UI Components](#các-component-ui)
- [Services Integration](#services-integration)
- [Inspector Attributes](#inspector-attributes)

---

## Core Systems

Phần này chứa các hệ thống nền tảng — sử dụng trong **mọi dự án**.

### Configuration System

**`Configuration.cs`** — Một `ScriptableObject` singleton quản lý các thiết lập toàn cục cho project.

| Tính năng | Mô tả |
|---|---|
| **Environment Management** | Quản lý các môi trường build (Development, Production). Mỗi môi trường là tập hợp `scripting define symbols` (`UNITY_IAP`, `FIREBASE_ANALYTICS`...) có thể bật/tắt. Chuyển đổi môi trường → define symbols tự động cập nhật |
| **Key-Value Store** | Dictionary serializable để lưu trữ cấu hình đa dụng dạng key-value string, truy cập toàn cục |
| **Culture Standardization** | Tự động thiết lập `CultureInfo` thành "en-US" khi khởi động, đảm bảo định dạng số/ngày nhất quán trên mọi thiết bị |

<!-- 📸 TODO: Screenshot cửa sổ Configuration Inspector trong Unity,
     cho thấy Environment dropdown và danh sách define symbols. -->

---

### Audio System

Hệ thống toàn diện để quản lý mọi khía cạnh âm thanh trong game.

| Component | Vai trò |
|---|---|
| **`AudioManager.cs`** | Singleton giao diện chính cho âm thanh. Kiểm soát âm lượng độc lập (master/music/SFX) với fade-in/out. Có **SFX pooling** và giới hạn số lượng đồng thời. Hỗ trợ music playlist. Tự động lắng nghe `EventDispatcher` cho UI SFX |
| **`AudioCollection.cs`** | ScriptableObject database cho `AudioClips`. Hỗ trợ tham chiếu trực tiếp hoặc tải qua **Addressable Assets**. Tích hợp **script generator** tự động phân tích thư mục âm thanh → tạo `SfxIDs.cs` cho type-safe audio calls |
| **`SfxSource.cs`** | MonoBehaviour component phát SFX từ bất kỳ GameObject nào. Hai chế độ: **Managed** (trigger cho AudioManager pool) hoặc **Standalone** (kiểm soát trực tiếp AudioSource, phù hợp âm thanh 3D). Hỗ trợ loop, random pitch, random clip |

<!-- 📸 TODO: Screenshot AudioCollection Inspector trong Unity,
     cho thấy danh sách AudioClips và nút Generate SfxIDs. -->

---

### Event System

**`EventDispatcher.cs`** — Hệ thống sự kiện tập trung, type-safe, sử dụng mô hình **publish-subscribe**. Tạo kiến trúc découple cho giao tiếp giữa các hệ thống.

```csharp
// Định nghĩa event
public struct OnGoldChanged : BaseEvent
{
    public int NewAmount;
}

// Đăng ký lắng nghe
EventDispatcher.AddListener<OnGoldChanged>(OnGoldChangedHandler);

// Phát sự kiện
EventDispatcher.Raise(new OnGoldChanged { NewAmount = 500 });

// Hủy đăng ký
EventDispatcher.RemoveListener<OnGoldChanged>(OnGoldChangedHandler);
```

| Tính năng | Mô tả |
|---|---|
| **Core** | `AddListener<T>()`, `RemoveListener<T>()`, `Raise<T>()` |
| **Debouncing** | `RaiseDeBounce()` — ngăn spam event, chỉ phát sau khoảng delay không có cuộc gọi mới. Dùng UniTask |
| **Design** | Hiệu suất cao, type-safe, đơn luồng. Dictionary-based routing, quản lý listener tốt để tránh memory leak |

---

### Module Factory System

Hệ thống dựa trên **attribute** để tự động khám phá, tạo và quản lý vòng đời của các module độc lập.

| Component | Vai trò |
|---|---|
| **`IModule`** | Interface cốt lõi. Định nghĩa vòng đời: `Initialize()`, `Tick()`, `Shutdown()` |
| **`ModuleAttribute`** | C# attribute đánh dấu class là module. Định nghĩa `Key`, `AutoCreate`, `LoadOrder` |
| **`ModuleFactory`** | Static class quét assembly tìm `[Module]` attributes → khởi tạo module (non-MonoBehaviour) |
| **`ModuleManager`** | Persistent singleton điều phối vòng đời module. Hỗ trợ auto-register (via attribute) và manual-register (cho MonoBehaviour modules) |

---

### Data Config Management

**`ConfigCollection.cs`** — Lớp cơ sở `ScriptableObject` trừu tượng để chứa **dữ liệu cấu hình tĩnh** từ tệp JSON.

**Đặc điểm:**
- Tách cấu hình ra khỏi code → data-driven design.
- Hỗ trợ tải từ `Resources` (runtime) hoặc `AssetDatabase` (Editor).
- Nút bấm tùy chỉnh trong Inspector để **Load/Refresh** dữ liệu.
- Cung cấp `LoadToArray<T>()` để deserialize JSON file.

> 📖 Xem ví dụ sử dụng chi tiết trong [trang chính — mục 2.1.3](index).

---

## Data Systems

Phần này chứa hệ thống lưu trữ dữ liệu người chơi — sử dụng khi game cần **save/load** tiến trình.

### JObjectDB System

Hệ thống lưu trữ player data dựa trên JSON, thiết kế theo kiến trúc **phân lớp** tách biệt giữa storage engine, data models và business logic.

<!-- 📸 TODO: Thay ASCII diagram bên dưới bằng hình ảnh thực (draw.io).
     Gợi ý: Sơ đồ phân cấp 5 tầng: DBManagerV2 → ModelCollection → JObjectModel<T> → JObjectData → JObjectDB.
     Mỗi tầng có màu khác nhau, label loại class (đâu là MonoBehaviour, đâu là ScriptableObject, đâu là static). -->

```
┌──────────────────────────────────────────────────────────────────────┐
│                        JObjectDB Architecture                        │
│                                                                      │
│  ┌──────────────────┐                                                │
│  │ JObjectDBManagerV2│  MonoBehaviour trên DontDestroyOnLoad          │
│  │ (Lifecycle Driver)│  Auto-save on pause/quit, configurable delay  │
│  └────────┬─────────┘                                                │
│           │ drives                                                   │
│  ┌────────▼──────────┐                                               │
│  │JObjectModel       │  ScriptableObject aggregator                  │
│  │   Collection      │  Điều phối Load/Save/OnUpdate cho mọi model  │
│  └────────┬──────────┘                                               │
│           │ contains many                                            │
│  ┌────────▼──────────┐                                               │
│  │  JObjectModel<T>  │  ScriptableObject + business logic            │
│  │  (Data Handler)   │  Lifecycle: Init, OnUpdate, OnPause,         │
│  │                   │  OnPostLoad, OnPreSave, OnRemoteConfig       │
│  └────────┬──────────┘                                               │
│           │ wraps                                                    │
│  ┌────────▼──────────┐                                               │
│  │  JObjectData      │  Abstract POCO (Serializable)                 │
│  │  (Data Model)     │  PlayerData, QuestData, InventoryData...     │
│  └────────┬──────────┘                                               │
│           │ stored by                                                │
│  ┌────────▼──────────┐                                               │
│  │    JObjectDB      │  Static storage engine                        │
│  │  (Storage Engine) │  JSON ↔ PlayerPrefs, Backup/Restore          │
│  └───────────────────┘                                               │
└──────────────────────────────────────────────────────────────────────┘
```

| Component | Loại | Vai trò |
|---|---|---|
| **`JObjectDB`** | Static class | Storage engine cốt lõi. Serialize → JSON → `PlayerPrefs`. API: Create, Load, Save, Backup, Restore |
| **`JObjectData`** | Abstract class | Base cho mọi data model. POCO serializable. Ví dụ: `PlayerData`, `InventoryData` |
| **`JObjectModel<T>`** | ScriptableObject | Data Handler cấp cao. Đóng gói `JObjectData` + business logic. Lifecycle callbacks: `OnUpdate`, `OnPause`, `OnPostLoad`... |
| **`JObjectModelCollection`** | ScriptableObject | Aggregator trung tâm. Chứa mọi `JObjectModel`, điều phối `Load`, `Save`, `OnUpdate` |
| **`JObjectDBManagerV2`** | MonoBehaviour | Driver trên DontDestroyOnLoad. Kết nối Unity lifecycle → data system. Auto-save (on pause, on quit) với configurable delay |

> 📖 Xem ví dụ sử dụng đầy đủ trong [trang chính — mục 2.2](index).

---

## Common Utilities

Các tiện ích dùng chung, không phụ thuộc vào kiến trúc cụ thể.

### Helpers

> ⚠️ **Đang cập nhật...**

### Pool System

Hệ thống **object pooling** generic, hiệu suất cao, giảm thiểu garbage collection.

| Component | Vai trò |
|---|---|
| **`CustomPool<T>`** | Pool cho một loại prefab. Quản lý danh sách active/inactive. Hỗ trợ **pre-warming**, callback khởi tạo lại, và giới hạn max active (tự tái sử dụng đối tượng cũ nhất) |
| **`PoolsContainer<T>`** | "Pool của các pool" — factory cấp cao. Giao diện duy nhất để spawn bất kỳ prefab nào, tự động tạo pool riêng cho mỗi loại. Cơ chế release có **source tracking** → trả đối tượng về đúng pool ngay lập tức |

---

### Timer System

Quản lý logic dựa trên thời gian **không cần coroutine**.

| Component | Vai trò | Phạm vi |
|---|---|---|
| **`TimedAction.cs`** | Bộ đếm ngược/cooldown đơn giản, không phải MonoBehaviour. Cần gọi thủ công từ `Update()` | Độc lập |
| **`TimerEvents.cs`** | Bộ xử lý trung tâm cho `CountdownEvent` và `ConditionEvent`. Tự disable khi không có event nào đang chờ | Base class |
| **`TimerEventsInScene.cs`** | Singleton cho scene hiện tại. Timer tự xóa khi chuyển scene | Trong scene |
| **`TimerEventsGlobal.cs`** | Persistent singleton (`DontDestroyOnLoad`). Timer tồn tại qua các scene. Có **thread-safe execution queue** (`Enqueue(Action)`) cho callback từ background thread | Toàn cục |

---

### Big Number System

Hệ thống xử lý số cực lớn (Idle/Incremental games).

| Component | Mô tả |
|---|---|
| **`BigNumberD.cs`** | Dùng `decimal` làm cơ sở → độ chính xác cao |
| **`BigNumberF.cs`** | Dùng `float` làm cơ sở → hiệu suất tốt hơn. Hỗ trợ notation string (`1.23E+45`) và KKK format (`1.23AA`) |
| **`BigNumberHelper.cs`** | Static utility methods để định dạng và hiển thị số lớn thân thiện |

---

## UI System

### Panel System

Framework phân cấp, **stack-based**, quản lý vòng đời, điều hướng và trình bày UI.

| Component | Vai trò |
|---|---|
| **`PanelController`** | Lớp cơ sở cho mọi view UI (dialog, screen, menu). Quản lý vòng đời, animation `Show`/`Hide`. Bản thân cũng là `PanelStack` → hỗ trợ **sub-panel lồng nhau** |
| **`PanelStack`** | Abstract class implement navigation stack. Push/pop panels với chiến lược: **OnTop** (lên trên) hoặc **Replacement** (thay thế) |
| **`PanelRoot`** | Singleton container cấp cao nhất. Hướng sự kiện: lắng nghe `PushPanelEvent`/`RequestPanelEvent` toàn cục → tách biệt UI khỏi game logic. Hỗ trợ **panel queue** (hiển thị tuần tự) và auto **dimmer overlay** cho modal panels |

### Các Component UI

| Component | Mô tả |
|---|---|
| **`JustButton` / `SimpleTMPButton`** | Button nâng cao: hiệu ứng bounce, greyscale, hỗ trợ TextMeshPro labels (swap color/material) |
| **`JustToggle` / `CustomToggleGroup`** | Toggle với tween animation (size/position/color). Toggle group có animated background highlight |
| **Optimized Scroll Views** | **Ảo hóa UI** (virtualization) — hiển thị hàng ngàn items với hiệu suất tối thiểu. Gồm: `OptimizedVerticalScrollView` (có grid), `OptimizedHorizontalScrollView` |
| **Specialized Scroll Views** | `HorizontalSnapScrollView` (carousel snap-to-center), `ScrollRectEx` (nested scroll rect handling) |

---

## Services Integration

| Nhóm | Components |
|---|---|
| **Ads** | `AdsProvider.cs` (base), `AdmobProvider.cs`, `ApplovinProvider.cs`, `IronSourceProvider.cs` |
| **Firebase** | `RFirebase.cs` (core), Analytics, Auth, Database, Firestore, Remote Config, Storage |
| **Game Services** | `GameServices.cs` (core), Cloud Save, In-App Review, In-App Update |
| **IAP** | `IAPManager.cs` — quản lý In-App Purchases |
| **Notifications** | `NotificationsManager.cs`, `GameNotification.cs`, `PendingNotification.cs` |

---

## Inspector Attributes

Tập hợp C# attributes nâng cao Unity Inspector:

| Attribute | Mô tả |
|---|---|
| **`[AutoFill]`** | Tự động điền trường null bằng component/ScriptableObject. Hỗ trợ tìm kiếm theo path, auto-fill mảng |
| **`[Comment]`** | Hiển thị ghi chú mô tả phía trên trường |
| **`[CreateScriptableObject]`** | Nút "Create" bên cạnh trường SO trống → tạo và gán asset nhanh |
| **`[DisplayEnum]`** | Hiển thị trường int dạng dropdown enum (static hoặc dynamic) |
| **`[ExposeScriptableObject]`** | Lồng properties của SO vào Inspector đối tượng cha → edit inline |
| **`[FolderPath]`** | Trường string → nút mở dialog chọn thư mục |
| **`[Highlight]`** | Tô màu nền trường quan trọng |
| **`[InspectorButton]`** | Hiển thị method dạng nút nhấp được. Hỗ trợ method có tham số |
| **`[ReadOnly]`** | Trường chỉ đọc trong Inspector |
| **`[Separator]`** | Đường kẻ ngang (có/không tiêu đề) để nhóm trường |
| **`[ShowIf]`** | Hiển thị/ẩn trường theo điều kiện boolean |
| **`[SingleLayer]`** | Trường int → dropdown chọn Layer |
| **`[SpriteBox]`** | Trường Sprite với preview thumbnail |
| **`[TagSelector]`** | Trường string → dropdown chọn Tag |
| **`[TMPFontMaterials]`** | Dropdown material cho TextMeshPro, tự liệt kê materials của font hiện tại |

---

## Editor Tools

> ⚠️ **Đang cập nhật...**