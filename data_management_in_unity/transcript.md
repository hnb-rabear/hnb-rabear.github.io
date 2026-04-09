---
layout: default
title: Transcript - Data Architecture trong Unity Game
---

# Kịch bản thuyết trình: Data Architecture trong Unity Game
**Thời lượng dự kiến:** 25-30 phút (Slides) + 10-15 phút (Live Demo)
**Phong cách:** Kỹ thuật, chuyên nghiệp, đi thẳng vào vấn đề thực tế.

---

## Slide 1: Cover (1 phút)
"Chào mọi người. Hôm nay chúng ta sẽ trao đổi về **Data Architecture trong Unity Game**. Mục tiêu của buổi này không phải là hướng dẫn code tính năng, mà là chia sẻ cách thiết kế một hệ thống quản lý dữ liệu — sao cho dễ bảo trì, dễ mở rộng, và giúp nhiều dev có thể làm việc chung mà không dẫm chân lên nhau."

---

## Slide 2: Bối cảnh (2 phút)
"Trước tiên, hãy nhìn vào thực tế. Một mobile game mid-core điển hình có bao nhiêu data?

Con số đầu tiên: **200+** config fields cần balance — item stats, level curves, rewards... Tiếp theo: **15+** player data models cần persist — profile, inventory, quests... Và quan trọng nhất: chỉ **2-3 devs** làm 5-7 features khác nhau, mà tất cả đều đọc/ghi vào cùng một data layer.

Vấn đề lớn nhất ở đây không nằm ở logic từng tính năng. Nó nằm ở **khả năng Scale và Bảo trì**. Thiếu kiến trúc chuẩn thì các tính năng dính chặt vào nhau, code dễ vỡ, state mất đồng bộ, và data layer dần biến thành một 'God Class' cản trở cả team."

---

## Slide 3: Quá trình tiến hóa (2 phút)
"Hầu hết dev đều trải qua 3 giai đoạn khi xây dựng hệ thống data.

Giai đoạn đầu — **Spaghetti Data**: PlayerPrefs tùy tiện, Set/GetInt rải khắp nơi, hardcode config. Side-effects khó lường — đổi 1 chỗ thì phát sinh lỗi dây chuyền.

Giai đoạn hai — **God Class**: Gom hết vào 1 Central SaveManager. Gọn hơn, nhưng file phình to thành God Class 3000 dòng, merge conflict liên tục.

Giai đoạn ba — **Decoupled**: Đây mới là đích đến. Layered System — tách Data, Logic, Persistence. Chia theo Domain. Giao tiếp qua Event.

Nhìn lại thì, Gen 1 sang 2 là quá trình **tập trung độ phức tạp**. Còn Gen 2 sang 3 là **chia nhỏ có hệ thống**."

---

## Slide 4: 3 nguyên tắc cốt lõi (2 phút)
"Mọi quyết định thiết kế trong hệ thống này đều quay về 3 điều.

Thứ nhất, **Separation of Concerns**: Data Model, Game Logic, và Persistence — ba thứ này phải tách rời. Data chỉ lưu trạng thái, Handler xử lý logic.

Thứ hai, **Single Point of Access**: Mọi lệnh ghi data bắt buộc phải đi qua Data Gateway để validate. Nếu 5 features đều có thể tự trừ coins thì code sẽ rối, khó bảo trì.

Thứ ba, **Event-Driven**: Các module không nhìn thấy nhau. Khi logic đổi state thì phát Event. Thêm tính năng chỉ cần Subscribe, xóa thì Unsubscribe — Plug & Play."

---

## Slide 5: Kiến trúc tổng quan (3 phút)
"Đây là thiết kế tổng thể. Hệ thống chia làm 2 luồng data chạy song song.

Bên trái là **luồng Static Config — Read-Only**. Designer quản lý balance và content trên Google Sheets hoặc Excel. Export Tool — cụ thể là SheetX — tự động sinh ra JSON Data cùng C# IDs và Constants, tất cả đều type-safe. Sau đó deserialize vào ConfigCollection — một ScriptableObject Singleton chứa toàn bộ config.

