---
layout: default
title: Transcript - Data Architecture trong Unity Game
---

# Kịch bản thuyết trình: Data Architecture trong Unity Game
**Thời lượng dự kiến:** 25-30 phút (Slides) + 10-15 phút (Live Demo)
**Phong cách:** Kỹ thuật, chuyên nghiệp, đi thẳng vào vấn đề thực tế.

---

## Slide 1: Cover (1 phút)
"Chào mọi người. Hôm nay chúng ta sẽ trao đổi về **Data Architecture trong Unity Game**. Mục tiêu của buổi này không phải là hướng dẫn code tính năng, mà là chia sẻ cách thiết kế một hệ thống quản lý dữ liệu sao cho dễ bảo trì, dễ scale, và giúp nhiều dev có thể làm việc chung mà không dẫm chân lên nhau."

---

## Slide 2: Bối cảnh vấn đề (2 phút)
"Hãy nhìn vào thực tế của một dự án game mid-core:
- Chúng ta có hàng trăm trường data config cần cân bằng.
- Hàng chục model dữ liệu người chơi cần lưu trữ. 
- Và quan trọng nhất: một team 2-3 dev, ai cũng cần đọc/ghi vào cùng một data layer.

Vấn đề lớn nhất ở đây không nằm ở logic từng tính năng, mà là ở khả năng Scale (mở rộng). Nếu thiếu kiến trúc chuẩn, các module sẽ dính chặt vào nhau, code dễ vỡ, state mất đồng bộ và hệ thống Data dần biến thành một 'God Class' nguyên khối gây cản trở toàn team."

---

## Slide 3: Quá trình tiến hóa (2 phút)
"Đa số chúng ta trải qua 3 giai đoạn xây dựng hệ thống data:
- **Gen 1 - Spaghetti:** Lưu PlayerPrefs rải rác và Hard code. Ship nhanh nhưng khó kiểm soát rủi ro và khó scale. Phải sửa nhiều chỗ cho một thay đổi nhỏ, hoặc sửa 1 chỗ hỏng 3 chỗ khác.
- **Gen 2 - God Class:** Gom tất cả vào 1 file `SaveManager`. Gọn hơn về quy trình save, nhưng file phình to hàng ngàn dòng, dễ gây merge conflict.
- **Gen 3 - Decoupled:** Đây là đích đến. Chia tách lớp Data ra khỏi Logic và lớp Persist. Sử dụng Event để giao tiếp.

Nhìn chung, tiến hóa từ Gen 1 sang 2 là quá trình **tập trung hóa sự phức tạp**. Từ Gen 2 sang 3 là quá trình **chia nhỏ sự phức tạp một cách có hệ thống**."

---

## Slide 4: 3 nguyên tắc cốt lõi (2 phút)
"Mọi quyết định thiết kế từ giờ trở đi đều bám vào 3 nguyên tắc:
1. **Separation of Concerns:** Data Model và Game Logic phải tách rời. Model chỉ chứa data thuần, Handler chứa logic xử lý data đó.
2. **Single Point of Access:** Mọi thao tác ghi (Write) bắt buộc phải qua lớp kiểm soát (Data Gateway). Không có chuyện tính năng A hay B tự trừ tiền trực tiếp. 
3. **Event-Driven:** Các tính năng không được trỏ thẳng vào nhau. Một module thay đổi data sẽ phát Event, module nào quan tâm thì tự lắng nghe."

---

## Slide 5: Kiến trúc tổng quan (3 phút)
"Đây là thiết kế tổng thể chia làm 2 luồng rõ rệt:
- **Luồng Static Config (Read-Only):** Designer cấu hình trên Google Sheets -> Tool tự sinh ra JSON và mã C# tương ứng. Chúng ta có hệ thống type-safe, không hardcode.
- **Luồng Dynamic Player (Read/Write):** DataManager điều khiển lifecycle của game, kết nối với chuỗi Handler (DataModel được quản lý bởi Handler).

Luồng chạy luôn là một chiều: Game logic gọi Handler để validate -> Event phát ra -> UI tự động cập nhật. Đảm bảo luôn thực thi theo luồng một chiều."

---

## Slide 6: 4 Patterns sẽ tìm hiểu (1 phút)
"Để hiện thực hoá bức tranh trên, hệ thống sử dụng 4 Patterns:
1. **Config Pipeline:** Tách vòng lặp làm việc của Designer ra khỏi Dev.
2. **Data Gateway:** Mọi thay đổi data phải qua Handler để validate và truy vết.
3. **Lifecycle:** Vòng đời dữ liệu — load, offline rewards, auto-save.
4. **Observer:** Thêm feature chỉ cần subscribe event, không đụng code cũ.
Chúng ta sẽ lướt nhanh từng pattern."

---

## Slide 7: Pattern: Config Pipeline (2 phút)
"Với Config Pipeline, chúng ta tách bạch hoàn toàn công việc của Dev và Designer. 
Config Class phản chiếu chính xác cấu trúc trên Sheet. Chúng ta truy xuất thông qua ID được auto-generated. Gõ sai ID? Lỗi ngay từ lúc biên dịch (Compile-time).

Quy trình này trong project hiện tại được tự động hoá hoàn toàn bởi công cụ **SheetX** - xử lý toàn bộ từ Google Sheets xuống JSON và ScriptableObject."

---

## Slide 8: Pattern: Data Gateway (2.5 phút)
"Pattern này áp dụng nguyên tắc Separation of Concerns.
- **Layer 1 (Data Model):** Ví dụ như `PlayerData`, chỉ gồm các property thuần tuý. Lớp này làm nhiệm vụ Serialize/Gửi lên server.
- **Layer 2 (Data Handler):** Ví dụ `PlayerModel`. Lớp này chứa các phương thức như `SetCurrency`. Nó validate dữ liệu, gán giá trị, và lập tức phát Event (`CurrencyChangedEvent`).

