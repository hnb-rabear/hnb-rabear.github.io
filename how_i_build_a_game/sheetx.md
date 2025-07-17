# Tài liệu SheetX

## 1. Giới thiệu

SheetX là một công cụ đơn giản hóa việc thiết kế và quản lý cơ sở dữ liệu cho các nhà phát triển và thiết kế trò chơi, cho phép dễ dàng sửa đổi cấu hình và thống kê.

Khi các dự án phát triển, nhu cầu quản lý hiệu quả các bảng dữ liệu, hằng số và ID tăng lên. SheetX tập trung hóa quy trình, cho phép tìm kiếm, sửa đổi và cập nhật dễ dàng.

Công cụ này hỗ trợ nhiều loại dữ liệu khác nhau và sử dụng các công cụ bảng tính phổ biến để quản lý dữ liệu.

**Để cài đặt, URL Git sau vào Unity Package Manager:**
https://github.com/hnb-rabear/RCore.git?path=Assets/RCore.SheetX

**Hoặc bạn có thể cài đặt phiên bản Winform [Tại đây](https://github.com/hnb-rabear/excel-to-unity)**


**Bạn có thể tải xuống Example [Tại đây](https://github.com/hnb-rabear/hnb-rabear.github.io/blob/main/sheetx/SheetXExample.unitypackage)**

**Xem trước các loại dữ liệu được SheetX hỗ trợ [Tại đây](https://docs.google.com/spreadsheets/d/1_9BqoKwRsod5cMwML5n_pLpuWk045lD3Jd7nrizqVBo/edit?usp=sharing)**

## 2. Các chức năng chính

- **Excel and Google Sheets Integration:** Quản lý toàn bộ cơ sở dữ liệu bằng Excel hoặc Google Spreadsheets.
- **ID and Constants Management:** Thực hiện điều chỉnh hàng loạt cho ID và hằng số mà không ảnh hưởng đến cơ sở dữ liệu.
- **Localization System:** Xử lý nhiều ngôn ngữ một cách dễ dàng, tích hợp liền mạch với Unity.
- **Export JSON:** Chuyển đổi bảng dữ liệu thành tệp JSON để dễ dàng tích hợp với Unity.
- **Flexible Data Format:** Hỗ trợ nhiều định dạng dữ liệu, phù hợp với nhu cầu thiết kế của bạn.

## 3. Cài đặt

Điều hướng đến menu chính và chọn: `RCore > SheetX > Settings`

![sheetx_settings](https://github.com/user-attachments/assets/cd15d421-dfd5-4bbc-9e88-717a480c2ebe)

- **Scripts Output Folder:** Lưu trữ các script C# được xuất, bao gồm ID, Hằng số, Thành phần Bản địa hóa và API Bản địa hóa.
- **JSON Output Folder:** Lưu trữ dữ liệu JSON được xuất.
- **Localization Output:** Lưu trữ Dữ liệu Bản địa hóa, nên nằm trong thư mục Resources để tải qua Resources, hoặc trong thư mục Localizations để tải qua Hệ thống Addressable Asset.
- **Namespace:** Xác định namespace cho các tệp C# được xuất.
- **Separate IDs: Sheets**
  - TRUE: Xuất các sheet _[%IDs]_ thành các tệp C# riêng lẻ có tên _[SheetName] + IDs.cs_.
  - FALSE: Gộp tất cả các sheet _[%IDs]_ từ tất cả các tệp Excel vào một tệp C# duy nhất có tên _IDs.cs_.
- **Separate Constants: Sheets**
  - TRUE: Xuất các sheet _[%Constants]_ thành các tệp C# riêng lẻ có tên _[SheetName] + %Constants.cs_.
  - FALSE: Gộp tất cả các sheet _[%Constants]_ từ tất cả các tệp Excel vào một tệp C# duy nhất có tên _Constants.cs_.
- **Separate Localization Sheets:**
  - TRUE (mặc định): Xuất các sheet _[Localization%]_ thành các nhóm riêng biệt, mỗi nhóm chứa Dữ liệu Bản địa hóa, Thành phần và API, với cấu trúc tên tệp như sau:
    - Dữ liệu Bản địa hóa: _[SheetName]\_[language].txt_
    - Thành phần: _[SheetName] + Text.cs_
    - API: _[SheetName].cs_
  - FALSE: Gộp tất cả các sheet _[Localization%]_ từ tất cả các tệp Excel vào một nhóm duy nhất, với cấu trúc tên tệp như sau:
    - Dữ liệu Bản địa hóa: _Localization\_ + [language].txt_
    - Thành phần: _LocalizationText.cs_
    - API: _Localization.cs_
- **Only enum as IDs:** Đối với các sheet _[%IDs]_, các cột có đuôi _[enum]_ sẽ được xuất dưới dạng enum và không bao gồm dạng Hằng số Integer.
- **Combine JSON Sheets:** Gộp Bảng Dữ liệu từ một tệp Excel vào một tệp JSON duy nhất, có tên _[ExcelName].txt_.
- **Language Character Set:** Được sử dụng trong Bản địa hóa với TextMeshPro để biên dịch bảng ký tự của một ngôn ngữ, chủ yếu áp dụng cho tiếng Hàn, tiếng Nhật và tiếng Trung do hệ thống ký tự phức tạp của chúng.
- **Persistent Fields:** Mặc định, các ô trống sẽ bị loại bỏ khi xuất sang JSON. Nếu bạn muốn giữ lại các ô trống này, hãy thêm tên cột của chúng vào hộp Persistent Fields.
- **Google Client ID:** Nhập Google Client ID của bạn (lấy từ Credentials trong Google Console).
- **Google Client Secret:** Nhập Google Secret của bạn (lấy từ Credentials trong Google Console).

## 4. Xuất Excel Sheets

### 4.1. Xuất một tệp Excel duy nhất

Điều hướng đến menu chính và chọn: `RCore > SheetX > Excel Spreadsheets`

![sheetx_excel_1](https://github.com/user-attachments/assets/2087c745-4a40-4dcd-a813-9ba1d0134c10)

Chức năng này lý tưởng để tìm hiểu cách sử dụng công cụ. Nó phù hợp cho các Cơ sở Dữ liệu Tĩnh nhỏ, đơn giản chỉ cần một tệp Excel cho tất cả dữ liệu.

Các chức năng chính:

- **Export IDs:** Chuyển đổi các sheet ID thành tệp C#.
- **Export Constants:** Chuyển đổi các sheet Hằng số thành tệp C#.
- **Export JSON:** Chuyển đổi các sheet Bảng Dữ liệu thành dữ liệu JSON.
- **Export Localization:** Xuất Dữ liệu Bản địa hóa, Thành phần Bản địa hóa và API Bản địa hóa.
- **Export All:** Thực hiện tất cả các chức năng chỉ với một lần nhấp.

### 4.2. Xuất nhiều tệp Excel

![sheetx_excel_2](https://github.com/user-attachments/assets/fead0cb8-fdd1-4089-9d56-7ed288519fab)
![tab_excel_2_edit](https://github.com/user-attachments/assets/d958d749-5410-416b-9095-a598f9fe5a82)

Tính năng này cần thiết để quản lý các Cơ sở Dữ liệu Tĩnh phức tạp được chia thành nhiều tệp Excel. Nó giúp bạn xử lý và xuất tất cả các tệp một cách hiệu quả chỉ với một lần nhấp:

1. Thêm tất cả các tệp Excel bạn muốn xử lý.
2. Đối với mỗi tệp Excel, bạn có tùy chọn chọn bao gồm hoặc loại trừ các sheet.
3. Nhấn nút Export All để hoàn tất quá trình.

## 5. Google Spreadsheets

Ưu tiên sử dụng Google Spreadsheets? Không vấn đề gì.

Điều hướng đến menu chính và chọn: `RCore > SheetX > Google Spreadsheets`

### 5.1. Thiết lập Google Client ID and Client Secret

[Xem hướng dẫn tại đây](https://hnb-rabear.github.io/sheetx/how-get-google-client-id-and-secret-id)

Sao chép **Client ID** và **Client Secret**, sau đó dán chúng vào các cài đặt tương ứng trong cửa sổ **Sheets Exporter Settings**

![sheetx_settings_2](https://github.com/user-attachments/assets/9c4bddcc-d014-4b73-a71f-c817771fc8cb)

### 5.2. Xuất một Google Spreadsheet duy nhất

![sheetx_google_1](https://github.com/user-attachments/assets/86bf3278-e61f-4b0b-b4bf-2feae0cf4429)

Nhập ID Google Sheet, sau đó nhấp vào nút Tải xuống. Bạn có thể tìm ID trong URL của Google Sheet, được định dạng như sau:

```url
https://docs.google.com/spreadsheets/d/[GOOGLE_SHEET_ID]/edit?......
```

### 5.3. Xuất nhiều Google Spreadsheets

Nhấp vào **Add Google Spreadsheets**, sau đó nhập ID Google Sheet vào cửa sổ bật lên. Nhấn **Tải xuống**, sau đó chọn các sheet bạn muốn xử lý.

![sheetx_google_2](https://github.com/user-attachments/assets/77df4421-a5ed-44d9-951d-1a36ce293781)
![tab_google_2_edit](https://github.com/user-attachments/assets/3386dda3-a2ba-4f88-87d0-f25e43ebfa56)

## 6. Quy tắc trong Bảng tính

### 6.1. ID

| Hero   |     |         | Building      |     |         | Pet      |     |         | Gender[enum]      |     |
| ------ | --- | ------- | ------------- | --- | ------- | -------- | --- | ------- | ----------------- | --- |
| HERO_1 | 1   | comment | BUILDING_NULL | 0   | comment | PET_NULL | 0   | comment | GENDER_NONE       | 0   |
| HERO_2 | 2   | comment | BUILDING_1    | 1   |         | PET_1    | 1   |         | GENDER_MALE       | 1   |
| HERO_3 | 3   | comment | BUILDING_2    | 2   |         | PET_2    | 2   |         | GENDER_FEMALE     | 2   |
|        |     |         | BUILDING_3    | 3   |         | PET_3    | 3   |         | GENDER_HELICOPTER | 3   |
|        |     |         | BUILDING_4    | 4   |         | PET_4    | 4   |         |                   |     |
|        |     |         | BUILDING_5    | 5   |         | PET_5    | 5   |         |                   |     |
|        |     |         | BUILDING_6    | 6   |         | PET_6    | 6   |         |                   |     |
|        |     |         | BUILDING_7    | 7   |         | PET_7    | 7   |         |                   |     |
|        |     |         | BUILDING_8    | 8   |         |          |     |         |                   |     |

Các Sheet ID, được đặt tên với hậu tố `IDs`, được sử dụng để biên dịch tất cả ID thành Hằng số Integer. Các quy tắc thiết kế bao gồm:

- Tên sheet phải kết thúc bằng `IDs`.
- Chỉ cho phép kiểu dữ liệu Integer.
- Mỗi nhóm được tổ chức trong 3 cột liên tiếp.
- Hàng đầu tiên chứa tên nhóm để dễ tham khảo.
- Cột đầu tiên chứa Tên Khóa, và cột tiếp theo chứa Giá trị Khóa.
- Giá trị Khóa phải là số nguyên.
- Mặc định, tất cả ID trong một cột sẽ được xuất dưới dạng Hằng số Integer. Thêm hậu tố [enum] vào tên nhóm để xuất chúng dưới dạng enum.
- Để chỉ xuất enum và bỏ qua Hằng số Integer, chọn `Only enum as IDs` trong Cài đặt.

```a
| Group | Key | Comment |
| ----- | --- | ------- |
```

### 6.2. Hằng số

| Name                  | Type        | Value              | Comment               |
| --------------------- | ----------- | ------------------ | --------------------- |
| EXAMPLE_INT           | int         | 83                 | Ví dụ Integer         |
| EXAMPLE_FLOAT         | float       | 1.021              | Ví dụ Float           |
| EXAMPLE_STRING        | string      | 321fda             | Ví dụ String          |
| EXAMPLE_INT_ARRAY_1   | int-array   | 4                  | Ví dụ mảng Integer    |
| EXAMPLE_INT_ARRAY_2   | int-array   | 0:3:4:5            | Ví dụ mảng Integer    |
| EXAMPLE_FLOAT_ARRAY_1 | float-array | 5                  | Ví dụ mảng Float      |
| EXAMPLE_FLOAT_ARRAY_2 | float-array | 5:1:1:3            | Ví dụ mảng Float      |
| EXAMPLE_VECTOR2_1     | vector2     | 1:2                | Ví dụ Vector2         |
| EXAMPLE_VECTOR2_2     | vector2     | 1:2:3              | Ví dụ Vector2         |
| EXAMPLE_VECTOR3       | vector3     | 3:3:4              | Ví dụ Vector3         |
| EXAMPLE_REFERENCE_1   | int         | HERO_1             | Ví dụ Integer         |
| EXAMPLE_REFERENCE_2   | int-array   | HERO_1 : HERO_2    | Ví dụ mảng Integer    |
| EXAMPLE_REFERENCE_3   | int-array   | HERO_1 \| HERO_3   | Ví dụ mảng Integer    |
| EXAMPLE_REFERENCE_4   | int-array   | HERO_1 HERO_4      | Ví dụ mảng Integer    |
| EXAMPLE_FORMULA_1     | int         | =1*10*36           | Ví dụ công thức Excel |
| EXAMPLE_FORMULA_2     | float       | =1+2+3+4+5+6+7+8+9 | Ví dụ công thức Excel |

Các Sheet Hằng số, được đặt tên với hậu tố `Constants`, biên dịch các hằng số dự án. Các quy tắc thiết kế bao gồm:

- Tên sheet phải kết thúc bằng `Constants`.
- Có bốn cột: Tên, Kiểu, Giá trị và Ghi chú.
  - Tên: Tên của hằng số; phải liền mạch, không chứa ký tự đặc biệt.
  - Kiểu: Kiểu dữ liệu của hằng số. Các kiểu dữ liệu có thể bao gồm: `int`, `float`, `bool`, `string`, `int-array`, `float-array`, `vector2` và `vector3`.
  - Giá trị: Giá trị phù hợp với kiểu dữ liệu. Đối với kiểu mảng, các phần tử được phân tách bằng `:`, `|` hoặc `newline`.

```a
| Name | Type | Value | Comment |
| ---- | ---- | ----- | ------- |
```

### 6.3. Bản địa hóa

| idstring     | relativeId | english                   | spanish                        | vietnamese                   |
| ------------ | ---------- | ------------------------- | ------------------------------ | ---------------------------- |
| message_1    |            | this is english message 1 | este es el mensaje en ingles 1 | đây là thông điệp tiếng Anh 1 |
| message_2    |            | this is english message 2 | este es el mensaje en ingles 2 | đây là thông điệp tiếng Anh 2 |
| message_3    |            | this is english message 3 | este es el mensaje en ingles 3 | đây là thông điệp tiếng Anh 3 |
|              |            |                           |                                |                              |
| content      | 1          | this is english message 1 | este es el mensaje en ingles 1 | đây là thông điệp tiếng Anh 1 |
| content      | 2          | this is english message 2 | este es el mensaje en ingles 2 | đây là thông điệp tiếng Anh 2 |
| content      | 3          | this is english message 3 | este es el mensaje en ingles 3 | đây là thông điệp tiếng Anh 3 |
|              |            |                           |                                |                              |
| title_1      |            | this is english title 1   | este es el titulo 1 en ingles  | đây là tiêu đề tiếng Anh 1   |
| title_2      |            | this is english title 2   | este es el titulo 2 en ingles  | đây là tiêu đề tiếng Anh 2   |
| title_3      |            | this is english title 3   | este es el titulo 3 en ingles  | đây là tiêu đề tiếng Anh 3   |
|              |            |                           |                                |                              |
| whatever_msg |            | this is a sample message  | este es un mensaje de muestra  | đây là một thông điệp mẫu    |
|              |            |                           |                                |                              |
| hero_name    | HERO_1     | hero name 1               | nombre del héroe 1             | tên anh hùng 1               |
| hero_name    | HERO_2     | hero name 2               | nombre del héroe 2             | tên anh hùng 2               |
| hero_name    | HERO_3     | hero name 3               | nombre del héroe 3             | tên anh hùng 3               |

Các Sheet Bản địa hóa được đặt tên với tiền tố `Localization` và tuân theo các quy tắc sau:

- Tên sheet phải bắt đầu bằng `Localization`.
- Mỗi sheet có hai cột khóa: khóa chính `idString` và khóa phụ `relativeId`.
- Các cột tiếp theo chứa nội dung được bản địa hóa.
- Khóa cho mỗi hàng là sự kết hợp của `idString` và `relativeId`.
- `relativeId` có thể tham chiếu đến một ID từ các sheet ID.

```a
| idString | relativeId | english | spanish | vietnamese | .... |
| -------- | ---------- | ------- | ------- | ---------- | ---- |
```

### 6.4. Bảng dữ liệu - Dữ liệu JSON

#### Kiểu dữ liệu cơ bản: Boolean, Number, String

| numberExample1 | numberExample2 | numberExample3 | boolExample | stringExample |
| -------------- | -------------- | -------------- | ----------- | ------------- |
| 1              | 10             | 1.2            | TRUE        | văn bản       |
| 2              | 20             | 3.1            | TRUE        | văn bản       |
| 3              | BUILDING_8     | 5              | FALSE       | văn bản       |
| 6              | HERO_3         | 10.7           | FALSE       | văn bản       |
| 9              | PET_2          | 16.4           | FALSE       | văn bản       |

#### Kiểu dữ liệu mở rộng: Mảng, Đối tượng JSON

| array1[]                | array2[]    | array3[]                       | array4[]              | array5[]   | array6[]    | JSON\{}                                                                   |
| ----------------------- | ----------- | ------------------------------ | --------------------- | ---------- | ----------- | ------------------------------------------------------------------------- |
| text1                   | 1           | 1                              | TRUE                  | 123<br/>66 | aaa<br/>ccc | \{}                                                                       |
| text2                   | 2 \| 2 \| 3 | 1 \| 2 \| 3                    | TRUE \| FALSE \| TRUE | 123<br/>71 | aaa<br/>ccc | \{"id":1, "name":"John Doe 1"}                                            |
| text1 \| text2          | 1 \| 2      | 1 \| BUILDING_2                | TRUE \| FALSE         | 123<br/>67 | aaa<br/>ccc | \{"id":2, "name":"John Doe 2"}                                            |
| text1 \| text2 \| text3 | 1 \| 2 \| 3 | BUILDING_1 \| HERO_2           | TRUE \| FALSE \| TRUE | 123<br/>68 | aaa<br/>ccc | \{"id":HERO_2, "name":"JohnDoe 2"}                                        |
| text3                   | 4 \| 2      | BUILDING_3 \| HERO_1 \| HERO_2 | TRUE \| FALSE         | 123<br/>76 | aaa<br/>ccc | [\{"id":HERO_1, "name":"John Doe 1"},\{"id":HERO_2, "name":"Mary Sue 2"}] |
| text1 \| text2 \| text7 | 5           | 1 \| 2 \| 4 \| PET_5           | TRUE                  | 123<br/>78 | aaa<br/>ccc | [\{"id":HERO_1, "name":"John Doe 1"},\{"id":HERO_2, "name":"Mary Sue 2"}] |

- Đối với kiểu mảng, tên cột phải kết thúc bằng `[]`.
- Đối với kiểu đối tượng JSON, tên cột phải kết thúc bằng `{}`.

#### Kiểu dữ liệu đặc biệt: Danh sách thuộc tính

| attribute0 | value0 | unlock0 | increase0 | max0 | attribute1 | value1[] | unlock1[] | increase1[] | max1[]   | ... | attributeN |
| ---------- | ------ | ------- | --------- | ---- | ---------- | -------- | --------- | ----------- | -------- | --- | ---------- |
| ATT_HP     | 30     | 2       | 1.2       | 8    |            |          |           |             |          | ... |            |
| ATT_AGI    | 25     | 3       | 1.5       | 8    |            |          |           |             |          | ... |            |
| ATT_INT    | 30     | 2       | 1         | 5    | ATT_CRIT   | 3 \| 2   | 0 \| 11   | 0.5 \| 1    | 10 \| 20 | ... |            |
| ATT_ATK    | 30     | 2       | 1         | 8    | ATT_CRIT   | 10 \| 1  | 1 \| 12   | 1.5 \| 1    | 10 \| 20 | ... |            |
|            |        |         |           |      | ATT_CRIT   | 10 \| 1  | 1 \| 12   | 1.5 \| 1    | 10 \| 20 | ... |            |

Thuộc tính là một kiểu dữ liệu đặc biệt, được tạo ra dành riêng cho các trò chơi thể loại RPG - nơi mà nhân vật và trang bị có thể sở hữu các thuộc tính và số liệu khác nhau và không cố định. Kiểu dữ liệu này giúp việc tùy chỉnh nhân vật và trang bị trở nên linh hoạt hơn, không bị giới hạn.

![Ví dụ Thuộc tính](https://github.com/nbhung100914/excel-to-unity/assets/9100041/2d619d56-5fa9-4371-b212-3e857bcbbead)

Để xác định kiểu đối tượng thuộc tính, cần tuân theo các quy tắc sau:

- Các cột thuộc tính nên được đặt ở cuối bảng dữ liệu.
- ID thuộc tính là một hằng số nguyên, vì vậy nó nên được định nghĩa trong sheet ID.
- Một thuộc tính có cấu trúc như sau:

  1. **attribute**: Tên cột tuân theo mẫu _`attribute + (index)`_, trong đó index có thể là bất kỳ số nào, nhưng nên bắt đầu từ 0 và tăng dần. Giá trị của cột này là ID của thuộc tính, là kiểu Integer, giá trị này nên được đặt trong sheet ID.
  2. **value**: Tên cột tuân theo mẫu _`value + (index)`_ hoặc _`value + (index) + []`_, giá trị của cột có thể là số hoặc mảng số.
  3. **increase**: Tên cột tuân theo mẫu _`increase + (index)`_ hoặc _`increase + (index) + []`_. Đây là giá trị bổ sung, có thể có hoặc không, thường được sử dụng cho các tình huống nâng cấp, chỉ định mức tăng thêm khi nhân vật hoặc vật phẩm lên cấp.
  4. **unlock**: Tên cột tuân theo mẫu _`unlock + (index)`_ hoặc _`unlock + (index) + []`_. Đây là giá trị bổ sung, có thể có hoặc không, thường được sử dụng cho các tình huống thuộc tính cần điều kiện để mở khóa, như cấp độ tối thiểu hoặc hạng tối thiểu.
  5. **max**: Tên cột tuân theo mẫu _`max + (index)`_ hoặc _`max + (index) + []`_. Đây là giá trị bổ sung, có thể có hoặc không, thường được sử dụng cho các tình huống thuộc tính có giá trị tối đa.

    ```a
    Ví dụ 1: attribute0, value0, increase0, value0, max0.
    Ví dụ 2: attribute1, value1[], increase1[], value1[], max1[].
    ```

## 7. Cách tích hợp

**Tải xuống và nhập [Ví dụ](https://github.com/hnb-rabear/hnb-rabear.github.io/blob/main/sheetx/SheetXExample.unitypackage)**

Đầu tiên, mở tệp Excel nằm tại `/Assets/SheetX/Examples/Exporting a Single Excel/Example.xlsx`. Đây là một tệp Excel mẫu. Trong tệp này, có các sheet chứa dữ liệu mẫu giúp bạn hiểu cách thiết kế các loại dữ liệu như ID, Hằng số và Bảng Dữ liệu.

![Tệp Excel](https://github.com/user-attachments/assets/2b4c8fe3-3c58-42bc-a85b-dea33c8122cf)

**Đối với ví dụ sử dụng Google Sheets, bạn có thể xem tệp tại đây.**

Ví dụ để xuất một tệp
[**Ví dụ**](https://docs.google.com/spreadsheets/d/1_9BqoKwRsod5cMwML5n_pLpuWk045lD3Jd7nrizqVBo/edit?usp=drive_link)

Ví dụ để xuất nhiều tệp
[**Ví dụ 1**](https://docs.google.com/spreadsheets/d/1l9_elk7QfABbWlKanOHqkSIYlWcxWO1EIPt9Ax4XtUE/edit?usp=drive_link)
[**Ví dụ 2**](https://docs.google.com/spreadsheets/d/1d53vWQzrp-qNsoeyEmkqQx4KeQObONOk55oWeNS2YXg/edit?usp=drive_link)
[**Ví dụ 3**](https://docs.google.com/spreadsheets/d/1i2CmDGYpAYuX_8vBUbHXBAhuWPKHi_gd52uwzsegLdY/edit?usp=drive_link)
[**Ví dụ 4**](https://docs.google.com/spreadsheets/d/1kq0KaQxQ129f1OABm62x6GtfOKTg_3t4M8gODGHzSu8/edit?usp=drive_link)

### 7.1. Tạo thư mục để xuất tệp

Tạo 3 thư mục để lưu trữ các tệp sẽ được xuất:

- Một thư mục để lưu trữ các script C# (ID, Hằng số, Thành phần Bản địa hóa, API Bản địa hóa).
- Một thư mục để lưu trữ các tệp dữ liệu JSON.
- Một thư mục để lưu trữ dữ liệu Bản địa hóa.

  - Có hai cách để thiết lập thư mục cho dữ liệu Bản địa hóa, tùy thuộc vào cách bạn muốn tải Bản địa hóa:
    - Cách đơn giản nhất là tải từ thư mục Resources. Tạo một thư mục bên trong thư mục Resources để lưu trữ dữ liệu Bản địa hóa. Bạn có thể đặt tên thư mục này bất kỳ tên nào bạn muốn.
    - Ngoài ra, sử dụng Hệ thống Addressable Asset. Trong trường hợp này, tạo một thư mục "Localizations" bên ngoài thư mục Resources và đặt nó làm Addressable Asset. Nên đặt tên thư mục này là "Localizations".

- Điều hướng đến `RCore > SheetX > Settings`
- Trong Cài đặt Xuất Sheets, thiết lập đường dẫn cho "Scripts Output Folder," "JSON Output Folder," và "Localization Output" bằng cách sử dụng ba thư mục bạn vừa tạo.

Trong ví dụ này, tôi sẽ tạo 3 thư mục:

- `Assets\SheetXExample\Scripts\Generated`: cho các script C#
- `Assets\SheetXExample\DataConfig`: cho dữ liệu JSON
- `Assets\SheetXExample\Resources\Localizations`: cho dữ liệu Bản địa hóa

### 7.2. Lập trình

#### Tạo ScriptableObject làm Kho lưu trữ Cơ sở Dữ liệu Tĩnh

- Tạo các lớp Serializable tương ứng với các trường dữ liệu trong bảng dữ liệu.

```cs
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

```cs
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

```cs
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
    //=== CHÍNH
    public int id;
    public float value;
    public int unlock;
    public float increase;
    public float max;
    //=== Tùy chọn
    public float[] values;
    public float[] increases;
    public float[] unlocks;
    public float[] maxes;
}
```

- Tạo một ScriptableObject để bao bọc các lớp Serializable trên.

```cs
[CreateAssetMenu(fileName = "ExampleDataCollection", menuName = "SheetXExample/Create ExampleDataCollection")]
public class ExampleDataCollection : ScriptableObject
{
    public List<ExampleData1> exampleData1s;
    public List<ExampleData2> exampleData2s;
    public List<ExampleData3> exampleData3s;
}
```

- Tải dữ liệu JSON vào các lớp Serializable

```cs
// LƯU Ý: Hàm này sử dụng thư viện UnityEditor và phải được đặt trong thư mục Editor hoặc trong các chỉ thị #if UNITY_EDITOR.
// Nếu bạn không muốn sử dụng mã Editor, bạn có thể lưu trữ các tệp dữ liệu JSON trong thư mục Resources hoặc Asset Bundles và tải chúng tương ứng.
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

![Bộ sưu tập Dữ liệu Ví dụ](https://github.com/user-attachments/assets/8a0a1dc4-3cac-4c88-bd7e-a3bc2fa7b546)

![Bộ sưu tập Dữ liệu Ví dụ](https://github.com/user-attachments/assets/23e9aec3-cfbd-416c-8459-66cbb0e2fb58)

### 7.3. Tích hợp Bản địa hóa

- Khởi tạo

```cs
LocalizationManager.Init();
```

- Thay đổi ngôn ngữ.

```cs
// Đặt ngôn ngữ là tiếng Nhật
LocalizationsManager.CurrentLanguage = "jp";
```

- Đăng ký một trình xử lý sự kiện cho sự kiện thay đổi ngôn ngữ.

```cs
// Đăng ký một hành động khi ngôn ngữ thay đổi
LocalizationsManager.OnLanguageChanged += OnLanguageChanged;
```

- Bạn có thể truy xuất nội dung bản địa hóa bằng ba phương pháp khác nhau.

  1. Truy xuất nội dung bản địa hóa bằng Khóa. Lưu ý rằng văn bản sẽ không tự động làm mới khi ngôn ngữ thay đổi bằng phương pháp này.

      ```cs
      // Truy xuất văn bản bản địa hóa bằng khóa số nguyên
      m_simpleText1.text = LocalizationExample2.Get(LocalizationExample2.GO_TO_SHOP).ToString();
      // Truy xuất văn bản bản địa hóa bằng khóa số nguyên với một đối số
      m_simpleText2.text = LocalizationExample2.Get(LocalizationExample2.REQUIRED_CITY_LEVEL_X, 10).ToString();
      // Truy xuất văn bản bản địa hóa bằng khóa chuỗi với một đối số
      m_simpleText3.text = LocalizationExample2.Get("REQUIRED_CITY_LEVEL_X", 25).ToString();
      ```

  2. Liên kết một GameObject chứa thành phần Text hoặc TextMeshProUGUI với một khóa để văn bản tự động cập nhật khi ngôn ngữ thay đổi.

      ```cs
      // Đăng ký văn bản bản địa hóa động bằng khóa số nguyên
      LocalizationExample2.RegisterDynamicText(m_dynamicText1.gameObject, LocalizationExample2.TAP_TO_COLLECT);
      // Đăng ký văn bản bản địa hóa động bằng khóa số nguyên với một đối số
      LocalizationExample2.RegisterDynamicText(m_dynamicText2.gameObject, LocalizationExample2.REQUIRED_LEVEL_X, "3");
      // Đăng ký văn bản bản địa hóa động bằng khóa chuỗi với một đối số
      LocalizationExample2.RegisterDynamicText(m_dynamicText3.gameObject, "REQUIRED_LEVEL_X", "30");
      ```

      ```cs
      // Hủy đăng ký GameObject
      Localization.UnregisterDynamicText(m_textGameObject1);
      Localization.UnregisterDynamicText(m_textGameObject2);
      Localization.UnregisterDynamicText(m_dynamicText3);
      ```

  3. Sử dụng Thành phần Bản địa hóa.

      ![Sử dụng Thành phần Bản địa hóa](https://github.com/user-attachments/assets/0f0214b9-51ed-44bf-9b27-f2a210e6f0f6)

#### Kết hợp Bản địa hóa

Nếu bạn muốn kết hợp tất cả các Sheet Bản địa hóa, chỉ cần bỏ chọn hộp kiểm "Separate Localization Sheets" trong Cài đặt. Tiếp theo, xóa tất cả các tệp đã tạo và xuất lại mọi thứ.

Sau đó, thay thế các trường hợp của **LocalizationExample1** và **LocalizationExample2** bằng **Localization**. Cũng thay thế thành phần **LocalizationExample1Text** và **LocalizationExample2Text** bằng **LocalizationText**.

#### Tạo Phông chữ TextMeshPro cho các Ngôn ngữ Khác nhau

Để tạo phông chữ TextMeshPro cho tiếng Nhật, tiếng Hàn và tiếng Trung, hãy làm theo các bước sau sử dụng các tệp bộ ký tự tương ứng **characters_set_jp**, **characters_set_ko** và **characters_set_cn**, bao gồm tất cả các ký tự từ các sheet bản địa hóa:

Phông chữ sử dụng trong ví dụ này:

- Tiếng Nhật: NotoSerif-Bold
- Tiếng Hàn: NotoSerifJP-Bold
- Tiếng Trung: NotoSerifTC-Bold

Tạo Phông chữ TextMeshPro:

- Đối với mỗi phông chữ ngôn ngữ, tạo một tài sản phông chữ TextMeshPro.
- Mở cửa sổ Font Asset Creator trong Unity.
- Trong phần _Character Set_, chọn _Character From File_.
- Chọn tệp bộ ký tự phù hợp (ví dụ: characters_set_jp) trong phần Character File.

![Tạo phông chữ tiếng Nhật](https://github.com/user-attachments/assets/7bc98c77-9994-4551-8e5a-dae51eba9f45)

![Tạo phông chữ tiếng Hàn](https://github.com/user-attachments/assets/dc14fbbb-b38f-4f56-89b0-844d94b825cb)

![Tạo phông chữ tiếng Trung](https://github.com/user-attachments/assets/08020e00-14b1-47cd-a9f2-be3d4321ca48)

#### Tải Bản địa hóa bằng Hệ thống Addressable Assets

Để sử dụng tính năng này, hãy làm theo các bước sau:

- Cài đặt Hệ thống Addressable Assets.
- Thêm `ADDRESSABLES` vào danh sách chỉ thị trong Build Settings.
- Di chuyển thư mục Localizations ra khỏi thư mục Resources. Ngoài ra, di chuyển thư mục Đầu ra trong cửa sổ Cài đặt SheetX.
- Đặt thư mục Localizations làm Addressable Asset.

![Cài đặt SheetX](https://github.com/user-attachments/assets/ee17fdaa-c951-4f9c-8a6b-a5e2614db546)

![Thư mục Localizations](https://github.com/user-attachments/assets/1ecf2ae1-00e9-4c9f-9056-2867d04e8ee1)

![Cài đặt Build](https://github.com/user-attachments/assets/229da607-da10-4b87-b799-5d9549e5620d)