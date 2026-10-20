ỨNG DỤNG QUẢN LÝ BÁN HÀNG

Cài đặt web app: đưa index.html, manifest.webmanifest, sw.js lên hosting HTTPS (GitHub Pages dùng được).

LẬP HÓA ĐƠN
- Tên khách hàng nhập tự do, không bắt buộc phải tạo khách hàng trước.
- Chọn sản phẩm, chỉnh số lượng/đơn giá, thêm nhiều dòng rồi Lưu hóa đơn.
- Đơn vị sản phẩm hỗ trợ bịch và xâu. Hóa đơn cũ giữ lại thông tin mặt hàng đã bán.

KẾT NỐI GOOGLE SHEETS
1. Tạo Google Sheet mới thuộc tài khoản của anh.
2. Trong Sheet chọn Extensions (Tiện ích mở rộng) > Apps Script.
3. Xóa nội dung mặc định, dán toàn bộ mã trong file Code.gs, nhấn Save.
4. Deploy > New deployment > loại Web app.
5. Execute as: Me. Who has access: Anyone (chỉ dùng cho dữ liệu cửa hàng riêng, URL triển khai cần được giữ kín). Nhấn Deploy và cấp quyền truy cập.
6. Sao chép URL kết thúc bằng /exec.
7. Mở ứng dụng > Cài đặt > Google Sheets, dán URL > Lưu URL Google Sheets > Gửi dữ liệu ngay.
8. Kiểm tra các sheet Products, Customers, Orders, OrderItems, Settings trong Google Sheet.

Mỗi lần ứng dụng lưu dữ liệu, app gửi snapshot sản phẩm/khách hàng/hóa đơn tới Apps Script. Do trình duyệt không đọc được phản hồi của yêu cầu no-cors, thông báo gửi đi không đảm bảo server đã ghi thành công; cần kiểm tra trực tiếp Google Sheets sau lần cấu hình đầu. Endpoint web app là bí mật truy cập, không đăng công khai. Với dữ liệu nhạy cảm hoặc nhiều nhân viên, cần bổ sung xác thực phía máy chủ trước khi sử dụng chính thức.

Google Sheets là bản lưu/nhận dữ liệu từ app; hiện chưa tải dữ liệu thay đổi trực tiếp từ Sheet về app tự động và chưa đồng bộ thời gian thực nhiều thiết bị. Sao lưu JSON thường xuyên.

Cập nhật: Danh mục sản phẩm đã được nạp theo danh sách người dùng cung cấp (29 sản phẩm, giá mặc định 0 để người dùng nhập giá). Đã ẩn mục tồn kho và bỏ kiểm tra/trừ tồn khi bán. Lịch sử hóa đơn có thao tác sửa, hủy và xóa.


THIẾT LẬP FIREBASE ĐÃ ĐIỀN SẴN CẤU HÌNH DỰ ÁN
- Project ID: banhang-a3347; Auth domain: banhang-a3347.firebaseapp.com; Firestore location: asia-southeast1.
- Tài khoản quản lý dự kiến: loi0933451652@gmail.com.
- Trong Firebase Console > Firestore Database > Rules, có thể dùng nội dung file firestore.rules sau khi kiểm tra email chính xác rồi nhấn Publish. Quy tắc hiện tại chỉ cho tài khoản quản lý đọc/ghi tài liệu dữ liệu dùng chung; nhân viên chưa được cấp quyền truy cập.
- Quan trọng: bản giao diện hiện tại mới có thao tác đăng nhập Google và đồng bộ thủ công một tài liệu; chưa triển khai mô hình phân quyền nhân viên riêng, đồng bộ realtime an toàn nhiều nhân viên, hay tự chuyển ngày. Không sử dụng như hệ thống đa nhân viên chính thức cho đến khi hoàn tất phần đó. Không đặt Firestore thành public và không dùng allow read, write: if true.

CẬP NHẬT FIREBASE REALTIME (BẢN HIỆN TẠI)
- Đã thêm listener Firestore onSnapshot và tự gửi thay đổi sau khi lưu; các thiết bị đăng nhập cùng tài khoản quản lý sẽ nhận cập nhật realtime.
- Tài khoản quản lý duy nhất được phép trong Rules: loi0933451652@gmail.com. Đăng nhập bằng Google trong Cài đặt > Tài khoản Google & đồng bộ nhiều thiết bị.
- Trước khi dùng, hãy vào Firebase Console > Authentication > Settings > Authorized domains và thêm đúng tên miền đang host web (ví dụ ngocngan405.github.io nếu vẫn dùng GitHub Pages). Google provider phải bật.
- Firestore Rules phải được publish từ file firestore.rules. Không đổi sang allow read, write: if true.
- Lần đầu đăng nhập, nếu có dữ liệu cũ trên thiết bị, dùng nút “Đồng bộ ngay” và chọn đúng hướng: tải dữ liệu cloud xuống hoặc tải dữ liệu thiết bị lên. Sao lưu JSON trước khi ghi đè dữ liệu.

GIỚI HẠN CẦN BIẾT
- Bản realtime này đồng bộ tài liệu cửa hàng dùng chung khi các thiết bị đăng nhập bằng tài khoản quản lý. Nó CHƯA triển khai mô hình tài khoản nhân viên riêng, bảng riêng cho từng nhân viên, hay quyền quản lý tổng hợp an toàn giữa các nhân viên. Không dùng tài khoản quản lý chung cho nhân viên.
- Để hoàn thiện đúng yêu cầu nhiều nhân viên, cần thêm danh tính/tài khoản từng nhân viên và phân quyền Firestore theo UID; không thể an toàn chỉ bằng mã nhân viên lưu ở trình duyệt.

BẢN NÂNG CẤP GIAO DIỆN (2026-10-20)
- Báo cáo có nút chụp/xuất ảnh PNG độ phân giải cao.
- Danh sách hóa đơn có chọn nhiều, chọn tất cả, xóa nhiều hóa đơn và xuất ảnh các hóa đơn đã chọn.
- Chi tiết hóa đơn có nút xuất ảnh độ phân giải cao và dấu duyệt mẫu “QUẢN LÝ ĐÃ DUYỆT”, không có dòng chữ ký.
- Cài đặt có giao diện tạo/xóa tài khoản nhân viên và đổi mật khẩu; trạng thái online/offline chỉ là trạng thái trên thiết bị hiện tại.

LƯU Ý BẢO MẬT QUAN TRỌNG:
Phần tài khoản nhân viên vừa thêm là bản giao diện thử nghiệm lưu trong localStorage của trình duyệt, chưa phải hệ thống xác thực an toàn: tài khoản không tự đồng bộ giữa các máy, trạng thái online không phản ánh toàn hệ thống và mật khẩu chưa được bảo vệ theo chuẩn máy chủ. Không dùng phần này để bảo vệ dữ liệu thật hoặc triển khai công khai. Muốn đúng yêu cầu quản lý cấp tài khoản, đăng nhập lần đầu, ghi nhớ đăng nhập và theo dõi online/offline trên nhiều thiết bị, cần hoàn thiện Firebase Authentication + Cloud Functions/server và Firestore Rules phân quyền theo UID; không thể làm an toàn chỉ bằng HTML/JavaScript chạy trên trình duyệt.

Ảnh hóa đơn và con dấu trong giao diện là chứng từ mẫu do ứng dụng tạo, không phải con dấu pháp lý được chứng thực. Hãy chỉ sử dụng con dấu chính thức khi có quyền sử dụng.
