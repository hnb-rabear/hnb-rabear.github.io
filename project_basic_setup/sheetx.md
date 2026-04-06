# SheetX — Công Cụ Quản Lý Data Config

## 1. Giới Thiệu

**SheetX** là công cụ chuyển đổi dữ liệu từ **Excel/Google Sheets** thành các tệp sẵn sàng sử dụng trong Unity: **C# scripts** (IDs, Constants, Localization) và **JSON data**.

**Vấn đề SheetX giải quyết:**
- Khi dự án lớn dần, việc quản lý hàng trăm ID, constants và bảng dữ liệu trở nên phức tạp.
- Game Designer cần tự chỉnh sửa dữ liệu mà không phụ thuộc vào Developer.
- Cần đảm bảo **type-safety** — tránh lỗi do đánh sai tên ID hay sai kiểu dữ liệu.

**SheetX tập trung hóa quy trình:** Designer sửa Sheets → nhấn Export → code tự cập nhật.

### Cài đặt

**Unity Package Manager (UPM):**
```
https://github.com/hnb-rabear/RCore.git?path=Assets/RCore.SheetX
```

**Hoặc phiên bản Winform:** [excel-to-unity](https://github.com/hnb-rabear/excel-to-unity)

### Tài nguyên

- 📦 [Tải Example Package](https://github.com/hnb-rabear/hnb-rabear.github.io/blob/main/sheetx/SheetXExample.unitypackage)
- 📊 [Xem trước các kiểu dữ liệu được hỗ trợ](https://docs.google.com/spreadsheets/d/1_9BqoKwRsod5cMwML5n_pLpuWk045lD3Jd7nrizqVBo/edit?usp=sharing)

### Mục lục

- [2. Các Chức Năng Chính](#2-các-chức-năng-chính)
- [3. Cài Đặt Settings](#3-cài-đặt-settings)
- [4. Xuất Excel Sheets](#4-xuất-excel-sheets)
- [5. Google Spreadsheets](#5-google-spreadsheets)
- [6. Quy Tắc Thiết Kế Bảng Tính](#6-quy-tắc-thiết-kế-bảng-tính): [IDs](#61-sheet-ids) · [Constants](#62-sheet-constants) · [Localization](#63-sheet-localization) · [JSON Data](#64-bảng-dữ-liệu-json)
- [7. Hướng Dẫn Tích Hợp](#7-hướng-dẫn-tích-hợp)

---

## 2. Các Chức Năng Chính

| Chức năng | Mô tả |
|---|---|
| **Excel & Google Sheets** | Quản lý toàn bộ database bằng Excel hoặc Google Spreadsheets |
| **ID & Constants Management** | Điều chỉnh hàng loạt ID và hằng số, xuất thành C# type-safe |
| **Localization System** | Xử lý nhiều ngôn ngữ, tích hợp liền mạch với Unity |
| **Export JSON** | Chuyển đổi bảng dữ liệu thành JSON |
| **Flexible Data Format** | Hỗ trợ nhiều kiểu: basic types, arrays, JSON objects, attributes |

---

## 3. Cài Đặt Settings

Điều hướng tới: `RCore > SheetX > Settings`

![SheetX Settings](https://github.com/user-attachments/assets/cd15d421-dfd5-4bbc-9e88-717a480c2ebe)

### Các tùy chọn cấu hình

| Tùy chọn | Mô tả |
|---|---|
| **Scripts Output Folder** | Thư mục lưu C# scripts (IDs, Constants, Localization API) |
| **JSON Output Folder** | Thư mục lưu dữ liệu JSON |
| **Localization Output** | Thư mục lưu dữ liệu bản địa hóa (`Resources` hoặc `Addressable`) |
| **Namespace** | Namespace cho các tệp C# được xuất |

### Tùy chọn tách/gộp tệp

| Tùy chọn | TRUE | FALSE |
|---|---|---|
| **Separate IDs Sheets** | Mỗi sheet `[%IDs]` → tệp riêng `[SheetName]IDs.cs` | Gộp tất cả vào `IDs.cs` |
| **Separate Constants Sheets** | Mỗi sheet `[%Constants]` → tệp riêng | Gộp tất cả vào `Constants.cs` |
| **Separate Localization Sheets** | Mỗi sheet `[Localization%]` → nhóm riêng: `[SheetName]_[lang].txt`, `[SheetName]Text.cs`, `[SheetName].cs` | Gộp tất cả vào: `Localization_[lang].txt`, `LocalizationText.cs`, `Localization.cs` |

### Tùy chọn khác

| Tùy chọn | Mô tả |
|---|---|
| **Only enum as IDs** | Các cột có đuôi `[enum]` chỉ xuất enum, bỏ qua hằng số Integer |
| **Combine JSON Sheets** | Gộp tất cả bảng dữ liệu từ 1 Excel → 1 tệp JSON `[ExcelName].txt` |
| **Language Character Set** | Biên dịch bảng ký tự cho TextMeshPro (Hàn, Nhật, Trung) |
| **Persistent Fields** | Giữ lại các ô trống khi xuất JSON (mặc định sẽ bị loại bỏ) |
| **Google Client ID / Secret** | Credentials từ Google Console để kết nối Google Sheets |

---

## 4. Xuất Excel Sheets

### 4.1. Xuất một tệp Excel

Điều hướng tới: `RCore > SheetX > Excel Spreadsheets`

![Export Single Excel](https://github.com/user-attachments/assets/2087c745-4a40-4dcd-a813-9ba1d0134c10)

Phù hợp cho database nhỏ, chỉ cần một tệp Excel. Các nút export:
- **Export IDs** → Tệp C# chứa ID hằng số
- **Export Constants** → Tệp C# chứa hằng số
- **Export JSON** → Tệp JSON từ bảng dữ liệu
- **Export Localization** → Dữ liệu + Component + API bản địa hóa
- **Export All** → Tất cả chỉ với 1 click

### 4.2. Xuất nhiều tệp Excel

![Export Multiple Excel](https://github.com/user-attachments/assets/fead0cb8-fdd1-4089-9d56-7ed288519fab)
![Edit Excel Sheets](https://github.com/user-attachments/assets/d958d749-5410-416b-9095-a598f9fe5a82)

Phù hợp cho database phức tạp chia thành nhiều tệp:
1. Thêm tất cả tệp Excel cần xử lý.
2. Chọn bao gồm/loại trừ các sheet trong mỗi tệp.
3. Nhấn **Export All** để xử lý toàn bộ.

---

## 5. Google Spreadsheets

Điều hướng tới: `RCore > SheetX > Google Spreadsheets`

### 5.1. Thiết lập Google Credentials

[Hướng dẫn lấy Google Client ID và Client Secret](https://hnb-rabear.github.io/sheetx/how-get-google-client-id-and-secret-id)

Dán **Client ID** và **Client Secret** vào Settings:

![Google Credentials](https://github.com/user-attachments/assets/9c4bddcc-d014-4b73-a71f-c817771fc8cb)

### 5.2. Xuất một Google Spreadsheet

![Export Single Google Sheet](https://github.com/user-attachments/assets/86bf3278-e61f-4b0b-b4bf-2feae0cf4429)

Nhập **Google Sheet ID** (tìm trong URL) và nhấn **Download**:

```
https://docs.google.com/spreadsheets/d/[GOOGLE_SHEET_ID]/edit?......
                                        ^^^^^^^^^^^^^^^^
                                        Đây là Sheet ID
```

### 5.3. Xuất nhiều Google Spreadsheets

Nhấp **Add Google Spreadsheets** → nhập ID → **Download** → chọn sheet:

![Export Multiple Google Sheets](https://github.com/user-attachments/assets/77df4421-a5ed-44d9-951d-1a36ce293781)
![Edit Google Sheets](https://github.com/user-attachments/assets/3386dda3-a2ba-4f88-87d0-f25e43ebfa56)

---

## 6. Quy Tắc Thiết Kế Bảng Tính

### 6.1. Sheet IDs

Tên sheet kết thúc bằng `IDs`. Mỗi nhóm được tổ chức trong **3 cột liên tiếp**: Tên Khóa, Giá trị (Integer), Ghi chú.

| Hero   |     |         | Building      |     |         | Pet      |     |         | Gender[enum]      |     |
| ------ | --- | ------- | ------------- | --- | ------- | -------- | --- | ------- | ----------------- | --- |
| HERO_1 | 1   | comment | BUILDING_NULL | 0   | comment | PET_NULL | 0   | comment | GENDER_NONE       | 0   |
| HERO_2 | 2   | comment | BUILDING_1    | 1   |         | PET_1    | 1   |         | GENDER_MALE       | 1   |
| HERO_3 | 3   | comment | BUILDING_2    | 2   |         | PET_2    | 2   |         | GENDER_FEMALE     | 2   |
|        |     |         | BUILDING_3    | 3   |         | PET_3    | 3   |         | GENDER_HELICOPTER | 3   |

**Quy tắc:**
- Tên sheet **phải** kết thúc bằng `IDs`.
- Chỉ cho phép kiểu Integer.
- Hàng đầu tiên chứa tên nhóm.
- Thêm hậu tố `[enum]` vào tên nhóm để xuất dưới dạng `enum` thay vì hằng số.

### 6.2. Sheet Constants

Tên sheet kết thúc bằng `Constants`. Bao gồm 4 cột: **Tên, Kiểu, Giá trị, Ghi chú**.

| Name                  | Type        | Value               | Comment               |
| --------------------- | ----------- | ------------------- | --------------------- |
| EXAMPLE_INT           | int         | 83                  | Ví dụ Integer         |
| EXAMPLE_FLOAT         | float       | 1.021               | Ví dụ Float           |
| EXAMPLE_STRING        | string      | 321fda              | Ví dụ String          |
| EXAMPLE_INT_ARRAY_1   | int-array   | 4                   | Ví dụ mảng Integer    |
| EXAMPLE_INT_ARRAY_2   | int-array   | 0:3:4:5             | Phân tách bằng `:`    |
| EXAMPLE_FLOAT_ARRAY_1 | float-array | 5                   | Ví dụ mảng Float      |
| EXAMPLE_FLOAT_ARRAY_2 | float-array | 5:1:1:3             | Mảng nhiều phần tử    |
| EXAMPLE_VECTOR2_1     | vector2     | 1:2                 | Ví dụ Vector2         |
| EXAMPLE_VECTOR2_2     | vector2     | 1:2:3               | Vector2 (bỏ phần tử được thừa) |
| EXAMPLE_VECTOR3       | vector3     | 3:3:4               | Ví dụ Vector3         |
| EXAMPLE_REFERENCE_1   | int         | HERO_1              | Tham chiếu đến ID     |
| EXAMPLE_REFERENCE_2   | int-array   | HERO_1 : HERO_2     | Mảng tham chiếu (`:`) |
| EXAMPLE_REFERENCE_3   | int-array   | HERO_1 \| HERO_3    | Mảng tham chiếu (`\|`) |
| EXAMPLE_FORMULA_1     | int         | =1*10*36            | Công thức Excel       |
| EXAMPLE_FORMULA_2     | float       | =1+2+3+4+5+6+7+8+9 | Công thức Excel       |

**Kiểu dữ liệu hỗ trợ:** `int`, `float`, `bool`, `string`, `int-array`, `float-array`, `vector2`, `vector3`

**Phân tách mảng:** Dùng `:`, `|`, hoặc xuống dòng (newline).

### 6.3. Sheet Localization

Tên sheet bắt đầu bằng `Localization`. Hai cột khóa: **`idString`** (khóa chính) và **`relativeId`** (khóa phụ, liên kết với sheet IDs).

| idstring     | relativeId | english                   | spanish                        | vietnamese                    |
| ------------ | ---------- | ------------------------- | ------------------------------ | ----------------------------- |
| message_1    |            | this is english message 1 | este es el mensaje en ingles 1 | đây là thông điệp tiếng Anh 1 |
| message_2    |            | this is english message 2 | este es el mensaje en ingles 2 | đây là thông điệp tiếng Anh 2 |
|              |            |                           |                                |                               |
| hero_name    | HERO_1     | hero name 1               | nombre del héroe 1             | tên anh hùng 1                |
| hero_name    | HERO_2     | hero name 2               | nombre del héroe 2             | tên anh hùng 2                |
| hero_name    | HERO_3     | hero name 3               | nombre del héroe 3             | tên anh hùng 3                |

**Quy tắc:**
- Khóa mỗi hàng = `idString` + `relativeId` (ví dụ: `hero_name_HERO_1`)
- `relativeId` có thể tham chiếu đến ID từ sheet IDs → tự động liên kết

### 6.4. Bảng Dữ Liệu JSON

#### Kiểu cơ bản: Boolean, Number, String

| numberExample1 | numberExample2 | numberExample3 | boolExample | stringExample |
| -------------- | -------------- | -------------- | ----------- | ------------- |
| 1              | 10             | 1.2            | TRUE        | văn bản       |
| 2              | 20             | 3.1            | TRUE        | văn bản       |
| 3              | BUILDING_8     | 5              | FALSE       | văn bản       |

> Giá trị ô có thể **tham chiếu đến ID** (ví dụ: `BUILDING_8` → giá trị integer tương ứng).

#### Kiểu mở rộng: Mảng và JSON Object

| Quy ước | Cú pháp tên cột | Ví dụ |
|---|---|---|
| **Mảng** | Kết thúc bằng `[]` | `skills[]`, `rewards[]` |
| **JSON Object** | Kết thúc bằng `{}` | `metadata{}`, `config{}` |

| array1[]                | array2[]    | JSON\{}                          |
| ----------------------- | ----------- | -------------------------------- |
| text1                   | 1           | \{}                              |
| text1 \| text2          | 1 \| 2      | \{"id":1, "name":"John Doe"}     |
| text1 \| text2 \| text3 | 1 \| 2 \| 3 | \{"id":HERO_2, "name":"Jane"}    |

**Phân tách phần tử mảng:** Dùng `|` hoặc xuống dòng.

#### Kiểu đặc biệt: Danh sách Thuộc tính (Attributes)

Kiểu dữ liệu đặc biệt dành cho game **RPG** — nơi nhân vật/trang bị có các thuộc tính linh hoạt, không cố định.

| attribute0 | value0 | unlock0 | increase0 | max0 | attribute1 | value1[] | unlock1[] | increase1[] | max1[]   |
| ---------- | ------ | ------- | --------- | ---- | ---------- | -------- | --------- | ----------- | -------- |
| ATT_HP     | 30     | 2       | 1.2       | 8    |            |          |           |             |          |
| ATT_AGI    | 25     | 3       | 1.5       | 8    |            |          |           |             |          |
| ATT_INT    | 30     | 2       | 1         | 5    | ATT_CRIT   | 3 \| 2   | 0 \| 11   | 0.5 \| 1    | 10 \| 20 |

![Ví dụ Attribute](https://github.com/nbhung100914/excel-to-unity/assets/9100041/2d619d56-5fa9-4371-b212-3e857bcbbead)

**Cấu trúc một thuộc tính:**

| Cột | Tên mẫu | Mô tả | Bắt buộc |
|---|---|---|---|
| **attribute** | `attribute0`, `attribute1`... | ID thuộc tính (Integer, từ sheet IDs) | ✅ Bắt buộc |
| **value** | `value0` hoặc `value0[]` | Giá trị thuộc tính (số hoặc mảng) | ✅ Bắt buộc |
| **increase** | `increase0` hoặc `increase0[]` | Mức tăng khi nâng cấp | Tùy chọn |
| **unlock** | `unlock0` hoặc `unlock0[]` | Điều kiện mở khóa (level/rank tối thiểu) | Tùy chọn |
| **max** | `max0` hoặc `max0[]` | Giá trị tối đa | Tùy chọn |

> **Quy tắc:** Các cột thuộc tính nên đặt ở **cuối bảng**. Index bắt đầu từ 0 và tăng dần.

---

## 7. Hướng Dẫn Tích Hợp

### Bước 1: Tải Example Package

📦 **[Tải SheetXExample.unitypackage](https://github.com/hnb-rabear/hnb-rabear.github.io/blob/main/sheetx/SheetXExample.unitypackage)**

Mở tệp Excel mẫu tại `/Assets/SheetX/Examples/Exporting a Single Excel/Example.xlsx`:

![Example Excel File](https://github.com/user-attachments/assets/2b4c8fe3-3c58-42bc-a85b-dea33c8122cf)

**Google Sheets mẫu:**
- Xuất một tệp: [Ví dụ](https://docs.google.com/spreadsheets/d/1_9BqoKwRsod5cMwML5n_pLpuWk045lD3Jd7nrizqVBo/edit?usp=drive_link)
- Xuất nhiều tệp: [Ví dụ 1](https://docs.google.com/spreadsheets/d/1l9_elk7QfABbWlKanOHqkSIYlWcxWO1EIPt9Ax4XtUE/edit?usp=drive_link) · [Ví dụ 2](https://docs.google.com/spreadsheets/d/1d53vWQzrp-qNsoeyEmkqQx4KeQObONOk55oWeNS2YXg/edit?usp=drive_link) · [Ví dụ 3](https://docs.google.com/spreadsheets/d/1i2CmDGYpAYuX_8vBUbHXBAhuWPKHi_gd52uwzsegLdY/edit?usp=drive_link) · [Ví dụ 4](https://docs.google.com/spreadsheets/d/1kq0KaQxQ129f1OABm62x6GtfOKTg_3t4M8gODGHzSu8/edit?usp=drive_link)

### Bước 2: Tạo thư mục xuất tệp

```
Assets/SheetXExample/
├── Scripts/Generated/      ← C# scripts
├── DataConfig/             ← JSON data
└── Resources/
    └── Localizations/      ← Localization data
```

Cấu hình đường dẫn trong `RCore > SheetX > Settings`.

> **Localization Output** có 2 cách:
> - **Resources** (đơn giản): Tạo thư mục trong `Resources/` → load bằng `Resources.Load`
> - **Addressable Assets** (nâng cao): Tạo thư mục ngoài `Resources/`, đánh dấu là Addressable

### Bước 3: Tạo C# classes và ScriptableObject

**Tạo Serializable classes** tương ứng với cấu trúc sheet:

```csharp
[Serializable]
public class ExampleData1
{
    public int numberExample1;
    public int numberExample2;
    public float numberExample3;
    public bool boolExample;
    public string stringExample;
}
```

```csharp
[Serializable]
public class ExampleData2
{
    [Serializable]
    public class Example
    {
        public int id;
        public string name;
    }

    public string[] array1;
    public int[] array2;
    public int[] array3;
    public bool[] array4;
    public int[] array5;
    public string[] array6;
    public Example json1;
}
```

```csharp
[Serializable]
public class ExampleData3
{
    public int id;
    public string name;
    public List<Attribute> Attributes;
}

[Serializable]
public class Attribute
{
    // Bắt buộc
    public int id;
    public float value;
    // Tùy chọn
    public int unlock;
    public float increase;
    public float max;
    public float[] values;
    public float[] increases;
    public float[] unlocks;
    public float[] maxes;
}
```

**Tạo ScriptableObject** để chứa dữ liệu:

```csharp
[CreateAssetMenu(fileName = "ExampleDataCollection", menuName = "SheetXExample/Create ExampleDataCollection")]
public class ExampleDataCollection : ScriptableObject
{
    public List<ExampleData1> exampleData1s;
    public List<ExampleData2> exampleData2s;
    public List<ExampleData3> exampleData3s;
}
```

**Load JSON vào ScriptableObject:**

```csharp
// LƯU Ý: Hàm này dùng UnityEditor API → đặt trong thư mục Editor hoặc #if UNITY_EDITOR
[ContextMenu("Load")]
private void LoadData()
{
    #if UNITY_EDITOR
    var txt = AssetDatabase.LoadAssetAtPath<TextAsset>("Assets/Import/Json/ExampleData1.txt");
    exampleData1s = JsonConvert.DeserializeObject<List<ExampleData1>>(txt.text);

    txt = AssetDatabase.LoadAssetAtPath<TextAsset>("Assets/SheetXExample/DataConfig/ExampleData2.txt");
    exampleData2s = JsonConvert.DeserializeObject<List<ExampleData2>>(txt.text);

    txt = AssetDatabase.LoadAssetAtPath<TextAsset>("Assets/SheetXExample/DataConfig/ExampleData3.txt");
    exampleData3s = JsonConvert.DeserializeObject<List<ExampleData3>>(txt.text);
    #endif
}
```

![Example Data Collection Inspector](https://github.com/user-attachments/assets/8a0a1dc4-3cac-4c88-bd7e-a3bc2fa7b546)

![Example Data Detail](https://github.com/user-attachments/assets/23e9aec3-cfbd-416c-8459-66cbb0e2fb58)

### Bước 4: Tích hợp Localization

**Khởi tạo:**
```csharp
LocalizationManager.Init();
```

**Thay đổi ngôn ngữ:**
```csharp
LocalizationsManager.CurrentLanguage = "jp"; // Tiếng Nhật
```

**Đăng ký sự kiện thay đổi ngôn ngữ:**
```csharp
LocalizationsManager.OnLanguageChanged += OnLanguageChanged;
```

**Truy xuất nội dung bản địa hóa (3 cách):**

**Cách 1 — Truy xuất bằng key** (không tự cập nhật khi đổi ngôn ngữ):
```csharp
m_simpleText1.text = LocalizationExample2.Get(LocalizationExample2.GO_TO_SHOP).ToString();
m_simpleText2.text = LocalizationExample2.Get(LocalizationExample2.REQUIRED_CITY_LEVEL_X, 10).ToString();
m_simpleText3.text = LocalizationExample2.Get("REQUIRED_CITY_LEVEL_X", 25).ToString();
```

**Cách 2 — Dynamic Text** (tự động cập nhật khi đổi ngôn ngữ):
```csharp
// Đăng ký
LocalizationExample2.RegisterDynamicText(m_dynamicText1.gameObject, LocalizationExample2.TAP_TO_COLLECT);
LocalizationExample2.RegisterDynamicText(m_dynamicText2.gameObject, LocalizationExample2.REQUIRED_LEVEL_X, "3");

// Hủy đăng ký
Localization.UnregisterDynamicText(m_textGameObject1);
```

**Cách 3 — Sử dụng Component** (kéo thả trong Inspector):

![Localization Component](https://github.com/user-attachments/assets/0f0214b9-51ed-44bf-9b27-f2a210e6f0f6)

#### Gộp tất cả Localization Sheets

Bỏ chọn **"Separate Localization Sheets"** trong Settings → xóa tệp cũ → Export All lại.

Thay thế: `LocalizationExample1` / `LocalizationExample2` → `Localization`, và `LocalizationExample1Text` → `LocalizationText`.

#### Tạo Font TextMeshPro cho các ngôn ngữ CJK

Sử dụng các tệp bộ ký tự `characters_set_jp`, `characters_set_ko`, `characters_set_cn` (được tạo tự động từ sheet localization):

| Ngôn ngữ | Font sử dụng |
|---|---|
| Tiếng Nhật | NotoSerif-Bold |
| Tiếng Hàn | NotoSerifJP-Bold |
| Tiếng Trung | NotoSerifTC-Bold |

**Cách tạo:**
1. Mở **Font Asset Creator** trong Unity.
2. Chọn **Character Set** → *Character From File*.
3. Chọn tệp bộ ký tự phù hợp (ví dụ: `characters_set_jp`).

![Font JP](https://github.com/user-attachments/assets/7bc98c77-9994-4551-8e5a-dae51eba9f45)

![Font KR](https://github.com/user-attachments/assets/dc14fbbb-b38f-4f56-89b0-844d94b825cb)

![Font CN](https://github.com/user-attachments/assets/08020e00-14b1-47cd-a9f2-be3d4321ca48)

#### Tải Localization bằng Addressable Assets

1. Cài đặt **Addressable Assets System**.
2. Thêm `ADDRESSABLES` vào scripting define symbols trong Build Settings.
3. Di chuyển thư mục Localizations **ra khỏi** thư mục `Resources`.
4. Cập nhật đường dẫn trong SheetX Settings.
5. Đánh dấu thư mục Localizations là **Addressable Asset**.

![Addressable Settings](https://github.com/user-attachments/assets/ee17fdaa-c951-4f9c-8a6b-a5e2614db546)

![Addressable Folder](https://github.com/user-attachments/assets/1ecf2ae1-00e9-4c9f-9056-2867d04e8ee1)

![Build Settings](https://github.com/user-attachments/assets/229da607-da10-4b87-b799-5d9549e5620d)