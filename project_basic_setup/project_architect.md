# Kiến Trúc Dự Án Game Unity

## 1. Tại sao cần kiến trúc?

Trong quá trình phát triển game, khi dự án lớn dần, các vấn đề sau thường xuất hiện:

- **Game Designer** muốn chỉnh sửa một giá trị (ví dụ: máu của nhân vật), nhưng phải nhờ **Developer** sửa code → mất thời gian, tạo bottleneck.
- Khi thêm tính năng mới, việc sửa đổi ở một chỗ gây **lỗi lan tỏa** sang các phần khác vì code bị kết dính (tightly coupled).
- Nhiều thành viên làm việc trên cùng một file lớn → **xung đột (merge conflict)** liên tục.
- Logic game, dữ liệu, và giao diện trộn lẫn trong một script → **khó debug, khó test, khó mở rộng**.

Kiến trúc phân lớp giải quyết tất cả các vấn đề trên bằng cách **tách biệt trách nhiệm** giữa các thành phần.

### Mục lục

- [2. Tổng Quan Kiến Trúc](#2-tổng-quan-kiến-trúc)
- [3. Chi Tiết Các Lớp](#3-chi-tiết-các-lớp): [Services](#lớp-toàn-cục-dịch-vụ-services) · [Data Model](#lớp-1-dữ-liệu-gốc-data-model) · [Data Handler](#lớp-2-logic-xử-lý-dữ-liệu-data-handler) · [Presenter](#lớp-3-logic-game--điều-phối-presenter) · [View](#lớp-4-hiển-thị-view)
- [4. Luồng Hoạt Động Mẫu](#4-luồng-hoạt-động-mẫu-mua-vật-phẩm)
- [5. Tóm Tắt Nguyên Tắc](#5-tóm-tắt-nguyên-tắc)

---

## 2. Tổng Quan Kiến Trúc

Kiến trúc được xây dựng theo mô hình **phân lớp (Layered Architecture)**, áp dụng các nguyên tắc từ **MVP (Model-View-Presenter)**.

**Nguyên tắc cốt lõi:**

- **Tách biệt trách nhiệm**: Logic Game (Presenter) tách khỏi Logic Dữ liệu (Data Handler), và cả hai tách khỏi Hiển thị (View).
- **Phụ thuộc một chiều**: Mỗi lớp chỉ giao tiếp với lớp liền kề, lớp bên trong không biết sự tồn tại của lớp bên ngoài.
- **Giao tiếp qua sự kiện**: Khi dữ liệu thay đổi, Data Handler phát ra event → Presenter lắng nghe → ra lệnh cho View cập nhật.

### Sơ đồ Luồng Phụ thuộc

<!-- 📸 TODO: Thay ASCII diagram bên dưới bằng hình ảnh thực (draw.io hoặc Figma).
     Gợi ý: Sơ đồ 4 lớp (View → Presenter → Data Handler → Data Model) với màu sắc phân biệt,
     mũi tên Dependency Flow đi từ trái sang phải, mũi tên Events đi ngược lại.
     Thêm Services layer ở trên/dưới với đường nét đứt. -->

```
                    Luồng phụ thuộc (Dependency Flow)
                    ─────────────────────────────────►

  ┌─────────────┐     ┌─────────────┐     ┌───────────────┐     ┌─────────────┐
  │    VIEW     │────►│  PRESENTER  │────►│ DATA HANDLER  │────►│ DATA MODEL  │
  │ (Hiển thị)  │     │(Logic Game) │     │(Logic Dữ liệu)│     │(Dữ liệu gốc)│
  └─────────────┘     └─────────────┘     └───────────────┘     └─────────────┘
        ▲                    ▲                    │                     │
        │                    │                    │                     │
        └────── Events ──────┘                    │                     │
                                                  ▼                     │
                                         ┌───────────────┐             │
                                         │  PERSISTENCE  │◄────────────┘
                                         │   SERVICE     │
                                         └───────────────┘
```

---

## 3. Chi Tiết Các Lớp

**Tổng quan nhanh — mapping giữa kiến trúc và RCore:**

| Lớp | Vai trò | Class trong RCore | Loại |
|---|---|---|---|
| **Data Model** | Dữ liệu thuần túy | `JObjectData` | POCO (`[Serializable]`) |
| **Data Handler** | Logic nghiệp vụ | `JObjectModel<T>` | `ScriptableObject` |
| **Presenter** | Điều phối logic game | Tự tạo | `MonoBehaviour` |
| **View** | Hiển thị UI/Game world | Tự tạo | `MonoBehaviour` |
| **Services** | Hạ tầng dùng chung | `ConfigCollection`, `DBManager`... | Singleton/SO |

### Lớp Toàn cục: Dịch vụ (Services)

**Vai trò:** Cung cấp các chức năng hạ tầng, có thể được truy cập từ bất kỳ đâu (thông qua Singleton, Dependency Injection hoặc Service Locator).

| Dịch vụ | Vai trò | Ghi chú |
|---|---|---|
| `DataConfigCollection` | Cơ sở dữ liệu trung tâm, **chỉ đọc** cho dữ liệu cấu hình tĩnh | Xuất từ SheetX |
| `PersistenceService` | Đọc/ghi dữ liệu xuống file, PlayerPrefs hoặc server | Single Responsibility |
| `AssetProviderService` | Thư viện trung tâm cung cấp assets (Prefab, Icon, Audio...) theo ID | AssetsCollection |
| `APIService`, `AdsService`... | Các dịch vụ bên thứ ba | Analytics, IAP, Firebase... |

> **Lưu ý:** Services là lớp đặc biệt – không thuộc luồng phụ thuộc chính nhưng có thể được truy cập bởi Data Handler và Presenter khi cần.

---

### Lớp 1: Dữ liệu Gốc (Data Model)

**Vai trò:** Chỉ chứa dữ liệu trạng thái, **không chứa logic xử lý**.

**Đặc điểm:**
- Là các lớp C# thuần túy (**POCO** - Plain Old C# Object).
- Hoàn toàn **độc lập với Unity Engine** – có thể dùng trong unit test.
- Đánh dấu `[Serializable]` để hỗ trợ JSON serialization.

```csharp
// ✅ Đúng: Data Model chỉ chứa dữ liệu
[Serializable]
public class PlayerData : JObjectData
{
    public string userName;
    public int coin;
    public int level;
    public int experience;
}

// ❌ Sai: Không đặt logic trong Data Model
[Serializable]
public class PlayerData
{
    public int coin;
    public void AddCoin(int amount) { coin += amount; } // Không nên!
}
```

---

### Lớp 2: Logic Xử lý Dữ liệu (Data Handler)

**Vai trò:** Quản lý và thực thi **logic nghiệp vụ** liên quan đến dữ liệu. Đây là "người gác cổng" đảm bảo dữ liệu luôn hợp lệ.

**Đặc điểm:**
- Quản lý một hoặc nhiều đối tượng Data Model.
- Cung cấp các **phương thức công khai** để thay đổi dữ liệu một cách **an toàn và có kiểm tra**.
- Giao tiếp với `PersistenceService` để lưu/tải dữ liệu.
- **Phát ra sự kiện** (events) khi dữ liệu thay đổi để các lớp khác lắng nghe.

```csharp
public class PlayerModel : JObjectModel<PlayerData>
{
    // Phương thức an toàn để thay đổi dữ liệu
    public bool TrySpendCoin(int amount)
    {
        if (Data.coin < amount) return false;

        Data.coin -= amount;
        OnCoinChanged?.Invoke(Data.coin);   // Phát sự kiện
        MarkDirty();                         // Đánh dấu cần lưu
        return true;
    }

    public void AddCoin(int amount)
    {
        Data.coin += amount;
        OnCoinChanged?.Invoke(Data.coin);
        MarkDirty();
    }

    // Events
    public event Action<int> OnCoinChanged;
    public event Action<int> OnLevelUp;
}
```

---

### Lớp 3: Logic Game & Điều phối (Presenter)

**Vai trò:** Là bộ não xử lý logic tính năng, **điều phối** giữa View và Data Handler.

**Đặc điểm:**
- Lắng nghe sự kiện từ View (ví dụ: người dùng nhấn nút).
- Gọi phương thức trên Data Handler để thay đổi trạng thái. **Presenter không trực tiếp sửa đổi dữ liệu.**
- Lắng nghe sự kiện từ Data Handler → ra lệnh cho View cập nhật giao diện.

**Phân loại Presenter:**

| Loại | Ví dụ | Vai trò |
|---|---|---|
| **UI Presenters** | `ShopPresenter`, `HUDPresenter` | Quản lý màn hình giao diện |
| **Game World Presenters** | `PlayerController`, `EnemyAI` | Quản lý thực thể trong game world |

```csharp
public class ShopPresenter : MonoBehaviour
{
    [SerializeField] private ShopView shopView;
    private PlayerModel playerModel;
    private DataConfigCollection config;

    private void OnEnable()
    {
        // Lắng nghe View
        shopView.OnBuyClicked += HandleBuyClicked;
        // Lắng nghe Data Handler
        playerModel.OnCoinChanged += HandleCoinChanged;
    }

    private void OnDisable()
    {
        shopView.OnBuyClicked -= HandleBuyClicked;
        playerModel.OnCoinChanged -= HandleCoinChanged;
    }

    private void HandleBuyClicked(string itemId)
    {
        var item = config.GetShopItem(itemId);
        if (playerModel.TrySpendCoin(item.price))
            shopView.SetItemAsPurchased(itemId);
        else
            shopView.ShowNotEnoughGold();
    }

    private void HandleCoinChanged(int newAmount)
    {
        shopView.UpdateCoinDisplay(newAmount);
    }
}
```

> **Lưu ý:** Presenter biết cả View và Data Handler, nhưng View và Data Handler **không biết** nhau. Đây là nguyên tắc then chốt.

---

### Lớp 4: Hiển thị (View)

**Vai trò:** Là lớp **"thụ động" (dumb)**, chỉ hiển thị và chuyển tiếp tương tác.

**Đặc điểm:**
- Chứa tham chiếu đến UI components (`Text`, `Button`, `Image`) hoặc game components (`Animator`).
- Hiển thị thông tin do Presenter cung cấp.
- Phát ra sự kiện khi người dùng tương tác (`OnClick`, `OnValueChanged`).
- **Không chứa bất kỳ logic game hay logic dữ liệu nào.**

```csharp
// ✅ Đúng: View chỉ hiển thị và chuyển tiếp sự kiện
public class ShopView : MonoBehaviour
{
    [SerializeField] private Button buyButton;
    [SerializeField] private TMP_Text priceText;
    [SerializeField] private TMP_Text coinText;
    [SerializeField] private string itemId;

    public event Action<string> OnBuyClicked;

    public void SetPrice(string price) => priceText.text = price;
    public void SetItemAsPurchased(string id) => buyButton.interactable = false;
    public void UpdateCoinDisplay(int amount) => coinText.text = amount.ToString();
    public void ShowNotEnoughGold() { /* Hiển thị popup/animation */ }

    private void OnEnable()
    {
        buyButton.onClick.AddListener(() => OnBuyClicked?.Invoke(itemId));
    }

    private void OnDisable()
    {
        buyButton.onClick.RemoveAllListeners();
    }
}
```

> **Anti-pattern cần tránh:**
> ```csharp
> // ❌ Sai: View chứa logic game
> public class ShopView : MonoBehaviour
> {
>     public void OnBuyButtonClicked()
>     {
>         if (PlayerData.coin >= item.price)  // View truy cập data trực tiếp!
>         {
>             PlayerData.coin -= item.price;   // View sửa data trực tiếp!
>             UpdateUI();
>         }
>     }
> }
> ```

---

## 4. Luồng Hoạt Động Mẫu: Mua Vật Phẩm

<!-- 📸 TODO: Thay ASCII sequence diagram bên dưới bằng hình ảnh thực.
     Gợi ý: Sequence diagram với 4 cột (ShopView, ShopPresenter, PlayerModel, PlayerData),
     7 bước đánh số ①-⑦, mũi tên có label. Dùng draw.io hoặc Mermaid render. -->

Ví dụ sau minh họa cách các lớp phối hợp khi người chơi mua một vật phẩm trong Shop:

```
  ┌──────────┐      ┌───────────────┐      ┌────────────────┐      ┌────────────┐
  │ ShopView │      │ShopPresenter  │      │ PlayerModel    │      │ PlayerData │
  └────┬─────┘      └──────┬────────┘      └──────┬─────────┘      └─────┬──────┘
       │ ① Click "Mua"    │                       │                      │
       │──────────────────►│                       │                      │
       │                   │ ② AttemptPurchase()   │                      │
       │                   │──────────────────────►│                      │
       │                   │                       │ ③ Kiểm tra Gold     │
       │                   │                       │─────────────────────►│
       │                   │                       │ ④ Trừ Gold, thêm Item│
       │                   │                       │─────────────────────►│
       │                   │                       │                      │
       │                   │ ⑤ Event: OnGoldChanged│                      │
       │                   │◄──────────────────────│                      │
       │ ⑥ UpdateGoldText()│                       │                      │
       │◄──────────────────│                       │                      │
       │ ⑦ SetPurchased()  │                       │                      │
       │◄──────────────────│                       │                      │
```

**Chi tiết từng bước:**

1. **View → Presenter:** Người chơi nhấn nút "Mua" trên `ShopView`. View phát ra sự kiện `OnBuyItemClicked("item_id")`.
2. **Presenter → Data Handler:** `ShopPresenter` nhận sự kiện, gọi `playerModel.AttemptToPurchaseItem("item_id")`.
3. **Data Handler (xử lý logic):**
   - a. Lấy giá vật phẩm từ `DataConfigCollection` (dữ liệu cấu hình tĩnh).
   - b. Kiểm tra `playerData.coin` có đủ không.
   - c. Nếu đủ → trừ Gold, thêm vật phẩm vào Inventory.
   - d. Phát events: `OnGoldChanged(newAmount)`, `OnInventoryUpdated(newItem)`.
   - e. Yêu cầu `PersistenceService` lưu dữ liệu.
4. **Presenter (lắng nghe):** `ShopPresenter` và `HUDPresenter` đều đang lắng nghe events.
5. **Presenter → View:**
   - `HUDPresenter` nhận `OnGoldChanged` → gọi `hudView.UpdateGoldText(newAmount)`.
   - `ShopPresenter` nhận event → gọi `shopView.SetItemAsPurchased("item_id")`.

> **Điểm then chốt:** `ShopPresenter` không biết HUD tồn tại. `HUDPresenter` tự lắng nghe event và tự cập nhật. Đây chính là sức mạnh của kiến trúc découple thông qua events.

---

## 5. Tóm Tắt Nguyên Tắc

| Nguyên tắc | Mô tả | Vi phạm phổ biến |
|---|---|---|
| **View không chứa logic** | Chỉ hiển thị và phát sự kiện | View trực tiếp gọi `playerData.coin -= 100` |
| **Presenter không sửa data trực tiếp** | Luôn đi qua Data Handler | Presenter viết `data.level++` |
| **Data Model không chứa logic** | Chỉ là container dữ liệu | Thêm method `AddGold()` vào Data Model |
| **Giao tiếp qua Events** | Dùng events thay vì tham chiếu trực tiếp | Data Handler gọi trực tiếp `view.UpdateText()` |
| **Phụ thuộc một chiều** | View → Presenter → Handler → Model | Model import Presenter |