Bên phải là **luồng Dynamic Player — Read/Write**. Trên cùng là DataManager, một MonoBehaviour đóng vai trò Unity lifecycle driver — tự động save khi app pause hoặc quit. DataManager điều khiển DataHandlerCollection — đây là một ScriptableObject tập hợp tất cả Data Handlers. Mỗi DataHandler bọc lấy một DataModel: Handler chứa logic và events, còn DataModel chỉ là POCO serializable. Bên dưới là LocalDB — chuyển đổi JSON qua lại với PlayerPrefs.

Điểm quan trọng nhất: **luồng thay đổi data luôn đi 1 chiều**. Game Logic gọi Handler, Handler validate rồi ghi vào Data Model, sau đó phát Event, và các module liên quan như UI, Quest, Store tự cập nhật. Không ai được sửa Data Model trực tiếp — kể cả UI, kể cả các Handler khác."

---

## Slide 6: 4 Patterns sẽ tìm hiểu (1 phút)
"Để hiện thực hoá bức tranh trên, chúng ta sử dụng 4 patterns — mỗi cái giải quyết một vấn đề cụ thể.

**Config Pipeline** — tách bạch vòng lặp làm việc của Designer và Dev. **Data Gateway** — mọi thay đổi data phải qua Handler để validate và truy vết. **Lifecycle** — load xong thì làm gì? Offline rewards? Auto-save? Và **Observer** — thêm feature chỉ cần subscribe event, không đụng code cũ.

Chúng ta sẽ đi qua từng pattern."

---

## Slide 7: Pattern: Config Pipeline (2 phút)
"Pattern đầu tiên giúp loại bỏ dev ra khỏi vòng lặp balance.

Ý tưởng là: Config class phản chiếu chính xác cấu trúc trên Sheet. Ví dụ `ShopItemConfig` có id, name, price, currencyType. Khi truy xuất thì dùng ID được auto-generated — type-safe, compile-time checked. Gõ sai ID? Compile error ngay, không đợi đến lúc chạy game.

Vì sao pattern này quan trọng? Vì khi Designer muốn sửa giá trị, họ **tự làm** trên Sheet, không cần Dev. Khi Dev cần thêm field mới, chỉ **bổ sung field** trong config class. Và vì ID là auto-generated nên dev **không bao giờ phải hardcode string**.

Toàn bộ pipeline này được tự động hoá bởi **SheetX** — một Unity Editor tool xử lý từ Google Sheets hoặc Excel, xuất ra JSON, C# IDs, và ScriptableObject."

---

## Slide 8: Pattern: Data Gateway (2.5 phút)
"Pattern tiếp theo tách data structure ra khỏi business logic. Tôi sẽ lấy ví dụ từ JObjectDB System.

Có 2 layer. **Layer 1 là Data Model** — chỉ chứa data thuần. `PlayerData` kế thừa `JObjectData`, gồm các property như level, coins, lives, livesRegenTime, purchasedIapIds, noAds. Không có logic, không có method xử lý — chỉ là data.

**Layer 2 là Data Handler** — đây mới là nơi chứa logic. `PlayerModel` kế thừa `JObjectModel<PlayerData>`, có các phương thức như `SetCurrency`. Khi gọi method này, nó validate dữ liệu, gán giá trị, rồi lập tức phát Event `CurrencyChangedEvent`.

Tại sao phải tách? Vì Data Model cần serialize — gửi JSON lên server, lưu local. Còn Data Handler đóng vai trò Data Gateway — validate, notify, track dirty. Nhờ vậy mà đảm bảo được khả năng truy vết: bất cứ ai muốn sửa data đều phải đi qua Data Gateway."

---

## Slide 9: Pattern: Lifecycle Management (2.5 phút)
"Data có vòng đời phức tạp hơn nhiều so với chỉ load rồi save. Hệ thống cung cấp 5 callback chuẩn.

Đầu tiên là `Init()` — gọi lần đầu khi user mới, set default values. Tiếp theo `OnPostLoad` — nhận vào utcNow và offlineSecs, dùng để tính offline rewards, check expired. Rồi `OnUpdate` — gọi mỗi frame cho countdown timers, regen. Khi app vào background thì `OnPause` kích hoạt `OnPreSave` — validate rồi auto-save. Và cuối cùng `OnRemoteConfigFetched` — khi remote config thay đổi thì cập nhật data theo config mới.

