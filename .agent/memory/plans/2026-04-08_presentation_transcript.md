# Transcript Thuyết Trình: Data Architecture trong Unity Game
**Thời lượng dự kiến:** 30 - 45 Phút
**Giọng điệu:** Chia sẻ kinh nghiệm thực chiến, chuyên nghiệp, gãy gọn thẳng vào vấn đề kỹ thuật.

---

## 🟢 PHASE 1: VẤN ĐỀ VÀ SỰ TIẾN HÓA (Slides 1-3)

### Slide 1: Cover (1 phút) 🎤
"Chào mọi người. Hôm nay mình sẽ chia sẻ về một chủ đề mà có lẽ dev Unity nào cũng từng phải 'đau đầu' — Data Architecture trong Unity.
Đã bao giờ mọi người ngồi fix một con bug, mà nguyên nhân là do không biết 'ông nào' vừa trừ coin của user chưa? Hoặc có khi nào phải deal với cái save file gây ra merge conflict dài hàng kilobyte chưa? Bài talk hôm nay không mang tính lý thuyết sáo rỗng, mà nó là những bài học xương máu đúc kết được để giúp team scale dự án một cách mượt mà và ít 'đau thương' nhất có thể."

### Slide 2: Bối cảnh (2 phút) 🎤
"Để thấy vì sao chúng ta cần architecture, hãy nhìn vào một game mobile mid-core tiêu chuẩn. Ở mặt Static Config, ta có thể dễ dàng chạm mốc hơn 200 fields khác nhau — từ chỉ số item, đường cong EXP, đến bảng loot rate. Ở mặt Dynamic Data, ta phải lưu trữ và quản lý persistent state cho hàng chục model: profile, inventory, quest, timer, v.v.
Nhưng cái khó không nằm ở data size, Unity dư sức handle. **Sự phức tạp thực sự nằm ở con người.**
Khi bạn có 2-3 devs cùng làm 5-7 feature chồng chéo lên nhau, và tất cả đều 'thò tay' chọc trực tiếp vào một data layer vô tổ chức... kết quả là các tính năng sinh ra side-effects khó lường. Code dính chặt lấy nhau, state mất đồng bộ giữa UI và Game. Cuối cùng, Data Layer phình ra thành một mớ rùng beng cản trở tốc độ làm việc chung của team. Đó là lý do ta phải làm lại kiến trúc."

### Slide 3: Quá trình tiến hóa (3 phút) 🎤
"Vậy thì đa số anh em Unity tiến hóa qua bao nhiêu gen để đến được một kiến trúc chuẩn? Thường là 3 giai đoạn.
* **Gen 1 - Spaghetti Data:** Anh em nào mới code cũng từng ôm cái `PlayerPrefs.GetInt/SetInt` đi rải khắp project. Config thì hardcode luôn trong Unity Inspector. Hậu quả? Sửa 1 lỗi sinh ra 5 lỗi dây chuyền.
* **Gen 2 - God Class:** Rút kinh nghiệm, chúng ta tạo ra một cái `SaveManager` Singleton khổng lồ. Mọi thứ gom tóm về một chỗ. Code gọn hơn thật, nhưng class đó có thể dài 3,000 dòng. Cứ 2 dev cùng sửa tính năng, y như rằng có merge conflict.
* **Gen 3 - Decoupled Layered System:** Cuối cùng, để dự án sống được qua năm tháng, ta phải tiến lên Gen 3. Tách biệt rõ ràng Data (thứ để lưu), Logic (thứ kiểm tra đúng sai) và Persistence (cách lưu). Không gom rác về một chỗ nữa, mà là **tập trung độ phức tạp** một cách có hệ thống."

---

## 🟢 PHASE 2: BỨC TRANH LỚN (Slides 4-6)