Tại sao phải chia 2? Để đảm bảo tính phân quyền và **Traceability (khả năng truy vết)**. Bất cứ ai muốn sửa Data đều phải bước qua 'người gác cổng' là Handler."

---

## Slide 9: Pattern: Lifecycle Management (2.5 phút)
"Vòng đời dữ liệu phức tạp hơn là chỉ Load rồi Save.
Hệ thống cung cấp một luồng chuẩn:
- `Init()` cho user mới.
- `OnPostLoad()` để tính toán logic bù đắp khi game vừa load xong (ví dụ offline regen).
- `OnUpdate()` cho các timer realtime.
- `OnPause()` dùng để validate và tự động lưu nền (auto-save).

Ví dụ thực tế khi tính năng hồi thể lực lúc offline: Framework sẽ gọi callback `OnPostLoad` truyền vào số giây offline. Developer chỉ cần tập trung viết business logic ở bên trong hàm, framework tự động gọi đúng thời điểm."

---

## Slide 10: Pattern: Observer (2 phút)
"Quay lại nguyên tắc Event-Driven. Ở phương pháp gọi trực tiếp, `SpendCoin` gọi thẳng đến HUD, Shop, Quest. Mỗi tính năng mới thêm vào, ta lại phải sửa hàm này. Sự liên kết quá chặt chẽ (tight-coupling).

Ở phương pháp Event-Driven, hàm `SetCurrency` chỉ phân phát `CurrencyChangedEvent`. HUD, Quest tự subscribe vào hệ thống Event. Lúc này, tính năng cũ hoàn toàn không cần biết tính năng mới có tồn tại hay không, xoá hay thêm module đều an toàn."

---

## Slide 11: Scaling khi project lớn lên (2 phút)
"Khi tính năng bùng nổ, làm sao ngăn Data Layer phình to thành God Class?
1. **Partial class:** `PlayerData` được chẻ ra thành `Inventory`, `Avatar`, `Misc`. Một class duy nhất nhưng dev làm độc lập, giảm thiểu tối đa rủi ro merge conflict.
2. **Module boundaries:** Phân chia rõ 6-7 cụm model (Reward, Quest, Shop...). Không nhét hết vào Player.
3. **Cross-model access:** Các handler có tham chiếu đến nhau qua Inspector để Đọc trực tiếp, nhưng Sửa thì bắt buộc qua Event."

---

## Slide 12: Anti-patterns phổ biến (4 phút)
*Chú ý: Click nhanh qua từng phần, nhấn mạnh lỗi thường gặp nhất trong dự án thực.*
"Cùng nhìn vào 5 anti-patterns từ các lỗi production thực tế:
1. Trộn trạng thái runtime (như tween animation, drag state) vào class serialize data. Đây là thiết kế sai cấu trúc. Runtime gắn trên MonoBehaviour, data persistent phải hoàn toàn sạch.
2. Bỏ qua partial class, để nguyên 1 file 500 dòng gây God Object. 
3. Sửa thẳng config lúc runtime để buff chỉ số. Config là Read-Only. Xin hãy dùng Runtime Modifier pattern.
4. Một tính năng ngầm chọc thẳng data của tính năng khác, bỏ qua Handler. Bỏ đi nguyên tắc Data Gateway.
5. Để ScriptableObject lưu giữ reference của MonoBehaviour. Chuyển scene, MonoBehaviour bị huỷ, ScriptableObject vẫn trỏ đến null -> Crash game."

---

## Slide 13: Tại sao kiến trúc này hiệu quả? (1.5 phút)
"Kiến trúc này giải quyết được những vấn đề thực tế:
- **Single Source of Truth:** Mỗi data chỉ tồn tại 1 nơi. Không còn PlayerPrefs.GetInt() rải rác khắp codebase.
- **Predictable Data Flow:** Mọi thay đổi đều qua Handler — dễ trace, dễ debug. Luôn trả lời được: ai đã sửa data này?
- **Các tính năng không dính chặt:** Thêm feature chỉ cần subscribe event. Các module cũ không cần biết feature mới tồn tại.
- **Giảm lỗi do con người:** Auto-save, lifecycle callback, validation — framework tự xử lý, dev không cần nhớ.
- **Team làm việc song song:** Partial class + module boundaries — mỗi dev một file, hạn chế tối đa merge conflict.
- **Thay đổi không sợ vỡ:** Đổi cách lưu (JSON → encrypt → cloud)? Chỉ sửa persistence layer, game logic không đổi."

---

## Slide 14: Demo Dự án (1 phút)
"Lý thuyết thì luôn hay, nhưng thực tiễn ra sao? 
Bây giờ chúng ta sẽ đi vào phần Demo live dự án thực.
Tôi sẽ chạy qua quy trình:
1. Chỉnh sửa Google Sheets.
2. Export config qua SheetX.
3. Xem C# IDs được generate ra.
4. Chạy game, sử dụng thanh runtime debug JObjectDB để inject data live."
*(Chuyển sang Unity để Demo)*

---

## Slide 15: Takeaways & Q&A (1 phút)
"Trước khi kết thúc, tóm tắt lại:
- Nhớ kỹ 3 nguyên tắc: Separation of Concerns, Single Point of Access, Event-Driven.
- Cả bức tranh này thực ra là đang xây dựng cho kiến trúc Model-View-Presenter (MVP). View (Hiển thị) và Presenter (Điều phối logic) tuyệt đối không tự chọc vào Data Model, mà phải qua Data Handler.

Cảm ơn mọi người đã lắng nghe. Mọi người có câu hỏi gì không?"
