---
layout: default
title: Unity Addressables
---

# Unity Addressables

> Tài liệu áp dụng cho **Addressables 2.x** trên Unity 2022.3+. Một số mục (Play Asset Delivery built-in) yêu cầu Unity 6+ và được ghi chú riêng. API có thể khác đôi chút với phiên bản 1.x.

## Mục lục

1. [Addressable Asset Introduction](#1-addressable-asset-introduction)
2. [Creating and Organizing](#2-creating-and-organizing)
3. [Dependencies](#3-dependencies)
4. [Loading Addressable Assets](#4-loading-addressable-assets)
5. [Memory Management](#5-memory-management)
6. [Tools](#6-tools)
7. [Build & Content Update](#7-build--content-update)
8. [Migration](#8-migration-từ-resources--assetbundle-sang-addressables)
9. [Troubleshooting](#9-troubleshooting)
10. [References](#10-references)

## 1. Addressable Asset Introduction

### Addressables là gì?

Addressables là hệ thống quản lý asset của Unity cho phép load asset thông qua **address** (một chuỗi key) thay vì kéo thả reference trực tiếp vào script hay scene. Unity tự động quản lý dependency, bundle packing và memory, giúp developer tập trung vào logic thay vì quản lý file thủ công.

```csharp
// Cách cũ: direct reference
public GameObject enemyPrefab;

// Cách mới: load bằng address
var handle = Addressables.LoadAssetAsync<GameObject>("Enemy/Zombie");
```

### Giải quyết vấn đề gì?

| Hệ thống | Ưu điểm | Nhược điểm |
|----------|---------|------------|
| `Resources` | Load nhanh, đơn giản | Mọi asset bị đóng gói vào build, không cập nhật được sau release, tăng startup time |
| `AssetBundle` | Linh hoạt, hỗ trợ remote | Phải tự quản lý dependency, path, version, CRC → dễ sai |
| `Addressables` | Kế thừa ưu điểm cả hai: load theo key, Unity tự lo dependency, hỗ trợ cả local và remote, dễ content update | Cấu hình ban đầu phức tạp hơn |

### Các khái niệm cốt lõi

- **Address**: chuỗi key định danh asset khi load runtime (ví dụ `UI/Popup/Shop`).
- **Label**: tag cross-cutting gắn cho asset. Một asset có nhiều label; một label ứng với nhiều asset. Dùng để query/load theo nhóm.
- **Group**: container build-time. Mỗi group được build thành một hoặc nhiều bundle.
- **Bundle**: file output được build ra từ group. Là đơn vị I/O thực tế khi runtime.
- **AssetReference**: kiểu field thay thế direct reference, trỏ tới addressable asset qua GUID.

### Workflow cơ bản

1. **Mark** asset là Addressable (checkbox trong Inspector hoặc kéo vào Addressables Groups window).
2. **Gán address** và phân loại vào **group** phù hợp.
3. **Build** Addressables content (`Window → Asset Management → Addressables → Groups → Build`).
4. **Load** runtime bằng `Addressables.LoadAssetAsync<T>(address)`.
5. **Release** handle khi không còn dùng để giải phóng RAM.

> 📷 **[CẦN HÌNH]**: Flow diagram minh họa 5 bước Mark → Address → Build → Load → Release.

### Khi nào nên dùng?

- Game nhiều asset, cần tối ưu memory.
- Cần content update sau khi ship (live-ops, DLC, seasonal event).
- Cần tải asset từ server (CDN, Cloud Content Delivery, Play Asset Delivery).
- Muốn build size nhỏ, tải bổ sung sau.

Game nhỏ, ít asset, không cần remote update thì direct reference đơn giản và đủ tốt.

### Initialization

Addressables **tự khởi tạo** trong lần gọi API đầu tiên — hầu hết trường hợp không cần làm gì. Chỉ cần gọi thủ công khi muốn kiểm soát thời điểm init hoặc chờ xong trước khi load asset đầu tiên:

```csharp
await Addressables.InitializeAsync().Task;
```

### Query nhiều asset với MergeMode

Khi load nhiều key/label cùng lúc qua `LoadAssetsAsync`, chỉ định cách gộp kết quả:

| MergeMode | Ý nghĩa |
|-----------|---------|
| `UseFirst` | Chỉ dùng key đầu tiên |
| `Union` | Hợp — asset thỏa **bất kỳ** key nào |
| `Intersection` | Giao — asset thỏa **tất cả** key |

```csharp
// Load tất cả asset có CẢ label "enemy" VÀ "boss"
Addressables.LoadAssetsAsync<GameObject>(
    new List<string> { "enemy", "boss" },
    null,
    Addressables.MergeMode.Intersection);
```

---

## 2. Creating and Organizing

### Hierarchy cốt lõi

```
Group (build-time container)
  └── Bundle (output file)
        └── Asset (đơn vị có address)
              └── SubAsset (Sprite trong Atlas, mesh trong FBX, ...)

Label: tag song song, không nằm trong hierarchy
```

> 📷 **[CẦN HÌNH]**: Diagram thể hiện quan hệ Group ↔ Bundle ↔ Asset, và Label như một lớp tag ngang cắt qua nhiều asset ở các group khác nhau.

### Pack Mode (Bundle Mode)

Quy định cách Unity đóng gói asset trong group thành bundle:

| Pack Mode | Hành vi | Khi nào dùng |
|-----------|---------|--------------|
| **Pack Together** | Toàn bộ asset của group → 1 bundle | Mặc định, phù hợp nhất cho đa số trường hợp |
| **Pack Separately** | Mỗi entry → 1 bundle riêng. Entry là primary asset hoặc folder (xem chi tiết bên dưới) | Khi cần kiểm soát chi tiết từng asset hoặc folder, load độc lập |
| **Pack Together By Label** | Gộp asset theo label → mỗi label 1 bundle | Khi 1 group chứa nhiều nhóm logic phân biệt bằng label |

**Pack Separately và folder**: khi mark **folder** là Addressable, folder đó được coi là 1 entry — tất cả asset trong folder gộp thành **1 bundle**. Asset trong folder được mark riêng lẻ sẽ tách ra bundle riêng.

| Cách đánh dấu | Pack Separately → kết quả |
|---------------|--------------------------|
| Mark **folder** | Tất cả asset trong folder → 1 bundle |
| Mark **từng asset** | Mỗi asset → 1 bundle riêng |
| Mark **folder** + mark riêng vài asset trong đó | Folder 1 bundle, asset mark riêng tách ra bundle riêng |

**Lưu ý**: Scene asset luôn được đóng gói tách biệt với asset thường, bất kể Pack Mode.

> 📷 **[CẦN HÌNH]**: So sánh trực quan 3 Pack Mode — cùng 1 group 5 asset, output bundle khác nhau thế nào với Pack Together / Pack Separately / Pack Together By Label.

### 4 pattern thiết kế group

#### 1. Concurrent usage (theo tình huống load)
Gom asset **thường được load cùng lúc** vào 1 group. Tối ưu peak memory vì asset cùng load/release theo cụm.

> Ví dụ: toàn bộ environment, model, effect của Level 1 → group `Level_01`.

#### 2. Logical entity (theo thực thể)
Gom asset thuộc **cùng một thực thể logic**.

> Ví dụ: `Character_Hero` chứa prefab + animation + material + texture của nhân vật.

#### 3. Type (theo loại asset)
Gom asset **cùng loại** (audio, shader, texture) vào 1 group.

Phù hợp cho asset **dùng chung nhiều nơi** như shared UI atlas, common shaders. **Không nên gom tất cả asset cùng type vào 1 group** — ví dụ gom mọi texture của cả game vào group `All_Textures` sẽ tạo bundle khổng lồ, phá vỡ locality (load cả bundle dù chỉ cần vài texture cho 1 level) và dễ gây duplicate dependency khi các asset khác group reference vào.

#### 4. Shared group (anti-duplicate)
Tạo một group riêng chứa asset dùng chung giữa nhiều feature/group khác, mark Addressable để tránh Unity copy ngầm vào nhiều bundle. Thường kết hợp với Type grouping — ví dụ `Shared_Shaders` vừa gom theo type vừa đóng vai trò shared group.

> Ví dụ: `Shared_UI` chứa font, icon, shared atlas. `Shared_Shaders` chứa shader dùng xuyên suốt.

Cách phát hiện asset cần đưa vào Shared: chạy **Addressables → Analyze → Check Duplicate Bundle Dependencies**.

### Asset Bundle Compression

| Compression | Build Size | Load Time | Memory Footprint | Use Case |
|-------------|-----------|-----------|------------------|----------|
| **Uncompressed** | Lớn nhất | Nhanh nhất | Thấp | Khi dung lượng không quan trọng, ưu tiên tốc độ |
| **LZ4** | Trung bình | Nhanh, chunk-based 128KB | Thấp — chỉ metadata + chunk cần thiết | **Mặc định cho local bundle** |
| **LZMA** | Nhỏ nhất | Chậm, giải nén toàn bộ vào RAM | Cao — toàn bộ bundle trong RAM khi load | Remote download — tiết kiệm băng thông |

#### LZ4 chunk-based loading (chi tiết)

LZ4 chia bundle thành **chunk 128KB**, mỗi chunk nén độc lập. Khi load 1 asset, Unity chỉ giải nén các chunk chứa data của asset đó — **không giải nén toàn bộ bundle**.

Với `LoadFromFile` (cách Addressables dùng mặc định), bundle **không được copy vào RAM** — Unity đọc trực tiếp từ file trên disk. Trong RAM chỉ có:

| Thành phần | Mô tả |
|------------|-------|
| Metadata | TypeTrees, Table of Contents, Preload Table — luôn load |
| Block cache | Cache nhỏ chứa chunk đã đọc gần đây (`m_CachedBlocks`) |
| Asset objects | Chỉ asset được request mới deserialize thành Unity object (Texture2D, Mesh...) |

→ Bundle 100MB trên disk **không chiếm 100MB RAM**. RAM thực tế = metadata + asset objects được load + block cache.

Ngược lại, **LZMA** phải giải nén toàn bộ bundle vào RAM trước khi đọc được bất kỳ asset nào.

### Group Inspector Settings quan trọng

#### Include in Catalog (3 tùy chọn)

| Option | Công dụng | Tắt được khi |
|--------|-----------|--------------|
| **Include Addresses in Catalog** | Cho phép load bằng address string | Code chỉ dùng `AssetReference` (không dùng string) |
| **Include GUIDs in Catalog** | Cho phép load qua `AssetReference` | Code chỉ load bằng address string |
| **Include Labels in Catalog** | Cho phép `LoadAssetsAsync(label)` | Không query theo label runtime |

Bật càng nhiều → catalog càng lớn → startup chậm hơn. Chỉ bật những option thực sự dùng.

#### Internal Asset Naming Mode

- **Full Path**: dễ debug nhưng **catalog lớn** và **thay đổi khi di chuyển file** (ảnh hưởng content update).
- **Filename**: ngắn hơn Full Path, nhưng vẫn thay đổi nếu đổi tên file.
- **GUID**: ổn định nhất — không đổi khi di chuyển hay đổi tên file. Khó đọc khi debug.
- **Dynamic**: tự động chọn tên nội bộ ngắn nhất dựa trên các asset trong group. Giảm catalog size mà vẫn đảm bảo tính duy nhất.

#### Asset Load Mode

- **Requested Asset And Dependencies** (mặc định): chỉ load asset được yêu cầu + dependency của nó.
- **All Packed Assets And Dependencies**: khi request bất kỳ asset nào, Unity load **tất cả asset objects trong bundle** (không chỉ asset được request). Peak memory cao, chỉ dùng khi chắc chắn sẽ dùng gần hết asset trong bundle.

#### Content Update Restriction — Prevent Updates

- `Prevent Updates = false` (Can Change Post Release): khi có asset thay đổi, toàn bộ bundle của group được build lại trong content update.
- `Prevent Updates = true` (Cannot Change Post Release): bundle gốc được giữ nguyên. Khi chạy **Check for Content Update Restrictions**, tool sẽ di chuyển asset đã thay đổi sang một group mới riêng biệt để build thành bundle update — bundle cũ không bị ảnh hưởng. Dùng cho asset ổn định, hạn chế user phải tải lại bundle lớn.

### Naming convention khuyến nghị

- **Address**: phân cấp bằng `/` — `UI/Popup/Shop`, `Character/Hero/Warrior`.
- **Group name**: prefix theo delivery + feature — `InstallTime_Core`, `FastFollow_Event_Halloween`, `Shared_UI`.
- **Label**: lowercase, ngắn gọn — `enemy`, `boss`, `tutorial`.

---

## 3. Dependencies

![Addressable Dependencies](https://app.milanote.com/media/p/images/1WcAu21Y2c7WbJ/Tm8/1WcAu21Y2c7WbJ-akz8m.png)

Tham khảo: [Addressable asset dependencies — Unity Manual](https://docs.unity3d.com/Packages/com.unity.addressables@2.10/manual/AssetDependencies.html)

### Explicit vs Implicit dependencies

- **Explicit asset**: asset được **trực tiếp** mark Addressable và thêm vào group.
- **Implicit asset**: asset **không được mark Addressable** nhưng được reference bởi một explicit asset. Unity tự động kéo vào bundle chứa explicit asset đó.

### Vấn đề duplicate dependency

Khi một asset **không phải Addressable** được reference bởi **nhiều explicit asset ở các group khác nhau**, Unity **copy asset đó vào bundle của mỗi group** → nhân đôi dung lượng và memory.

**Ví dụ**: `shared_texture.png` (không Addressable) được dùng bởi `material_A` (group A) và `material_B` (group B) → texture bị copy vào cả 2 bundle.

> 📷 **[CẦN HÌNH]**: Diagram 2 trường hợp — (1) texture non-Addressable bị nhân đôi vào 2 bundle, (2) texture mark Addressable đặt vào Shared group chỉ tồn tại 1 bản.

**Giải pháp**:
1. Mark `shared_texture.png` là Addressable và đặt vào group `Shared`.
2. Hoặc đưa `material_A` và `material_B` về chung 1 group.
3. Chạy **Addressables Analyze → Check Duplicate Bundle Dependencies → Fix Selected Rules**.

### Lưu ý quan trọng

> **Load 1 asset sẽ mở AssetBundle chứa nó**: metadata (TypeTrees, Table of Contents, Preload Table) luôn load vào RAM. Với LZ4, asset data chỉ giải nén chunk cần thiết từ disk. Với LZMA, toàn bộ bundle giải nén vào RAM. Dù compression nào, **không thể unload từng asset riêng** — bundle chỉ unload khi tất cả asset trong nó đều được release. Đây là lý do cần cân nhắc kỹ bundle size và cách nhóm asset.

### SubAsset

SubAsset là asset con bên trong một container asset: Sprite trong SpriteAtlas, Mesh/Animation trong FBX, entry trong ScriptableObject composite.

#### Cách tham chiếu SubAsset

**1. Direct SubAsset Reference** — reference trực tiếp tới sub-asset.
- Hệ quả: load **toàn bộ asset cha** vào memory dù chỉ cần 1 sub-asset. Ví dụ: reference 1 Sprite trong SpriteAtlas 50 sprite → load cả atlas.

**2. Intermediary Asset Reference** — tạo một ScriptableObject trung gian cho mỗi sub-asset (hoặc nhóm sub-asset), đặt vào bundle riêng. SO chỉ chứa AssetReference trỏ tới sub-asset.
- Khi cần: load SO nhẹ trước → chỉ load asset cha nặng khi thực sự dùng đến sub-asset đó.
- Phù hợp khi container asset lớn (FBX nhiều mesh, atlas nhiều sprite) và chỉ cần một phần nhỏ tại mỗi thời điểm.

---

## 4. Loading Addressable Assets

### Hành vi async khi load

Khi load asset **lần đầu**, handle hoàn thành **tối thiểu sau 1 frame**. Nếu asset đã cache sẵn trong RAM, thời gian thực thi khác nhau tùy phương thức:

| Phương thức | Asset mới | Asset đã cache |
|-------------|-----------|----------------|
| **Coroutine** (`yield return handle`) | Delay ≥ 1 frame | Vẫn delay ≥ 1 frame |
| **Completed callback** | Trigger sau ≥ 1 frame | Trigger **ngay trong frame hiện tại** |
| **async/await** | Resume sau ≥ 1 frame | Resume **ngay trong frame hiện tại** |

→ Khi cần tối ưu đường load-từ-cache, ưu tiên `Completed callback` hoặc `async/await` thay vì coroutine.

### WaitForCompletion — cẩn trọng khi dùng

`handle.WaitForCompletion()` cho phép chờ đồng bộ một async operation. Nếu bundle đã có sẵn local hoặc trong cache, chi phí nhỏ. Tuy nhiên có **các rủi ro nghiêm trọng**:

#### ⚠️ Hiệu ứng dây chuyền
Khi gọi `WaitForCompletion` trên một operation, Unity **bị ép hoàn thành đồng bộ toàn bộ operation khác đang chạy ngầm** trên main thread.

> Nếu đang load ngầm 10 audio files và bạn gọi `WaitForCompletion` cho 1 texture, main thread sẽ **block cho đến khi cả 10 audio load xong**.

Chỉ dùng khi **nắm rõ số operation đang chạy** và thực sự muốn flush hết chúng đồng bộ.

#### ❌ Tránh gọi WaitForCompletion trong các trường hợp

- Trên operation đang tải AssetBundle từ **remote** (network) → block main thread suốt thời gian download, dễ gây ANR.
- Trong **Awake()** khi scene chưa integrate xong → deadlock.
- Trên **WebGL**: không hỗ trợ. WebGL single-threaded, tight loop block web request vĩnh viễn → app treo.

### Bẫy deadlock thường gặp

#### 1. Load Scene không thể hoàn toàn đồng bộ

Scene activation **bắt buộc async**. Gọi `WaitForCompletion` trên `LoadSceneAsync` chỉ chờ load xong dependency, scene chưa được activate.

```csharp
IEnumerator LoadScene(string sceneKey)
{
    // activateOnLoad: false để tách biệt hai giai đoạn load và activate
    var sceneHandle = Addressables.LoadSceneAsync(
        sceneKey, LoadSceneMode.Additive, activateOnLoad: false);

    // Chờ load đồng bộ dependency và asset
    var sceneInstance = sceneHandle.WaitForCompletion();

    // Scene activation BẮT BUỘC async
    yield return sceneInstance.ActivateAsync();

    // Lúc này scene mới thực sự tích hợp xong
}
```

Tương tự, **unload scene đồng bộ không được hỗ trợ** — cố tình gọi sẽ nhận warning.

#### 2. Deadlock khi load 2 scene liên tiếp

- Bước 1: Scene A load xong (mode `Single`) → Unity tự gọi `UnloadUnusedAssets` trên main thread.
- Bước 2: Ngay sau đó, code gọi `WaitForCompletion` để load Scene B → lock main thread chờ Scene B load xong.
- **Deadlock**: `UnloadUnusedAssets` (bước 1) cần main thread để hoàn thành, nhưng main thread đang bị `WaitForCompletion` (bước 2) chiếm giữ. Cả hai chờ nhau vô hạn.

**Giải pháp**: load scene liên tiếp bằng async pattern, hoặc chờ `UnloadUnusedAssets` xong rồi mới load scene tiếp.

#### 3. Deadlock trong Awake

Tránh gọi `WaitForCompletion` trong `Awake()` — scene chưa integrate xong, chặn các async operation khác (như unload AssetBundle).

**Giải pháp**: chuyển logic load đồng bộ sang `Start()`.

### Release — bắt buộc giải phóng

Addressables dùng **reference counting** để quản lý lifetime asset. **Mỗi asset load hoặc instantiate đều phải có Release tương ứng**, nếu không sẽ leak memory.

```csharp
var handle = Addressables.LoadAssetAsync<GameObject>("Enemy");
await handle.Task;
// ... dùng asset ...
Addressables.Release(handle); // bắt buộc
```

#### Ngoại lệ: InstantiateAsync với trackHandle

```csharp
// trackHandle = true (mặc định)
var handle = Addressables.InstantiateAsync("Enemy");
```

Khi `trackHandle = true`, handle được **tự động release khi scene chứa object đó bị unload**. Để release chủ động trong cùng scene, dùng `Addressables.ReleaseInstance(gameObject)` — vừa release handle vừa destroy GameObject.

Scene load qua `Addressables.LoadSceneAsync` cũng tự động release khi scene được unload qua `Addressables.UnloadSceneAsync` hoặc `SceneManager.UnloadSceneAsync` — không cần gọi `Release` thủ công cho scene handle.

Ngoài 2 trường hợp này, mọi `LoadAssetAsync` / `LoadAssetsAsync` / `DownloadDependenciesAsync` đều **phải tự gọi Release**.

#### ⚠️ DontDestroyOnLoad KHÔNG ngăn được Addressables unload

Các pattern giữ asset kiểu cũ **hoàn toàn vô tác dụng** với Addressables:

- `Object.DontDestroyOnLoad` chỉ giữ GameObject trên Hierarchy — **không tăng reference count** của AssetBundle.
- `HideFlags.DontUnloadUnusedAsset` không có tác dụng.

**Hệ quả**: khi scene cũ unload, Addressables thấy bundle reference count = 0 → unload bundle. GameObject được `DontDestroyOnLoad` sẽ mất material (thành màu tím) hoặc mất mesh (tàng hình).

**Giải pháp đúng**: dùng `Addressables.ResourceManager.Acquire(handle)` để tăng reference count thủ công (ngăn bundle bị unload), hoặc giữ reference đến handle dưới dạng field cho đến khi chủ động release.

### Xử lý lỗi khi load

Load có thể fail (mạng mất, asset bị xóa, catalog cũ). Luôn kiểm tra `Status` trước khi dùng kết quả:

```csharp
var handle = Addressables.LoadAssetAsync<GameObject>(key);
await handle.Task;

if (handle.Status != AsyncOperationStatus.Succeeded)
{
    Debug.LogError($"Load failed: {handle.OperationException}");
    Addressables.Release(handle);
    return;
}

var prefab = handle.Result;
```

### Ví dụ end-to-end: Load → Use → Release

Pattern hoàn chỉnh cho 1 component:

```csharp
using UnityEngine;
using UnityEngine.AddressableAssets;
using UnityEngine.ResourceManagement.AsyncOperations;
using System.Threading.Tasks;

public class EnemySpawner : MonoBehaviour
{
    [SerializeField] private string enemyAddress = "Enemy/Zombie";

    private AsyncOperationHandle<GameObject> _handle;
    private GameObject _spawned;

    private async void Start()
    {
        _handle = Addressables.LoadAssetAsync<GameObject>(enemyAddress);
        await _handle.Task;

        if (_handle.Status != AsyncOperationStatus.Succeeded)
        {
            Debug.LogError($"Load {enemyAddress} failed: {_handle.OperationException}");
            return;
        }

        _spawned = Instantiate(_handle.Result, transform);
    }

    private void OnDestroy()
    {
        if (_spawned != null) Destroy(_spawned);

        if (_handle.IsValid())
            Addressables.Release(_handle);
    }
}
```

### Ví dụ dùng AssetReference

Cách khuyến nghị khi có sẵn reference từ Inspector:

```csharp
public class WeaponLoader : MonoBehaviour
{
    [SerializeField] private AssetReferenceGameObject weaponRef;

    private GameObject _instance;

    public async void Equip()
    {
        var op = weaponRef.InstantiateAsync(transform);
        _instance = await op.Task;
    }

    public void Unequip()
    {
        if (_instance != null)
        {
            // ReleaseInstance = release handle + destroy GameObject trong 1 lệnh.
            // Không dùng Destroy(_instance) trực tiếp — handle chỉ tự release khi scene unload,
            // không phải khi gọi Destroy thủ công.
            Addressables.ReleaseInstance(_instance);
            _instance = null;
        }
    }
}
```

### Ví dụ pre-download remote content

Hiển thị size và tải trước asset remote:

```csharp
public async Task<bool> PreloadFeature(string label)
{
    // 1. Check dung lượng cần tải
    var sizeHandle = Addressables.GetDownloadSizeAsync(label);
    long bytes = await sizeHandle.Task;
    Addressables.Release(sizeHandle);

    if (bytes == 0) return true; // đã có trong cache

    Debug.Log($"Need to download {bytes / 1024f / 1024f:F2} MB");

    // 2. Download với progress
    var downloadHandle = Addressables.DownloadDependenciesAsync(label);
    while (!downloadHandle.IsDone)
    {
        float percent = downloadHandle.PercentComplete * 100f;
        Debug.Log($"Downloading... {percent:F1}%");
        await Task.Yield();
    }

    bool success = downloadHandle.Status == AsyncOperationStatus.Succeeded;
    Addressables.Release(downloadHandle);
    return success;
}
```

> 📷 **[CẦN HÌNH]**: Sequence diagram lifecycle Load → Use → Release, đánh dấu thời điểm Reference Count tăng/giảm và khi nào Bundle thực sự unload.

### Pattern tiện ích thường gặp

#### 1. Batch load song song nhiều asset với `Task.WhenAll`

Nhanh hơn load tuần tự nhiều lần vì các handle chạy song song:

```csharp
public static async Task<(List<T> assets, List<AsyncOperationHandle<T>> handles)>
    LoadAssetsAsync<T>(IList<string> addresses) where T : Object
{
    var handles = new List<AsyncOperationHandle<T>>(addresses.Count);
    var tasks = new List<Task<T>>(addresses.Count);

    foreach (var addr in addresses)
    {
        var h = Addressables.LoadAssetAsync<T>(addr);
        handles.Add(h);
        tasks.Add(h.Task);
    }

    var results = await Task.WhenAll(tasks);

    // Trả cả handles để caller có thể Release từng cái khi xong.
    return (new List<T>(results), handles);
}
```

> ⚠️ Caller **phải** Release từng handle trong danh sách trả về khi không còn dùng asset.

#### 2. Tránh load lại `AssetReference` đã load

`AssetReference` tự giữ 1 handle bên trong. Check `IsValid()` để dùng lại thay vì tạo handle mới (tránh tăng ref count thừa):

```csharp
public static Task<T> LoadAsync<T>(AssetReference reference) where T : Object
{
    if (reference.IsValid())
        return reference.OperationHandle.Convert<T>().Task;

    return reference.LoadAssetAsync<T>().Task;
}
```

Release bằng `reference.ReleaseAsset()` khi không còn dùng.

#### 3. Load prefab và lấy trực tiếp Component

Tiện khi chỉ quan tâm component cụ thể, không cần giữ GameObject trung gian:

```csharp
public static async Task<(T component, AsyncOperationHandle<GameObject> handle)>
    LoadPrefabComponentAsync<T>(string address) where T : Component
{
    var handle = Addressables.LoadAssetAsync<GameObject>(address);
    var go = await handle.Task;

    if (handle.Status != AsyncOperationStatus.Succeeded || go == null)
        return (null, handle); // caller vẫn cần Release handle dù fail

    if (!go.TryGetComponent<T>(out var component))
        Debug.LogError($"Prefab '{address}' không có component {typeof(T).Name}");

    // Trả handle cùng component để caller có thể Release khi xong.
    return (component, handle);
}
```

### Anti-patterns cần tránh

- ❌ Load asset trong `Update()` — spam handle, không release.
- ❌ Giữ `AsyncOperationHandle` làm field mà quên Release khi object destroy → leak.
- ❌ Gọi `Release` nhiều lần trên cùng handle → crash hoặc reference count âm.
- ❌ Mix `Resources.Load` và `Addressables.LoadAsset` cho cùng một asset → duplicate trong build.
- ❌ Direct reference prefab + AssetReference cùng asset → nhân đôi dung lượng.
- ❌ Load cùng `AssetReference` 2 lần mà không check `IsValid()` → tăng ref count thừa, dễ quên Release đủ lần.

---

## 5. Memory Management

### Reference counting

- Mỗi **asset** và mỗi **AssetBundle** có reference count riêng.
- Load tăng count, Release giảm count.
- Asset có `Reference Count = 0` **chưa unload ngay** — asset vẫn ở trong RAM cho đến khi AssetBundle chứa nó cũng có ref count = 0 và được unload, lúc đó tất cả asset trong bundle mới thực sự giải phóng.
- `Resources.UnloadUnusedAssets()` force giải phóng ngay nhưng **gây lag frame** — dùng tại điểm chuyển cảnh, không dùng runtime.

> 📷 **[CẦN HÌNH]**: Diagram reference counting qua 3 giai đoạn — (1) Load 2 lần → count=2, (2) Release 1 lần → count=1, asset vẫn trong RAM, (3) Release lần cuối → count=0, bundle unload.

### Bundle loaded vs Asset deserialized

Cần phân biệt hai lớp memory:

| Lớp | Khi nào xảy ra | Chiếm bao nhiêu RAM |
|-----|----------------|---------------------|
| **Bundle opened** | Khi load bất kỳ asset nào trong bundle | Metadata (TypeTrees, TOC, Preload Table) + block cache. Với LZ4 + `LoadFromFile`: **không copy toàn bộ file vào RAM** |
| **Asset deserialized** | Khi asset cụ thể được request | Unity object thực (Texture2D, Mesh, Material...) chiếm RAM tương ứng |

→ Load 1 asset từ bundle 100MB (LZ4) **không chiếm 100MB RAM**. Nhưng bundle vẫn "mở" và metadata vẫn trong RAM cho đến khi tất cả asset được release.

### Các nguyên tắc

- **Tránh Asset Churn**: không load/release bundle liên tục trong thời gian ngắn — chi phí I/O và GC cao.
- **Cân bằng số lượng bundle và số asset trong mỗi bundle**: mỗi bundle có chi phí metadata cố định (`TypeTrees`, `Table of Contents`, `Preload Table`, `Loading Cache`).
- **Cẩn trọng với bundle dependencies**: dependency tính ở **bundle level**, không phải asset level. Nếu 1 asset trong bundle A tham chiếu 1 asset trong bundle B, **toàn bộ bundle B trở thành dependency của bundle A**. Ví dụ: bundle A chứa `PrefabX` (không reference gì ngoài bundle) và `PrefabY` (reference texture trong bundle B). Load `PrefabX` — dù bản thân nó không cần bundle B — **vẫn trigger load bundle B** vì dependency tính ở bundle level.
- **Nguyên tắc vàng**: giữ dependency graph giữa các bundle **càng phẳng càng tốt**.

### Nhược điểm của chia quá nhiều bundle NHỎ

- **Memory overhead**: mỗi bundle có metadata cố định. Load hàng trăm bundle tí hon → tổng overhead lớn hơn data thực.
- **Concurrency limit**: Unity giới hạn số bundle load song song. Nhiều bundle nhỏ → queue dài, tải tuần tự.
- **Catalog bloat**: nhiều bundle → nhiều entry trong catalog → catalog phình, startup chậm.
- **Compression kém hiệu quả**: bundle nhỏ ít data để tìm pattern lặp → tỷ lệ nén thấp.
- **Nguy cơ duplicate cao**: asset chung giữa nhiều bundle dễ bị copy ngầm nếu không quản lý bằng Shared group.

> Ví dụ duplicate: 2 material Addressable cùng dùng 1 texture không Addressable → texture bị copy vào cả 2 bundle. Fix bằng cách mark texture Addressable và đặt vào Shared group.

### Nhược điểm của bundle KHỔNG LỒ

- **Không hỗ trợ resume khi lỗi mạng** (remote bundle): download fail giữa chừng phải tải lại từ đầu.
- **Không thể unload từng phần**: dù chỉ cần 1 asset, bundle metadata và dependency vẫn giữ trong RAM. Với LZ4 không phải toàn bộ file, nhưng metadata của bundle lớn (TypeTrees, Preload Table) cũng đáng kể.
- **Content update đắt đỏ**: sửa 1 asset nhỏ → user phải tải lại toàn bộ bundle lớn.
- **Metadata overhead lớn**: Preload Table phải track dependency của tất cả asset trong bundle — bundle càng lớn, table càng nặng.

### Kích thước bundle khuyến nghị

- **Local bundle**: 1–10 MB là lý tưởng cho hầu hết use case.
- **Remote bundle**: 1–5 MB để tối ưu download resume và parallel.
- **Play Asset Delivery (Android)**: có thể lớn hơn vì OS xử lý mmap, không load hết vào RAM.

---

## 6. Tools

### Addressables Groups Window
`Window → Asset Management → Addressables → Groups`

Quản lý group, asset, address, label. Trigger build.

### Addressables Analyze
`Window → Asset Management → Addressables → Analyze`

Các rule quan trọng:
- **Check Duplicate Bundle Dependencies**: phát hiện asset bị copy vào nhiều bundle. **Có auto-fix** — nút **Fix Selected Rules** tự tạo group chứa shared asset.
- **Check Resources to Addressable Duplicate Dependencies**: detect asset vừa ở `Resources/` vừa Addressable. Không có auto-fix — phải tự di chuyển asset ra khỏi `Resources/`.
- **Check Scene to Addressable Duplicate Dependencies**: detect asset ở built-in scene vừa Addressable. Không có auto-fix — cân nhắc chuyển scene sang Addressable.
- **Bundle Layout Preview**: xem trước cấu trúc bundle sẽ được build (chỉ hiển thị, không fix).

> **Lưu ý**: Analyze không phải lúc nào cũng đưa ra fix đúng. Với SubAsset (Sprite Atlas, FBX), Analyze có thể không phát hiện load toàn bộ. Một số duplicate là **cố ý** (để tránh load thêm bundle nhỏ) — cần review thủ công.

### Addressables Profiler
`Window → Asset Management → Addressables → Profiler` (Unity 2022.2+)

Theo dõi realtime:
- Bundle nào đang load/loaded.
- Reference count của từng asset/bundle.
- Memory footprint.
- Timing của async operation.

**Không hoạt động ở Play Mode Script = Use Asset Database (Fastest)** — phải dùng Simulate Groups hoặc Use Existing Build.

### Play Mode Scripts

| Script | Đặc điểm | Use case |
|--------|----------|----------|
| **Use Asset Database (Fastest)** | Load trực tiếp từ asset database, không qua bundle | Iteration nhanh khi dev |
| **Simulate Groups (Advanced)** | Mô phỏng bundle mà không build thật | Test gần giống production |
| **Use Existing Build** | Dùng bundle đã build | Test production thật |

### Build Layout Report
Bật `Preferences → Addressables → Debug Build Layout = true`. Sau khi build, mở `Library/com.unity.addressables/buildlayout.txt` để xem:
- Bundle nào chứa asset gì.
- Tỷ lệ nén thực tế.
- Asset nào bị duplicate.

### Event Viewer
`Window → Asset Management → Addressables → Event Viewer`

Debug lifecycle của operation: load, release, acquire, dependency resolution.

---

## 7. Build & Content Update

### Player Build vs Content Build

- **Player Build**: build APK/IPA/Exe từ Unity. Bao gồm Local Addressables content.
- **Content Build** (`Addressables → Build → New Build → Default Build Script`): build bundle và catalog. Chạy **trước** khi build Player.

### Content Update Workflow

Khi cần cập nhật asset sau release **không** qua app store:

1. Build ban đầu tạo ra `addressables_content_state.bin` — **commit file này vào version control**.
2. Sửa asset cần update.
3. Chạy **Addressables → Tools → Check for Content Update Restrictions**. Tool đọc `addressables_content_state.bin`, phát hiện asset thay đổi trong group có `Prevent Updates = true` và **di chuyển chúng sang group mới** — giữ bundle gốc nguyên vẹn.
4. Chạy `Addressables → Build → Update a Previous Build`. Chỉ build bundle mới cho asset thay đổi + catalog mới.
5. Upload bundle + catalog mới lên CDN. Client fetch catalog mới (qua hash file kiểm tra version) → tải bundle thay đổi.

> 📷 **[CẦN HÌNH]**: Timeline workflow content update — từ Build v1 → Release → Sửa asset → Update Build → Upload CDN → Client fetch catalog mới.

### Remote vs Local Groups

- **Local**: bundle đóng gói cùng Player build, luôn sẵn có.
- **Remote**: bundle upload lên CDN, client tải về cache khi cần.

Setup remote:
- `Build & Load Paths` → Remote profile.
- Bật `Build Remote Catalog` ở `AddressableAssetSettings`.
- Cấu hình `RemoteLoadPath` trỏ về URL CDN.

> 📷 **[CẦN HÌNH]**: Sơ đồ Local vs Remote — Player bundle đi kèm APK/IPA, Remote bundle host trên CDN, client cache sau lần tải đầu.

### Platform-specific delivery

- **Android — Play Asset Delivery (PAD)**: xem chi tiết ở mục dưới.
- **iOS — On-Demand Resources (ODR)**: cơ chế riêng của Apple, workflow khác PAD, không thảo luận trong tài liệu này.

### Play Asset Delivery (Android)

PAD là cơ chế phân phối asset của Google Play cho Android App Bundle (AAB), cho phép tách asset ra khỏi base APK và để Google Play host/serve asset pack. Với Addressables, dev không phải setup CDN riêng.

#### 3 loại Asset Pack (Delivery Mode)

| Delivery Mode | Khi nào tải | Đặc điểm | Use case |
| ------------- | ----------- | -------- | -------- |
| **Install Time** | Cùng lúc với APK | Tính vào app size trên Play Store, user không xóa được | Asset bắt buộc có ngay khi mở app |
| **Fast Follow** | Tự động ngay sau khi install xong (không cần mở app) | Không tính vào app size, user có thể xóa | Asset cần sớm nhưng chấp nhận delay vài chục giây |
| **On Demand** | Khi app chủ động request runtime | Không tính vào app size, user có thể xóa | Feature mở khóa dần, event theo mùa |

#### Giới hạn kích thước (theo Google Play Console, 2026)

| Hạng mục | Giới hạn |
| -------- | -------- |
| Base module | 200 MB |
| Mỗi feature module | 200 MB |
| Mỗi asset pack (bất kể delivery mode) | **1.5 GB** |
| Tổng base + feature + install-time packs | **4 GB** |
| Tổng on-demand + fast-follow packs (standard) | **4 GB** |
| Tổng on-demand + fast-follow packs (Level Up / Android XR) | **30 GB** |
| Tổng compressed download của app | 8 GB (34 GB với Level Up / XR) |

> Số liệu cập nhật theo [Google Play size limits](https://support.google.com/googleplay/android-developer/answer/9859372). Kiểm tra lại khi build vì Google có thể thay đổi.

#### Setup PAD với Addressables

Có **hai hướng tích hợp**, chọn một:

##### Hướng 1 — Dùng Unity built-in (Unity 6+)

Unity 6 tích hợp sẵn PAD qua `UnityEngine.Android.AndroidAssetPacks`. Build AAB từ Unity với Player Settings → Publishing Settings → Split Application Binary, nhóm Addressables group vào asset pack tương ứng.

##### Hướng 2 — Dùng Google Play Plugin for Unity

Cài plugin từ [google/play-unity-plugins](https://github.com/google/play-unity-plugins/releases), sau đó:

1. Thêm **Play Asset Delivery schema** vào Addressables group (Inspector → Add Schema).
2. Chọn **Delivery Mode** cho group: `Do Not Package` / `Install Time` / `Fast Follow` / `On Demand`.
3. Build Addressables content trước (`Build → New Build`).
4. Generate asset pack config: **Google → Android App Bundle → Create config for Addressables Groups**.
5. Build AAB.

Với cả hai hướng, group vẫn nên đặt:

- **Asset Bundle Compression** → `LZ4`
- **Build & Load Paths** → `Local`

#### Request On Demand pack runtime (Unity built-in)

```csharp
using UnityEngine;
using UnityEngine.Android;

public static class AssetPackLoader
{
    public static async Awaitable<bool> EnsurePackDownloaded(string packName)
    {
        var stateOp = AndroidAssetPacks.GetAssetPackStateAsync(new[] { packName });
        while (!stateOp.isDone) await Awaitable.NextFrameAsync();

        if (stateOp.states[0].status == AndroidAssetPackStatus.Completed)
            return true;

        var downloadOp = AndroidAssetPacks.DownloadAssetPackAsync(new[] { packName });
        while (!downloadOp.isDone) await Awaitable.NextFrameAsync();

        return downloadOp.state.status == AndroidAssetPackStatus.Completed;
    }
}
```

Sau khi pack đã ở trạng thái `Completed`, load asset bằng Addressables như bình thường.

#### Request On Demand pack runtime (Google plugin)

```csharp
using Google.Play.AssetDelivery;

public async Task<bool> RetrievePack(string packName)
{
    var retrieval = PlayAssetDelivery.RetrieveAssetPackAsync(packName);
    await retrieval;

    return retrieval.Error == AssetDeliveryErrorCode.NoError;
}
```

#### Khác biệt quan trọng so với Addressables thường

- **Không cần CDN / remote server** — Google Play host và distribute.
- **Không thay thế content update workflow**: bản cập nhật asset vẫn phải submit qua Play Console.
- **Asset pack lớn không đồng nghĩa tốn RAM**: OS mmap, chỉ phần asset đang dùng mới chiếm RAM.
- **Chỉ có trên Android AAB**: iOS cần giải pháp khác (ODR hoặc bundle thường).
- **Install-time pack cần nhiều storage tạm**: Android yêu cầu **gấp đôi dung lượng** install-time packs lúc install/update.

#### Test PAD

- **Internal testing track** trên Google Play Console — cách gần với production nhất.
- **bundletool** local: build `.aab` từ Unity, chạy `bundletool build-apks --local-testing` rồi `install-apks` để test Fast Follow / On Demand trên device thật.
- **Không chạy được trên Editor Play Mode** — phải build và deploy lên device/emulator.

### API hữu ích cho remote content

```csharp
// Check dung lượng cần tải
var sizeHandle = Addressables.GetDownloadSizeAsync(key);
long sizeInBytes = await sizeHandle.Task;
Addressables.Release(sizeHandle);

// Pre-download dependency
var downloadHandle = Addressables.DownloadDependenciesAsync(key);
await downloadHandle.Task;
Addressables.Release(downloadHandle);

// Clear cache — ClearDependencyCacheAsync tự release handle (autoReleaseHandle = true mặc định)
await Addressables.ClearDependencyCacheAsync(key).Task;
```

#### Check catalog updates runtime

Gọi trước khi chơi game để lấy catalog/bundle mới nhất mà không cần update app:

```csharp
public static async Task<bool> CheckAndUpdateCatalogs()
{
    var checkHandle = Addressables.CheckForCatalogUpdates(autoReleaseHandle: false);
    List<string> catalogsToUpdate = await checkHandle.Task;
    Addressables.Release(checkHandle);

    if (catalogsToUpdate == null || catalogsToUpdate.Count == 0)
        return false; // không có update

    var updateHandle = Addressables.UpdateCatalogs(catalogsToUpdate, autoReleaseHandle: false);
    await updateHandle.Task;
    Addressables.Release(updateHandle);
    return true;
}
```

Sau khi catalog update xong, `GetDownloadSizeAsync` sẽ trả về dung lượng bundle mới cần tải.

---

## 8. Migration: Từ Resources / AssetBundle sang Addressables

### Các bước migrate

1. **Install package** `com.unity.addressables` qua Package Manager.
2. **Tạo Default Local Group** — group khởi đầu.
3. **Mark asset là Addressable theo từng module** — đừng mark cả project cùng lúc.
4. **Thay thế API load**:

**Trước**:

```csharp
var prefab = Resources.Load<GameObject>("Enemy");
Instantiate(prefab);
```

**Sau**:

```csharp
var handle = Addressables.LoadAssetAsync<GameObject>("Enemy");
var prefab = await handle.Task;
Instantiate(prefab);
// ... khi không cần nữa
Addressables.Release(handle);
```

5. **Thay direct reference bằng AssetReference** nơi cần:

**Trước**:

```csharp
public GameObject enemyPrefab;
```

**Sau**:

```csharp
public AssetReferenceGameObject enemyRef;
```

6. **Build và test** với từng Play Mode Script khác nhau.
7. **Chạy Analyze** trước khi ship production.

### Checklist trước khi ship

- [ ] Chạy **Check Duplicate Bundle Dependencies**, fix duplicate.
- [ ] Xem **Build Layout Report**, xác nhận bundle size hợp lý.
- [ ] Test với **Use Existing Build** Play Mode Script.
- [ ] Kiểm tra mọi `LoadAssetAsync` đều có Release tương ứng.
- [ ] `addressables_content_state.bin` đã commit vào repo.
- [ ] Test flow content update nếu có remote.
- [ ] Profile memory bằng Addressables Profiler trên device thật.

---

## 9. Troubleshooting

Các lỗi phổ biến và cách xử lý nhanh:

| Triệu chứng | Nguyên nhân thường gặp | Cách xử lý |
| ----------- | ---------------------- | ---------- |
| `InvalidKeyException` khi load | Address sai, hoặc chưa build Addressables | Check address trong Groups window, build lại content |
| Build OK trên Editor nhưng load fail trên device | Chưa build Addressables trước khi build Player | Chạy `Addressables → Build → New Build` trước build Player |
| Asset hiển thị sai màu tím / tàng hình sau đổi scene | `DontDestroyOnLoad` bị mất AssetBundle | Giữ reference handle, hoặc dùng `ResourceManager.Acquire` |
| Memory tăng liên tục mỗi lần load | Quên gọi `Release` | Audit mọi `LoadAssetAsync` có Release tương ứng |
| Load rất chậm lần đầu | Bundle quá lớn, compression LZMA local | Đổi sang LZ4 cho local bundle |
| `ChainOperation failed` | Dependency bundle thiếu hoặc mismatch version | Build lại toàn bộ content, clear cache |
| App treo khi load scene | `WaitForCompletion` trong `Awake()` hoặc load 2 scene liên tiếp | Dùng async, hoặc chuyển logic sang `Start()` |
| Download fail silent | Remote URL sai, CORS, hoặc CRC mismatch | Check profile path, check log network |
| Bundle duplicate sau update | Addressables content state cũ | Commit `addressables_content_state.bin`, build update từ state đúng |
| App treo trên WebGL khi load | Dùng `WaitForCompletion` trên WebGL (không hỗ trợ) | Chuyển sang async pattern, không dùng sync loading trên WebGL |

---

## 10. References

- [Unity Addressables Manual](https://docs.unity3d.com/Packages/com.unity.addressables@2.10/manual/index.html)
- [Addressables Samples](https://github.com/Unity-Technologies/Addressables-Sample)
- [Unity Forum — Addressables](https://forum.unity.com/forums/addressables.169/)
- [Memory management — Unity Docs](https://docs.unity3d.com/Packages/com.unity.addressables@2.10/manual/MemoryManagement.html)
- [Asset dependencies — Unity Docs](https://docs.unity3d.com/Packages/com.unity.addressables@2.10/manual/AssetDependencies.html)