### Slide 4: 3 nguyên tắc cốt lõi (3 phút) 🎤
"Và để build được Gen 3 đó, mình luôn bám sát 3 core principles này:
1. **Separation of Concerns:** Quy tắc ngầm là không trộn lẫn logic. Data model thuần túy chỉ là POCO để deserialize. Game logic phải đưa ra ngoài Handler.
2. **Single Point of Access:** Quy tắc 'Data Gateway'. Ví dụ trong game, nếu 5 features cùng có quyền tự ý móc túi trừ coin của player, logic sẽ ngay lập tức rối tung rối mù. Cần phải có duy nhất một cái cổng kiểm soát để validate trước khi cho phép dữ liệu bị thay đổi.
3. **Event-Driven:** Các module không gọi trực tiếp lẫn nhau. Mình cập nhật thay đổi thông qua Event. Rất Plug & Play: Feature mới vào thì thêm hàm Subscribe, bỏ Feature đi thì Unsubscribe, không break logic cũ."

### Slide 5: Kiến trúc tổng quan (3 phút) 🎤
"Nhìn vào luồng này, hệ thống Data chạy song song 2 luồng ống nước rõ rệt:
* Ở ống màu **xanh dương (Static Config - Read Only):** Nguồn duy nhất là Google Sheets/Excel của Designer. Nó đi qua Export Tool đẻ ra auto-generated code và JSON, cuối cùng nạp vào Unity ở dạng ScriptableObject. Mọi thứ là Type-safe và tuyệt nhiên không có ai sửa config lúc runtime (Read-only).
* Ở ống màu **tím (Dynamic Player - Read/Write):** Ta có một chiếc `DataManager` gắn ở MonoBehaviour để cắm vào Unity Lifecycle (bắt sự kiện Pause, Quit để trigger auto-save). DataManager này nắm bộ não là `DataHandlerCollection` (SO) đứng trên cùng. Bên trong bộ não này chứa các Handler kiểm soát mọi logic. Các Handler sẽ bọc lấy các DataModel lõi bên trong.
* **Insight lớn nhất:** Flow ở đây là *Một Chiều*. Logic ➞ Gọi Handler ép kiểu/Validate ➞ Phát Event  ➞  UI/System tự động Subscribe để render. Khẳng định: Không một ai được chọc tay vào Data trực tiếp!"

### Slide 6: 4 Patterns sẽ tìm hiểu (1 phút) 🎤
"Dựa trên kiến trúc đó, chúng ta sẽ mổ xẻ 4 Pattern mạnh nhất để giải quyết các constraint trong dự án thực tế: Config Pipeline, Data Gateway, Lifecycle và Observer."

*(Ghi chú: Nếu bạn muốn demo sớm ở Slide này, bạn có thể nói: "Trước khi đi vào đi deep-dive vào mặt Code, mình sẽ demo nhanh trên Unity và tool SheetX để mọi người hình dung mặt mũi luồng chạy này ra sao...". Tiếp tục quay màn hình Unity).*

---

## 🟢 PHASE 3: HIỂU SÂU VỀ DESIGN PATTERN (Slides 7-10)

### Slide 7: Pattern: Config Pipeline (3 phút) 🎤
"Pattern đầu tiên: Config Pipeline. Bài toán ở đây là làm sao tách bạch hoàn toàn vòng lặp làm việc của Designer và Dev.
Mọi người nhìn vào code — mình định nghĩa một schema tương đồng hoàn toàn với cái bảng Google Sheets. 
Lợi ích ở đây quá rõ: Khi config có thay đổi (chỉnh giá, tăng máu quái), Designer hoàn toàn làm chủ cục Data đó, xuất ra JSON, nạp vô build, Dev **không cần tham gia** vào nhịp độ balance. Dev chỉ viết thêm biến mới rỗng nếu tính năng mới yêu cầu thêm trường dữ liệu thôi. Type-safety đảm bảo rằng gõ sai kiểu dữ liệu, gõ sai Enum (ví dụ `IDs.Currency`) thì lỗi lúc Compile ngay lập tức, không lọt được xuống Runtime."

