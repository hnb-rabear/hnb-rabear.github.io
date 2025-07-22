# RCore Framework

## Tổng Quan

RCore là một framework cho Unity được thiết kế để cung cấp nền tảng cho việc phát triển game. Framework này không đặt mục tiêu trở thành một giải pháp mạnh mẽ và toàn diện, mà tập trung vào một tập hợp các hệ thống và công cụ thiết yếu để hợp lý hóa các tác vụ phát triển thông thường.

### Các Tính Năng Cốt Lõi

*   Dynamic Module Management
*   Integrated Audio System
*   Flexible Data Management
*   Optimized UI Components
*   Service Integration (Ads, Firebase, IAP)
*   Development-assistance Editor Tools

### Mục Lục

- [RCore Framework](#rcore-framework)
  - [Tổng Quan](#tổng-quan)
    - [Các Tính Năng Cốt Lõi](#các-tính-năng-cốt-lõi)
    - [Mục Lục](#mục-lục)
  - [Cài đặt](#cài-đặt)
  - [Các Hệ Thống Cốt Lõi (Core Systems)](#các-hệ-thống-cốt-lõi-core-systems)
    - [Configuration System](#configuration-system)
    - [Audio System](#audio-system)
    - [Event System](#event-system)
    - [Module Factory System](#module-factory-system)
    - [Data Config Management](#data-config-management)
  - [Các Hệ Thống Dữ Liệu (Data Systems)](#các-hệ-thống-dữ-liệu-data-systems)
    - [JObjectDB System](#jobjectdb-system)
  - [Các Tiện Ích Chung (Common Utilities)](#các-tiện-ích-chung-common-utilities)
    - [Các Lớp Helper](#các-lớp-helper)
    - [Pool System](#pool-system)
    - [Timer System](#timer-system)
    - [Big Number System](#big-number-system)
  - [Hệ Thống UI (UI System)](#hệ-thống-ui-ui-system)
    - [Panel System](#panel-system)
    - [Các Component UI](#các-component-ui)
  - [Tích Hợp Dịch Vụ (Services Integration)](#tích-hợp-dịch-vụ-services-integration)
  - [Công Cụ Editor (Editor Tools)](#công-cụ-editor-editor-tools)

---

## Cài đặt

Để cài đặt, thêm các Git URL sau vào Unity Package Manager (UPM) bằng cách chọn "Add package from git URL...":

1.  **UniTask** (Dependency - Bắt buộc)
    ```
    https://github.com/Cysharp/UniTask.git?path=src/UniTask/Assets/Plugins/UniTask
    ```
2.  **RCore**
    ```
    https://github.com/hnb-rabear/RCore.git?path=Assets/RCore/Main
    ```

---

## Các Hệ Thống Cốt Lõi (Core Systems)

Phần này chứa các hệ thống nền tảng của framework.

### Configuration System

**Configuration.cs** — Một `ScriptableObject` singleton đóng vai trò là trung tâm quản lý các thiết lập toàn cục cho project. Nó cung cấp một giao diện mạnh mẽ và thân thiện để quản lý các cấu hình build và các dữ liệu persistent khác.

*   **Environment and Directive Management**: Tính năng cốt lõi của hệ thống này là khả năng quản lý các môi trường build (ví dụ: "Development", "Production"). Mỗi môi trường là một tập hợp các `scripting define symbols` (directives) có thể được bật hoặc tắt. Điều này cho phép lập trình viên dễ dàng kiểm soát các tính năng nào được đưa vào một bản build (ví dụ: `UNITY_IAP`, `FIREBASE_ANALYTICS`, `DEVELOPMENT`) chỉ bằng cách chuyển đổi môi trường đang hoạt động trong editor, và các `define symbols` của project sẽ được tự động cập nhật.
*   **Key-Value Data Store**: Bao gồm một dictionary có thể serialize để lưu trữ dữ liệu cấu hình đa dụng dưới dạng các cặp key-value string, có thể truy cập toàn cục.
*   **Automatic Culture Standardization**: Khi ứng dụng khởi động, hệ thống sẽ tự động thiết lập `CultureInfo` của project thành "en-US". Đây là một tính năng quan trọng giúp ngăn ngừa các lỗi liên quan đến bản địa hóa bằng cách đảm bảo định dạng ngày, giờ và số (ví dụ: sử dụng dấu chấm `.` làm dấu phân cách thập phân) nhất quán trên tất cả các thiết bị, bất kể cài đặt khu vực của người dùng.

### Audio System

Một hệ thống toàn diện và tập trung để quản lý mọi khía cạnh của âm thanh trong game.

*   **AudioManager.cs** — Một singleton toàn cục (`BaseAudioManager`) đóng vai trò là giao diện chính cho mọi hoạt động âm thanh. Nó cung cấp khả năng kiểm soát âm lượng độc lập cho master, music, và SFX, hoàn chỉnh với hiệu ứng fade-in/out. Để tối ưu hiệu năng, nó có một hệ thống quản lý SFX mạnh mẽ với cơ chế pooling và giới hạn số lượng âm thanh đồng thời để tránh nhiễu loạn. Nó cũng hỗ trợ playlist cho music và tự động lắng nghe các sự kiện UI từ `EventDispatcher` để phát các âm thanh tương ứng.
*   **AudioCollection.cs** — Một `ScriptableObject` hoạt động như một cơ sở dữ liệu trung tâm cho tất cả `AudioClips`. Nó được thiết kế để quản lý bộ nhớ linh hoạt, hỗ trợ cả tham chiếu trực tiếp đến clip và tải động thông qua hệ thống **`Addressable Assets`**. Một tính năng chính là **`script generator`** tích hợp, có thể tự động phân tích các tệp âm thanh từ các thư mục được chỉ định và tạo ra các lớp ID tĩnh (ví dụ: `SfxIDs.cs`), cho phép gọi âm thanh một cách type-safe và không có lỗi từ code.
*   **SfxSource.cs** — Một `MonoBehaviour` component linh hoạt để phát hiệu ứng âm thanh từ bất kỳ `GameObject` nào. Nó có thể hoạt động ở hai chế độ:
    1.  **Managed (Được quản lý)**: Mặc định, nó hoạt động như một trigger đơn giản, yêu cầu `AudioManager` trung tâm phát một âm thanh từ pool được quản lý của nó.
    2.  **Standalone (Độc lập)**: Nếu một `AudioSource` component được gán trực tiếp, nó sẽ kiểm soát hoàn toàn source đó, lý tưởng cho âm thanh 3D định vị.
    Nó cũng bao gồm các tính năng lặp lại (loop), thay đổi pitch ngẫu nhiên và phát một clip ngẫu nhiên từ danh sách được định sẵn.

### Event System

**EventDispatcher.cs** — Một lớp static cung cấp một hệ thống sự kiện tập trung, type-safe sử dụng mô hình publish-subscribe. Nó được thiết kế để tạo ra một kiến trúc découple, cho phép các phần khác nhau của ứng dụng giao tiếp với nhau mà không cần giữ tham chiếu trực tiếp.

*   **Core Functionality**: Các hệ thống có thể đăng ký (subscribe) các loại sự kiện cụ thể bằng `AddListener<T>()` và hủy đăng ký bằng `RemoveListener<T>()`. Các sự kiện được phát đi toàn cục bằng cách tạo một instance của một event struct (phải implement `BaseEvent` interface) và truyền nó vào phương thức `EventDispatcher.Raise()`.
*   **Debouncing**: Cung cấp phương thức `RaiseDeBounce()` giúp ngăn chặn spam sự kiện bằng cách đảm bảo một sự kiện chỉ được phát đi sau một khoảng thời gian trễ nhất định mà không có cuộc gọi mới nào cho cùng loại sự kiện. Điều này đặc biệt hữu ích để xử lý các hành động nhanh của người dùng, như nhấp chuột liên tục. Tính năng này phụ thuộc vào thư viện **UniTask**.
*   **Design**: Hệ thống được thiết kế cho hiệu suất cao và an toàn kiểu (type safety) trong môi trường đơn luồng của Unity. Nó sử dụng một dictionary để định tuyến sự kiện hiệu quả và đảm bảo việc đăng ký listener được quản lý chính xác để ngăn rò rỉ bộ nhớ.

### Module Factory System

Một hệ thống mạnh mẽ dựa trên attribute được thiết kế để tạo ra một kiến trúc découple. Nó cho phép tự động khám phá, tạo và quản lý vòng đời của các tính năng hoặc hệ thống độc lập, được gọi là "module".

*   **`IModule`** — Interface cốt lõi mà tất cả các module phải implement. Nó định nghĩa một vòng đời tiêu chuẩn với ba phương thức thiết yếu: `Initialize()`, `Tick()`, và `Shutdown()`.
*   **`ModuleAttribute`** — Một C# attribute được sử dụng để đánh dấu một lớp là một module có thể được khám phá, định nghĩa `Key` duy nhất, hành vi `AutoCreate`, và `LoadOrder`.
*   **`ModuleFactory`** — Một lớp static quét các assembly của project để tìm `[Module]` attributes và xử lý việc khởi tạo các module không phải là MonoBehaviour.
*   **`ModuleManager`** — Một singleton persistent điều phối toàn bộ vòng đời của module. Nó tự động đăng ký các module dựa trên attribute của chúng, cung cấp các phương thức để đăng ký thủ công (bắt buộc đối với các module `MonoBehaviour`), và gọi `Tick()` và `Shutdown()` trên tất cả các module đang hoạt động.

### Data Config Management

**ConfigCollection.cs** — Một lớp cơ sở trừu tượng `ScriptableObject` để chứa dữ liệu cấu hình từ các tệp văn bản bên ngoài (ví dụ: `JSON`). Nó thúc đẩy một quy trình làm việc sạch sẽ, dựa trên dữ liệu bằng cách tách cấu hình ra khỏi code. Hệ thống hỗ trợ tải từ thư mục `Resources` khi chạy hoặc trực tiếp từ `AssetDatabase` trong Editor, với một nút bấm tùy chỉnh trong Inspector để làm mới dữ liệu theo yêu cầu.

---

## Các Hệ Thống Dữ Liệu (Data Systems)

Framework cung cấp một hệ thống lưu trữ linh hoạt dựa trên JSON, được thiết kế để tách biệt rõ ràng giữa dữ liệu và logic. Nó tận dụng `ScriptableObjects` để quản lý trong editor và sử dụng `PlayerPrefs` làm backend lưu trữ, cung cấp một giải pháp mạnh mẽ để quản lý trạng thái của game.

### JObjectDB System

Hệ thống này được xây dựng trên một kiến trúc phân lớp, tách biệt engine lưu trữ khỏi các data model và logic của chúng.

*   **`JObjectDB`** — Một lớp database static hoạt động như engine lưu trữ cốt lõi. Nó quản lý các hoạt động cấp thấp của việc serialize các đối tượng dữ liệu sang JSON và ghi chúng vào `PlayerPrefs`. Nó cung cấp một giao diện toàn cục để tạo, tải, lưu, sao lưu và khôi phục các bộ sưu tập dữ liệu.
*   **`JObjectData`** — Một lớp cơ sở trừu tượng định nghĩa hợp đồng cho bất kỳ cấu trúc dữ liệu nào có thể serialize. Tất cả các lớp dữ liệu tùy chỉnh (ví dụ: `PlayerData`, `InventoryData`) phải kế thừa từ lớp này để được hệ thống quản lý.
*   **`JObjectModel`** — Một `ScriptableObject` trừu tượng đại diện cho một data model cấp cao. Nó đóng gói một instance `JObjectData` tương ứng (trạng thái thô) và chứa tất cả business logic cho dữ liệu đó. Bằng cách implement các callback vòng đời như `OnUpdate`, `OnPause`, và `OnPostLoad`, nó có thể quản lý trạng thái của mình một cách độc lập, chẳng hạn như tính toán tiến trình offline hoặc xử lý việc tạo tài nguyên.
*   **`JObjectModelCollection`** — Một `ScriptableObject` trung tâm hoạt động như một bộ tổng hợp cho toàn bộ hệ thống dữ liệu. Nó chứa một bộ sưu tập tất cả các `JObjectModel` được sử dụng trong game và chịu trách nhiệm điều phối các sự kiện vòng đời của chúng, truyền các cuộc gọi như `Load`, `Save`, và `OnUpdate` đến mọi model đã đăng ký.
*   **`JObjectDBManagerV2`** — Một `MonoBehaviour` điều phối được thiết kế để đặt trên một đối tượng persistent trong scene. Nó kết nối vòng đời ứng dụng của Unity với hệ thống dữ liệu bằng cách điều khiển `JObjectModelCollection`. Nó xử lý việc tải dữ liệu ban đầu và cung cấp các tính năng tự động lưu mạnh mẽ (khi pause, khi thoát game) với một độ trễ có thể cấu hình để gộp các hoạt động ghi và nâng cao hiệu suất.

---

## Các Tiện Ích Chung (Common Utilities)

### Các Lớp Helper
ĐANG CẬP NHẬT...

### Pool System

Một hệ thống object pooling generic và hiệu suất cao được thiết kế để giảm thiểu `garbage collection` và giảm tải `CPU` từ việc khởi tạo và hủy đối tượng thường xuyên.

*   **`CustomPool<T>`** — Một lớp generic, có thể serialize, tạo thành khối xây dựng nền tảng của hệ thống. Mỗi instance quản lý vòng đời của một loại prefab `Component` duy nhất. Nó duy trì các danh sách riêng biệt cho các đối tượng đang hoạt động và không hoạt động, cho phép tái sử dụng hiệu quả. Các tính năng chính bao gồm pre-warming (khởi tạo trước đối tượng để tránh giật lag khi chạy), callback để khởi tạo lại các đối tượng được spawn, và giới hạn tùy chọn về số lượng instance hoạt động để tự động tái sử dụng các đối tượng cũ nhất.
*   **`PoolsContainer<T>`** — Một factory cấp cao hoạt động như một "pool của các pool", cung cấp một trình quản lý tập trung cho nhiều instance `CustomPool`. Nó đơn giản hóa việc quản lý đối tượng bằng cách cung cấp một giao diện duy nhất để spawn bất kỳ prefab nào; container tự động tạo và duy trì một pool riêng cho mỗi prefab theo yêu cầu. Tính năng quan trọng nhất của nó là cơ chế release được tối ưu hóa cao, theo dõi nguồn gốc của mọi đối tượng được spawn, cho phép nó trả một đối tượng về đúng pool của nó ngay lập tức mà không cần tìm kiếm chậm chạp.

### Timer System

Một hệ thống hiệu quả và linh hoạt để quản lý logic dựa trên thời gian, các điều kiện chờ và các hành động an toàn luồng (thread-safe) mà không cần đến `coroutine` của Unity. Hệ thống được chia thành một tiện ích gọn nhẹ và một trình quản lý sự kiện mạnh mẽ, tập trung.

*   **`TimedAction.cs`** — Một lớp đơn giản, không phải `MonoBehaviour`, để xử lý các bộ đếm ngược và cooldown cơ bản. Nó phải được cập nhật thủ công từ vòng lặp `Update` của một `MonoBehaviour` và cung cấp một giải pháp gọn nhẹ cho logic thời gian độc lập.
*   **`TimerEvents.cs`** — Một `MonoBehaviour` hoạt động như một bộ xử lý trung tâm cho các loại sự kiện khác nhau. Nó được tối ưu hóa cao, tự động vô hiệu hóa chính nó khi không có sự kiện nào đang chờ xử lý để tiết kiệm tài nguyên. Nó quản lý hai loại sự kiện chính: `CountdownEvent` và `ConditionEvent`.
*   **`TimerEventsInScene.cs`** — Một singleton accessor dành riêng cho scene cho `TimerEvents`. Nó cung cấp một điểm truy cập toàn cục cho bất kỳ sự kiện thời gian nào nên được xóa khi scene thay đổi. Đây là lựa chọn tiêu chuẩn cho hầu hết các timer trong game.
*   **`TimerEventsGlobal.cs`** — Một phiên bản singleton persistent, toàn cục (`DontDestroyOnLoad`) của `TimerEvents`. Nó phục vụ hai chức năng quan trọng:
    1.  Quản lý các timer chạy dài phải tồn tại qua các lần tải scene.
    2.  Cung cấp một **hàng đợi thực thi an toàn luồng (thread-safe execution queue)**. Phương thức `Enqueue(Action)` của nó cho phép các hành động từ các luồng nền (ví dụ: callback mạng) được chuyển một cách an toàn và thực thi trên luồng chính của Unity.

### Big Number System

*   **`BigNumberD.cs`** — Đại diện cho các số lớn sử dụng kiểu `decimal` làm cơ sở để có độ chính xác cao.
*   **`BigNumberF.cs`** — Đại diện cho các số lớn sử dụng kiểu `float` làm cơ sở để có hiệu suất tốt hơn. Hỗ trợ chuyển đổi sang `notation string` (ví dụ: 1.23E+45) và `KKK format` (ví dụ: 1.23AA).
*   **`BigNumberHelper.cs`** — Một lớp `static` với các `utility methods` để định dạng và hiển thị các số lớn một cách thân thiện với người dùng.

---

## Hệ Thống UI (UI System)

### Panel System

Panel System là một framework phân cấp, dựa trên stack, được thiết kế để quản lý vòng đời, điều hướng và trình bày UI. Nó cung cấp một kiến trúc mạnh mẽ để tạo ra các luồng UI phức tạp, lồng nhau thông qua ba component chính.

*   **`PanelController`** — Lớp cơ sở cho bất kỳ view UI riêng lẻ nào (ví dụ: dialog, màn hình, hoặc menu). Mỗi controller chịu trách nhiệm về vòng đời của chính nó, bao gồm trạng thái trình bày và hoạt ảnh chuyển tiếp (`Show`/`Hide`). Vì bản thân nó cũng là một `PanelStack`, một `PanelController` có thể quản lý các sub-panel của riêng mình, cho phép tạo ra các cấu trúc UI lồng nhau sâu.
*   **`PanelStack`** — Một lớp trừu tượng implement mô hình điều hướng dựa trên stack cốt lõi. Nó quản lý một bộ sưu tập các instance `PanelController`, xử lý logic để push (thêm) và pop (xóa) các panel. Nó hỗ trợ các chiến lược trình bày khác nhau, chẳng hạn như thêm một panel `OnTop` (lên trên) của một panel khác hoặc `Replacement` (thay thế).
*   **`PanelRoot`** — Một singleton đóng vai trò là container cấp cao nhất và điểm vào cho toàn bộ hệ thống UI. Nó sử dụng một kiến trúc hướng sự kiện, lắng nghe các yêu cầu toàn cục để hiển thị các panel (`PushPanelEvent`, `RequestPanelEvent`). Điều này tách biệt UI khỏi các hệ thống game khác. Các tính năng chính bao gồm một hàng đợi panel toàn cục để hiển thị tuần tự và quản lý tự động một lớp phủ làm mờ (dimmer overlay) cho các panel modal.

### Các Component UI

Một bộ sưu tập các component UI mạnh mẽ, sẵn sàng cho sản phẩm, mở rộng hệ thống UI cơ bản của Unity với các tính năng nâng cao, hoạt ảnh và tối ưu hóa hiệu suất.

*   **`JustButton` / `SimpleTMPButton`** — Một hệ thống button nâng cao được xây dựng để thay thế Button tiêu chuẩn của Unity. `JustButton` cung cấp hiệu ứng nảy khi nhấp và hiệu ứng greyscale, trong khi `SimpleTMPButton` bổ sung hỗ trợ phong phú cho các nhãn TextMeshPro, bao gồm hoán đổi màu sắc và material.
*   **`JustToggle` / `CustomToggleGroup`** — Một hệ thống toggle mạnh mẽ để tạo ra các điều khiển lựa chọn động và tương tác. `JustToggle` cung cấp một hệ thống chuyển tiếp phong phú dựa trên tween cho kích thước, vị trí và màu sắc. `CustomToggleGroup` mở rộng điều này bằng cách quản lý một phần tử nền động có thể tạo hoạt ảnh để làm nổi bật toggle đang được chọn.
*   **Optimized Scroll Views** — Một tập hợp các component scroll view hiệu suất cao sử dụng cơ chế ảo hóa UI (tái sử dụng) để hiển thị hàng ngàn mục với tác động hiệu suất tối thiểu. Bao gồm `OptimizedScrollItem`, `OptimizedVerticalScrollView` (có hỗ trợ lưới), và `OptimizedHorizontalScrollView`.
*   **Specialized Scroll Views** — Bao gồm `HorizontalSnapScrollView` cho các menu dạng carousel nơi các mục tự động "bắt dính" vào một điểm trung tâm, và `ScrollRectEx` để xử lý đúng cách các scroll rect lồng nhau (ví dụ: cuộn dọc bên trong cuộn ngang).

---

## Tích Hợp Dịch Vụ (Services Integration)

*   **Ads System**: `AdsProvider.cs` (base), `AdmobProvider.cs`, `ApplovinProvider.cs`, `IronSourceProvider.cs`.
*   **Firebase Integration**: `RFirebase.cs` (core), `RFirebaseAnalytics.cs`, `RFirebaseAuth.cs`, `RFirebaseDatabase.cs`, `RFirebaseFirestore.cs`, `RFirebaseRemote.cs`, `RFirebaseStorage.cs`.
*   **Game Services**: `GameServices.cs` (core), `GameServices.CloudSave.cs`, `GameServices.InAppReview.cs`, `GameServices.InAppUpdate.cs`.
*   **IAP System**: `IAPManager.cs` for managing `In-App Purchases`.
*   **Notification System**: `NotificationsManager.cs`, `GameNotification.cs`, `PendingNotification.cs`.

---

## Công Cụ Editor (Editor Tools)
ĐANG CẬP NHẬT...