Ví dụ thực tế: tính năng hồi thể lực khi offline. Trong hàm `OnPostLoad`, code gọi `ValidateAvatars()`, `ValidateLiveModifiers()`, rồi kiểm tra nếu `offlineSeconds > 0` thì chạy `ProcessOfflineRegen()`. Framework gọi đúng callback, đúng thời điểm — developer chỉ cần tập trung viết business logic bên trong."

---

## Slide 10: Pattern: Observer (2 phút)
"Quay lại nguyên tắc Event-Driven. Slide này so sánh 2 cách tiếp cận.

Cách sai trước: hàm `SpendCoin` gọi thẳng `hudView.UpdateCoin()`, `shopView.Refresh()`, `questMgr.CheckProgress()`... Cứ mỗi feature mới là thêm một dependency. 10 features thì 10 dòng gọi trực tiếp. Mỗi lần thêm feature mới lại phải mở PlayerModel ra sửa.

Cách đúng: hàm `SetCurrency` chỉ validate, assign, rồi gọi `DispatchEvent(new CurrencyChangedEvent(...))`. UI tự subscribe, Quest cũng tự subscribe. PlayerModel chỉ phát event, không biết và không cần biết ai đang lắng nghe.

Kinh nghiệm thực tế: với code dính chặt, thêm feature mới buộc phải sửa các module không liên quan. Với event-driven thì ngược lại — **các module cũ hoàn toàn không cần biết feature mới tồn tại.**"

---

## Slide 11: Scaling khi project lớn lên (2 phút)
"Khi tính năng bùng nổ, làm sao giữ data layer không biến thành 'big ball of mud'? Có 3 kỹ thuật.

Đầu tiên là **partial class**. `PlayerData` được chẻ thành 4 file: `PlayerData.cs`, `PlayerData.Inventory.cs`, `PlayerData.Avatar.cs`, `PlayerData.Misc.cs`. Vẫn là 1 class duy nhất, nhưng 4 file riêng biệt — mỗi dev làm 1 file, không ai đụng vào file của người khác.

Tiếp theo là **module boundaries**. Không nhét hết vào Player — chia rõ thành `PlayerModel`, `RewardModel`, `QuestModel`, `CompetitionModel`, `StoreModel`... Thực tế trong dự án là **7 models** trong SaveDataCollection.

Và cuối cùng là **cross-model access**. Các handler có tham chiếu đến nhau qua Inspector — ví dụ PlayerModel giữ reference đến CompetitionModel. Nguyên tắc là: **đọc trực tiếp thì OK, nhưng muốn sửa thì bắt buộc đi qua event.**"

---

## Slide 12: Anti-patterns phổ biến (4 phút)
*Chú ý: Click qua từng phần (5 slides con), nhấn mạnh lỗi thường gặp nhất trong dự án thực.*

"Bây giờ cùng nhìn vào 5 anti-patterns từ production thực tế. Đây không chỉ là lỗi của junior — dev lâu năm code ẩu cũng mắc.

**Lỗi thứ nhất: Trộn runtime state với persistent state.** Ví dụ đưa `tweenProgress`, `isDragging` vào class `PlayerData`. Sai hoàn toàn. Runtime state phải gắn trên MonoBehaviour — nó chỉ tồn tại trong scene. Persistent data phải hoàn toàn sạch, chỉ chứa những gì cần lưu.

**Lỗi thứ hai: Fat Data Model — God Object.** Bỏ qua partial class, để nguyên 1 file `PlayerData.cs` 500+ dòng gom hết coins, level, lives, unlockedAvatars, boosters, questProgress, raceRank... 2 devs cùng sửa file này là conflict ngay. Giải pháp đơn giản: dùng partial class chia theo domain.

**Lỗi thứ ba: Config data bị sửa lúc runtime.** Sửa thẳng config từ SheetX để buff chỉ số — `hero.attack += buffValue`. Nghe có vẻ nhanh, nhưng config sẽ sai vĩnh viễn trong session đó. Config là Read-Only, muốn buff thì dùng runtime modifier: `finalAtk = baseAtk + GetBuffTotal()`.