### Slide 8: Pattern: Repository (3 phút) 🎤
"Tiếp theo, Repository hay Data Gateway. Tại sao ta phải mệt mỏi tách 2 file cho cùng 1 Data thay vì viết gộp cho lẹ?
Mọi người nhìn Layer 1: Chỉ chứa pure data class. Không có bất kỳ behavior hay hàm logic nào. Cái này cực kì nhạy việc Serialize JSON và bắn lên Server (nếu có làm backend).
Nhưng ở Layer 2, cái Handler sinh ra chức năng bảo kê: Nhận Input, kiểm tra (có đủ tiền không? ID hợp lệ không?), rồi sau đó mới thực thi đè data lên Layer 1, và quan trọng nhất: **Gào lên (phát event) cho cả thế giới biết là Tiền vừa bị thay đổi.**
Điều này tạo ra tính **Traceability (Truy vết)**. Mai mốt QA báo lỗi "tự nhiên bật panel lên mất tiêu tiền", mình search nhẹ hàm `SetCurrency` của đúng Data Gateway này để đặt Breakpoint là tóm cổ ngay thủ phạm gọi bậy."

### Slide 9: Pattern: Lifecycle (2 phút) 🎤
"Data lưu trữ không chỉ gọi hàm Save/Load là xong, nó có cả một Lifecycle giống hệt Unity LifeCycle vậy.
Trong game mid/hard-core, chúng ta cực kì hay gặp task 'Tính toán số năng lượng/tài nguyên phục hồi được trong thời gian player Offline'. Nếu không mài xẻ nó đàng hoàng, logic Offline Reward sẽ bay bay ngẫu nhiên ở một script UI nào đấy.
Bằng cách xây hook `OnPostLoad()`, khi Data vừa deserialize xong, Framework tự tính offline seconds và chích vào model. Logic offline regen được xử lý khép kín bên trong, khi game render frame đầu tiên (Awake/Start), Data đã là dữ liệu mới tinh chuẩn 100%."

### Slide 10: Pattern: Observer (3 phút) 🎤
"Chắc chắn ai cũng đã từng viết mẩu code của cái khung ĐỎ bên tay trái rồi. Đúng không ạ?
`SpendCoin` xong, dev tự tay lôi `HUD.UpdateCoin`, lôi `Shop.Refresh`, móc luôn cái `Quest.Check()` vào chạy.
Nhìn có vẻ dễ debug, nhưng khi bạn nhét tính năng thứ 10 vào game, ví dụ 'Hệ thống Thành Tựu' cũng cần track lúc player tiêu Coin. Bạn lại mò vào đít hàm này gõ tiếp `Achievement.Update()`.
Điều này khiến file PlayerModel bị ép buộc phải ôm reference (depend) vào HUD, Shop, Quest... Cái PlayerModel không còn reusuable được nữa.
Với Event-driven bên khung XANH, ông PlayerModel chỉ làm đúng chuyện của mình: Sửa data -> Rút một cái loa thông báo (DispatchEvent). Phía HUD, Shop, Quest... hãy tự Awake tự subscribe vào mà update. File PlayerModel hoàn toàn 'mù' về hệ thống UI sinh ra để phục vụ nó."

---

## 🟢 PHASE 4: TỪ LÝ THUYẾT ĐẾN THỰC CHIẾN (Slides 11-13)

### Slide 11: Scaling: Khi project phình to (3 phút) 🎤
"Okay, giờ nhìn qua các dự án lớn. Mọi model rồi sẽ bành trướng. 
Để chặn cái này, mình dùng `partial class`. Cái lõi lưu profile nằm ở `PlayerData.cs`. Quần áo skin nằm file `PlayerData.Avatar.cs`, túi đồ nằm ở `Inventory.cs`. Cùng 1 class data, nhưng rải ra 4 file. Team 4 thằng chia nhau 4 file, push code Git 1 nhịp - 0 merge conflicts.
Tiếp đến, boundary. Nên tách khoanh vùng những file không liên quan nhau. Competition Model khác Data Model. Tách biệt chúng. Truy xuất thì đọc tự do, nhưng muốn chọc sửa Data chéo thì thông qua Event Gateway hết."

