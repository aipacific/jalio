## Những điều lưu ý

#### Module & Widget
- Sự khác biệt là ở chỗ Module đảm nhận đáp trực tiếp cho các đầu gọi API. Widget cung cấp thêm dữ liệu để hiển thị ở trên các màn hình.
- Nên giữ trong mindset như sau: Widget là thành phần cung cấp thêm tính năng cho các Module một cách linh hoạt và gọn gàng.
- Ví dụ màn hình chấm công của một nhân viên, tổng số giờ làm việc nên viết trong widget. Giả sử cần hiển thị số lần đi trễ, số lần về sớm ta cũng làm nó trong 1 widget khác.

#### Plugin làm gì?
- Plugin sử dụng với mục đích giúp cho việc mở rộng tính năng cho Module diễn ra một cách dễ dàng và linh hoạt.
- Khi xử lý trong Module, ta có các "sự kiện" liên quan đến quá trình xử lý sẽ diễn ra. Ví dụ thao tác người dùng thực hiện bấm vào nút "Check-In", sẽ có 1 công việc chính là lưu dữ liệu Check-In đó vào DB. Có nghĩa nó sẽ phát sinh sự kiện "On-Before-Save-Check-In" và "On-After-Save-Check-In".
- Người viết Module nên để sẵn vào đó 2 điểm mốc. Ở mỗi điểm mốc đó, sau này ta có thể viết các plugin sẽ chạy ở ngay đó để thực hiện các xử lý mở rộng. 

#### Phân luồng bằng Controller
- Việc các nhóm thao tác là hoàn toàn động. Tuy nhiên luồng (flow) đầu vào của xử lý thì ta có thể bố cục ngay từ khi viết code.
- Ví dụ, một ứng dụng cho phép reset-password. Có 2 tình huống xử lý cho việc này (1) user đã đăng nhập thì đổi mật khẩu không cần confirm qua OTP, (2) user chưa đăng nhập thì cần confirm qua OTP. Như vậy ta hình dung trong tình huống này có 2 luồng (flow) xử lý ứng với (1) - loại user đã đang nhập và (2) - loại user chưa đăng nhập.
- Như vậy, có thể bố cục mỗi luồng đi vào một Controller để linh hoạt trong việc xử lý và phân quyền.

#### Module, Plugin, Widget hoạt động kết hợp với Config động.
- Loại bỏ suy nghĩ Module, Plugin, Widget chỉ hoạt động một kiểu thì sẽ viết code linh hoạt hơn.
- Ví dụ, ta viết Module trả về danh sách các lịch hẹn. Như vậy đó có thể là lịch hẹn của 1 chi nhánh, lịch hẹn của vài 3 chi nhánh nào đó, hay lịch hẹn của tất cả các chi nhánh. Như vậy ta phải làm sao?
- Để trả lời câu hỏi trên, ta lấy một ví dụ đơn giản nhất như sau: Khánh khi đăng nhập thấy lịch hẹn chỉ của chi nhánh SNA, Hoàng khi đăng nhập thấy lịch hẹn của chi nhánh SNA và NDC.
- Trên ứng dụng phân quyền (JWT mà Huy đang ôm) ta tạo ra 2 Menu-Item cùng trỏ đến Module lịch hẹn, Menu-Item-1 ta Config tham số cho nó ID của SNA, Menu-Item-2 ta Config tham số cho nó ID của SNA và NDC.
- Gán cho Khánh thấy Menu-Item-1, Gán cho Hoàng thấy Menu-Item-2 (cùng có nhãn là "Danh sách lịch hẹn").
- Như vậy, khi Khánh click vào "Danh sách lịch hẹn" thì gọi tới Menu-Item-1, khi Hoàng click vào "Danh sách lịch hẹn" thì gọi tới Menu-Item-2.
- Đây là cách tiếp cận cơ bản cho việc Phân quyền động.

### Refactor lại cho chuẩn

#### Authen & Autho
- Loại bỏ việc Authen & Autho diễn ra ở cấp Application. Nên chuyển nó vào cấp Plugin (có nghĩa ta sẽ xây các plugin trigger ở sự kiện OnAuthen của Application).

#### MainPage cần sửa lại
- MainPage là thứ hơi sai sai mà server-side và client-side thời gian trước khi còn chưa có kinh nghiệm sáng tạo ra
- Sửa lại biến này có tên là _page (cùng cách quy ước với _fm, _renderer, _mod, _view..., những thứ gì thuộc về tham số điều khiển thì có tiền tố _). Jalio đã handle và xử lý biến này.
- Để đảm bảo phân quyền chạy đúng và chặt chẽ thì tất cả mọi API gọi lên server luôn phải gắn tham số _page