**Lỗi thứ tư: Cross-model mutation — bypass Data Gateway.** StoreModel chọc thẳng `player.data.coins -= item.price`, bỏ qua PlayerModel validation hoàn toàn. Như vậy thì mất hết validate, mất event, mất tracking. Phải gọi qua methods của PlayerModel.

**Lỗi thứ năm: ScriptableObject gọi MonoBehaviour.** Trong PlayerModel — một ScriptableObject — gọi `FindObjectOfType<HUDView>()` rồi `hud.UpdateCoin()`. Vấn đề là khi scene unload, HUDView bị huỷ nhưng ScriptableObject vẫn tồn tại — kết quả là null reference, crash game. Cách đúng: phát event, MonoBehaviour tự lắng nghe và tự unsubscribe khi scene unload."

---

## Slide 13: Tại sao kiến trúc này hiệu quả? (1.5 phút)
"Vậy kiến trúc này mang lại gì?

Thứ nhất, **Single Source of Truth** — mỗi data chỉ tồn tại đúng 1 nơi. Không còn cảnh `PlayerPrefs.GetInt()` rải khắp codebase mà không biết đâu mới là giá trị đúng.

Thứ hai, **Predictable Data Flow** — mọi thay đổi đều đi qua Handler. Nghĩa là lúc nào cũng trả lời được câu hỏi: ai đã sửa data này? Đặt 1 breakpoint ở Handler là trace được ngay.

Thứ ba, **các tính năng không dính chặt vào nhau**. Thêm feature mới chỉ cần subscribe event — các module cũ hoàn toàn không bị ảnh hưởng.

Thứ tư, **giảm lỗi do con người**. Auto-save, lifecycle callback, validation — framework lo hết, dev không cần nhớ phải gọi save hay check null.

Thứ năm, **team làm việc song song** được. Partial class + module boundaries — mỗi dev một file, gần như không có merge conflict.

Và cuối cùng, **thay đổi không sợ vỡ**. Muốn đổi cách lưu từ JSON sang encrypt hay cloud? Chỉ sửa persistence layer, toàn bộ game logic phía trên không đổi một dòng."

---

## Slide 14: Demo: Dự án thực tế (1 phút)
"Lý thuyết thì luôn hay, nhưng thực tiễn ra sao? Bây giờ chúng ta sẽ áp dụng kiến trúc vừa trình bày vào một mobile game thật.

Tôi sẽ chạy qua 4 bước. Đầu tiên là **Google Sheets** — xem Designer quản lý config và balance trực tiếp như thế nào. Tiếp theo là **SheetX** — export ra JSON, C# IDs, và ScriptableObject. Sau đó vào **Unity Project** — xem Data Models, DBManager, và Inspector. Cuối cùng là **Runtime Debug** — dùng JObjectDB để xem và sửa player data trực tiếp khi game đang chạy."

*(Chuyển sang Unity để Demo)*

---

## Slide 15: Takeaways & Q&A (1 phút)
"Trước khi kết thúc, tóm tắt lại 3 nhóm chính.

Về **nguyên tắc**: Separation of Concerns, Single Point of Access, và Event-Driven — ba cái này là nền tảng cho mọi quyết định thiết kế.

Về **patterns**: Config Pipeline đưa sheets thành ScriptableObject. Data Gateway tách data khỏi logic. Lifecycle callback lo auto-save. Observer giúp các module không dính chặt.

Về **scaling**: Partial class để tránh God Object. Module boundaries rõ ràng. Cross-model thì đọc trực tiếp OK, sửa thì phải qua event.

Và bức tranh lớn hơn: toàn bộ Data Management này thực ra là nền tảng của kiến trúc **MVP — Model-View-Presenter**. View chỉ hiển thị, Presenter điều phối logic, và cả hai đều **không sửa data trực tiếp** — luôn phải đi qua Data Handler rồi mới đến Data Model.

Cảm ơn mọi người đã lắng nghe. Mọi người có câu hỏi gì không?"