### Slide 12: Anti-patterns (4 phút) 🎤
*(Lần lượt click qua 5 lỗi, phân tích trực diện trên slide)*
"Slide này chống chỉ định cho những tâm hồn mong manh vì đây rặt những đoạn code thực tế lúc dự án lên production, nơi mà kiến trúc bị phá vỡ vì OT và deadline:
1. **Trộn runtime state với persistent state:** Rất nhiều bạn lấy DataModel để gán `tweenProgress` hay check `isDragging`. Model này nó save xuống File đĩa cứng đấy các bạn! Save luôn tình trạng đang bấm nút vào file thì load lại game sẽ bị treo luôn state UI cũ.
2. **Fat Data Model:** File 500+ dòng nhồi nhét, ai đụng cũng bể đầu. Hãy Partial class.
3. **Mutate Config Data:** Một bug cực phổ biến: anh em gọi lấy Config của Con bài/Hero lúc đánh nhau, xong buff damage tự cắn trực tiếp vào `hero.attack += buff`. Từ đó đến khi tắt game, cái config gốc (lẽ ra rỗng tuếch, ReadOnly) bị thay đổi vĩnh viễn trong RAM. Hãy sinh một model runtime (Modifier) để bọc cái base attack lại.
4. **Mutate Cross-Model:** Đang code trong Shop Item. Mua đồ xong lách luôn `player.data.coins -= price` (Bypass Gateway). 0 Point access. Khi QA báo lỗi không render được tiền, vỡ mặt. Hãy gọi qua Player Model Gateway đàng hoàng.
5. **Dùng ScriptableObject đâm chọc MonoBehaviour:** Scriptable Object nó thọ hơn cả cái Scene trong Unity. Gọi đi tìm một cái HUDView xong chọc vào nó update UI. Đổi scene nó un-load mất HUD View cái là ăn NullReference Exception ngay. Pattern chuẩn ở đây là Event-driven."

### Slide 13: Benefits (2 phút) 🎤
"Sự đầu tư vất vả về mặt architecture mang lại 6 lợi ích hiển hiện: Single Source of Truth, Data Flow rõ rệt (dễ truy vết bugs). Các features tách rời độc lập (Thêm/xóa tính năng dễ ợt). Hệ thống Lifecycle chặn đứng lỗi logic thời gian của con người. Team chạy Agile, dev song song mà không dẫm đạp code của nhau. 
Cuối cùng: Mai mốt thay lưu bằng JSON sang lưu Cloud Firebase, không sửa 1 dòng game logic, chỉ thay đúng cục DB DBManager là xong."

---

## 🟢 PHASE 5: TỔNG KẾT VÀ Q&A (Slides 14-15)

### Slide 14: Demo Dự Án Thực Tế (5 phút) 🎤
*(Nếu bạn chưa Demo ở Slide 6, đây là thời điểm tuyệt vời).*
"Lý thuyết thì hay, giờ chúng ta đi vào thực tế một chút chạy xem hệ thống Tool DataSheet bằng Google Sheets nó export như nào.
*Mở Unity: Demo tạo sheet mới / thêm cột ➞ Chạy SheetX Export ➞ Kiểm tra auto-generated Class và ID ➞ Bật play mode bật JObjectDB Inspector thay đổi coin xem UI có update theo event hay không.*" 

### Slide 15: Takeaways (1 phút) 🎤
"Chút Takeaways chốt lại mang về...
Chốt hạ nhé: Thật ra hệ thống Data Management này là bộ móng nhà của tư tưởng kiến trúc kinh điển **MVP (Model-View-Presenter)**.
`View` gửi interaction lên `Presenter`. `Presenter` quăng logic và yêu cầu xuống ngõ gác cổng `Data Handler`. Sau khi `Handler` validate xong sẽ lẳng lặng đè data xuống `Data Model`. Tới đây, `Handler` rú còi báo Event. `Presenter` / `View` nghe thấy réo rắt bèn chui vào Handler Get dữ liệu và chạy UI update.

1 Vòng luân hồi khép kín cực kỳ chặt chẽ tạo ra một dự án ổn định vững vàng.
Cảm ơn mọi người. Ai có câu hỏi gì không?"
