# **Kiến trúc dự án Game Unity**

## **1. Tổng quan và Triết lý Thiết kế**

Kiến trúc này được xây dựng theo mô hình phân lớp (Layered Architecture), áp dụng các nguyên tắc từ **MVP (Model-View-Presenter)** để đạt được sự tách biệt rõ ràng giữa các thành phần.

*   **Mục tiêu chính:** Tách biệt **Logic Game (Presenter)** khỏi **Logic Dữ liệu (Data Handler)**, và tách biệt cả hai khỏi **Hiển thị (View)**.
*   **Triết lý:** Mỗi lớp chỉ có một trách nhiệm duy nhất và chỉ giao tiếp với các lớp liền kề nó.

### **Sơ đồ Luồng Phụ thuộc**

Luồng phụ thuộc đi theo một chiều, từ lớp ngoài vào lớp trong. Lớp bên trong không biết sự tồn tại của lớp bên ngoài.

```
+----------------+      +--------------+      +-----------------+      +---------------+
|      View      |----->|  Presenter   |----->|  Data Handler   |----->|  Data Model   |
| (Hiển thị UI)  |      | (Logic Game) |      | (Logic Dữ liệu) |      | (Dữ liệu gốc) |
+----------------+      +--------------+      +-----------------+      +---------------+
```

## **2. Chi tiết các Lớp (Layers)**

### **Lớp Toàn cục: Services (Dịch vụ)**

*   **Vai trò:** Cung cấp các chức năng hạ tầng, nền tảng, có thể được truy cập từ bất kỳ đâu (thường thông qua Dependency Injection hoặc Service Locator).
*   **Các dịch vụ tiêu biểu:**
    *   `DataConfigCollection`: Là cơ sở dữ liệu trung tâm, chỉ đọc (read-only) cho toàn bộ dữ liệu **cấu hình tĩnh** của game.
    *   `PersistenceService`: Chịu trách nhiệm **duy nhất** việc đọc và ghi dữ liệu xuống file, PlayerPrefs hoặc server.
    *   `AssetProviderService` (**AssetsCollection**): Đóng vai trò thư viện trung tâm, cung cấp `assets` (Prefab, Icon, Audio...) dựa trên một `ID` định danh.
    *   Các dịch vụ bên thứ ba: `APIService`, `AnalyticsService`, `IAPService`, `AdsService`.

---

### **Lớp 1: Data Models (Dữ liệu Gốc)**

*   **Vai trò:** Tương ứng với lớp **"Data Saved"**. Đây là lớp chỉ chứa dữ liệu trạng thái của game, không chứa bất kỳ logic xử lý nào.
*   **Đặc điểm:**
    *   Là các lớp C# thuần túy (POCO - Plain Old C# Object).
    *   Hoàn toàn độc lập với Unity Engine.
    *   Ví dụ: `PlayerData.cs` chứa `Level`, `Gold`, `Experience`; `SettingsData.cs` chứa `MusicVolume`, `SfxVolume`.

---

### **Lớp 2: Data Handlers (Logic Xử lý Dữ liệu)**

*   **Vai trò:** Tương ứng với lớp **"Data Handler"**. Đây là lớp trung tâm quản lý và thực thi logic nghiệp vụ liên quan đến dữ liệu.
*   **Đặc điểm:**
    *   Quản lý một hoặc nhiều đối tượng `Data Model`. Ví dụ: `PlayerDataHandler` sẽ chứa và quản lý đối tượng `PlayerData`.
    *   Cung cấp các phương thức công khai (public methods) để thay đổi dữ liệu một cách an toàn. Ví dụ: `playerDataHandler.AddGold(amount)`, `playerDataHandler.CompleteQuest(questId)`.
    *   Giao tiếp với `PersistenceService` để yêu cầu lưu hoặc tải dữ liệu khi cần.
    *   Phát ra các sự kiện (events) khi dữ liệu thay đổi (ví dụ: `OnGoldChanged`, `OnLevelUp`) để các lớp khác (chủ yếu là `Presenter`) lắng nghe.

---

### **Lớp 3: Presenter (Logic Game & Điều phối)**

*   **Vai trò:** Là bộ não xử lý logic của các tính năng và điều phối hoạt động giữa `View` và `Data Handler`.
*   **Đặc điểm:**
    *   Lắng nghe sự kiện từ `View` (ví dụ: người dùng nhấn nút).
    *   Gọi các phương thức trên `Data Handler` để yêu cầu thay đổi trạng thái game. **Presenter không trực tiếp sửa đổi dữ liệu.**
    *   Lắng nghe sự kiện từ `Data Handler` để biết khi nào dữ liệu đã thay đổi.
    *   Sau khi nhận được thông báo, `Presenter` sẽ ra lệnh cho `View` tương ứng để cập nhật lại giao diện.
*   **Phân loại Presenter:**
    *   **UI Presenters:** Quản lý các màn hình giao diện. Ví dụ: `ShopPresenter`, `MainMenuPresenter`, `HUDPresenter`.
    *   **Game World Presenters:** Quản lý các thực thể trong thế giới game. Ví dụ điển hình nhất chính là **`PlayerController`** hoặc `EnemyAIController`. Chúng nhận `input` (từ người chơi hoặc AI), xử lý logic hành động, và ra lệnh cho `View` (như `PlayerView`) để thực hiện `animation` hoặc hiệu ứng.

---

### **Lớp 4: View (Hiển thị)**

*   **Vai trò:** Là lớp "thụ động" (dumb), chỉ chịu trách nhiệm hiển thị và chuyển tiếp tương tác của người dùng.
*   **Đặc điểm:**
    *   Chứa tham chiếu đến các thành phần UI (`Text`, `Button`, `Image`) hoặc các thành phần trong game (`Animator`, `ParticleSystem`).
    *   Hiển thị thông tin do `Presenter` cung cấp.
    *   Phát ra sự kiện khi người dùng tương tác (ví dụ: `OnClick`, `OnSliderValueChanged`).
    *   Không chứa bất kỳ logic game hay logic dữ liệu nào.

## **3. Luồng hoạt động mẫu: Người chơi mua một vật phẩm**

1.  **View:** Người chơi nhấn nút "Mua" trên `ShopView`. `ShopView` phát ra sự kiện `OnBuyItemClicked("item_id")`.
2.  **Presenter:** `ShopPresenter` nhận được sự kiện này.
3.  **Presenter → Data Handler:** `ShopPresenter` gọi phương thức `playerDataHandler.AttemptToPurchaseItem("item_id")`.
4.  **Data Handler:** `PlayerDataHandler` thực thi logic:
    * a. Lấy thông tin giá của vật phẩm từ cấu hình game (`Game Data Config`).
    * b. Kiểm tra xem `playerData.Gold` có đủ không.
    * c. Nếu đủ, nó sẽ thay đổi các `Data Model`: trừ `Gold`, thêm vật phẩm vào `Inventory`.
    * d. Phát ra các sự kiện: `OnGoldChanged(newGoldAmount)` và `OnInventoryUpdated(newItem)`.
    * e. Yêu cầu `PersistenceService.Save(playerData)`.
5.  **Presenter (Lắng nghe):** `ShopPresenter` và `HUDPresenter` đang lắng nghe các sự kiện này.
6.  **Presenter → View:**
    * a. `HUDPresenter` nhận sự kiện `OnGoldChanged` và gọi `hudView.UpdateGoldText(newGoldAmount)`.
    * b. `ShopPresenter` nhận sự kiện và gọi `shopView.SetItemAsPurchased("item_id")`.

