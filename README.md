# Bài 13 : (Bài tập tổng hợp) Ngày Đầu Thực Tập Dev Tại Công Ty Công Nghệ

##  **Phần A –** Thiết lập Profile và Bookmark chuẩn Dev
---
1.
  _ Hiện tượng khi dùng chung 1 Profile: Xung đột Session/Cookie. Trình duyệt chia sẻ chung bộ lưu trữ Cookie cho cùng một domain (github.com), dẫn đến việc tài khoản đăng nhập sau sẽ ghi đè tài khoản trước, buộc tài khoản kia bị đăng xuất

2.
|   Profile   |   Tên đặt   |   Mục đích riêng   |
|:---:|:---:|:---:|
|   1   |   Personal   |   Sử dụng việc cá nhân (GitHub cá nhân, mạng xã hội, giải trí, email cá nhân).   |
|   2   |   company-work   |   Phục vụ công việc công ty (GitHub công ty, Jira, Slack, hệ thống nội bộ).   |
|   3   |   Test   |   Phục vụ test chức năng, debug, đăng nhập tài khoản giả lập/khách mà không ảnh hưởng profile chính.   |

3. Cấu trúc 3 thư mục Bookmark:

- 📁 01_Daily_Tools: github.com, mail.google.com (hoặc Jira/Slack).

- 📁 02_Project_Docs: test.internal-tool.com, docs.internal-tool.com (tài liệu API/specs).

- 📁 03_Dev_Resources: developer.mozilla.org (MDN), stackoverflow.com.

4.
- Chế độ cho mượn máy: Dùng Guest Profile (Hồ sơ Khách) hoặc Incognito Mode (Chế độ Ẩn danh). Toàn bộ lịch sử, cookie, mật khẩu cá nhân không bị lộ và mọi dữ liệu phiên duyệt web sẽ tự động xóa sạch ngay khi đóng cửa sổ.

## Phần B – Chọn đúng Extension hỗ trợ
1  Nhóm Extension:

-  Chọn: Nhóm Dev Suite — Tiện ích cụ thể: JSON Viewer (hoặc JSON Formatter).

-  Lý do không chọn 2 nhóm còn lại:

  -  Productivity Suite (Notion Web Clipper, Todoist...): Dành cho quản lý thời gian/công việc, không có tính năng parse và format cú pháp dữ liệu JSON.

  -  Security Suite (uBlock Origin, Bitwarden...): Dành cho bảo mật, chặn quảng cáo, lọc mã độc; không hỗ trợ hiển thị cấu trúc dữ liệu.

2  Cấu hình Site Access: Chọn On click (hoặc cấu hình cụ thể tên domain API cần đọc).

-  Giải thích: Tuân thủ nguyên tắc đặc quyền tối thiểu (Least Privilege). Tránh cấp quyền On all sites để ngăn rủi ro rò rỉ dữ liệu nhạy cảm hoặc nguy cơ extension bị tấn công chuỗi cung ứng (supply-chain attack) đọc trộm nội dung trên mọi website.

## Phần C – Phân tích luồng HTTP Request
1  4 giai đoạn xử lý:

    - Phân giải DNS (DNS Resolution): Trình duyệt tra cứu địa chỉ IP tương ứng với tên miền test.internal-tool.com.
    
    - Thiết lập kết nối (TCP Handshake & SSL/TLS): Khởi tạo kết nối mạng và bắt tay mã hóa bảo mật HTTPS.
    
    - Gửi Request & Nhận Response (HTTP Exchange): Gửi HTTP GET request lên server và nhận mã trạng thái cùng mã nguồn HTML/dữ liệu trả về.
    
    - Dựng giao diện (Browser Rendering): Trình duyệt parse HTML, CSS, JavaScript để render giao diện lên màn hình.
    
2 So sánh 2 lỗi 

|  Mã trạng thái  |  Ý nghĩa  |  Lỗi nằm ở đâu ?  |  Hướng Debug ban đầu  |
|:---|:---|:---|:---|
| 404 Not Found  |  Không tìm thấy tài nguyên yêu cầu  |  Client-side (yêu cầu sai tài nguyên/URL)  |  Kiểm tra lại tính chính xác của đường dẫn URL, endpoint hoặc routing hệ thống.  |
|  500 Internal Server Error  |  Lỗi máy chủ nội bộ không xử lý được  |  Server-side (máy chủ backend gặp crash/lỗi logic)  |  Kiểm tra Log máy chủ (Server Logs), cấu hình code xử lý backend và kết nối Database.  |

## Phần D – Chẩn đoán Cache và Cookies
1  Chẩn đoán CSS cũ & Khắc phục:

- Nguyên nhân: Trình duyệt đã lưu file CSS vào Browser Cache, khi tải lại trang thông thường (F5), trình duyệt ưu tiên lấy file CSS từ Cache máy tính thay vì tải bản mới từ Server.

- Thao tác khắc phục: Nhấn tổ hợp phím Ctrl + F5 (hoặc Ctrl + Shift + R) để Hard Reload, hoặc mở F12, chuột phải vào nút Reload trên trình duyệt chọn Empty Cache and Hard Reload.

2  Cookie thiếu cờ HttpOnly:

- Rủi ro: Mã JavaScript (thông qua document.cookie) có thể đọc trực tiếp giá trị của cookie session_id.

- Loại tấn công: Tấn công XSS (Cross-Site Scripting) nhằm đánh cắp phiên làm việc (Session Hijacking).

3  Thao tác xóa phiên đăng nhập giữ nguyên Cache:

- Vào F12 > Tab Application > Mục Storage > Mở rộng Cookies > Chọn domain test.internal-tool.com.

- Chuột phải vào Cookie session_id (hoặc nhấn nút Clear All Cookies ở trên) và chọn Delete. Không bấm "Clear site data" vì sẽ xóa luôn Cache.

## Phần E – Debug bằng Inspect Element và Network Tab

  1  Thao tác sửa màu nút trực tiếp:
  
    1  Chuột phải vào nút "Đăng nhập" > chọn Inspect (hoặc bấm biểu tượng mũi tên góc trên tab DevTools rồi trỏ vào nút).
    
    2  Tại tab con Styles ở cột bên phải, tìm thuộc tính background-color (hoặc color).
    
    3  Nhấp đúp vào mã màu hiện tại để đổi trực tiếp sang mã màu chuẩn của bản thiết kế và xác nhận hiển thị.
  
  2  Quy trình 4 Bước Debugging trên tab Network:
  
    Bước 1 - Xác định (Identify): Nhấp chọn dòng request API màu đỏ trên danh sách để mở bảng chi tiết.
  
    Bước 2 - Phân tích Status & Headers (Inspect): Xem Status Code (mã lỗi gì) và tab Headers (kiểm tra Request URL, Request Method, Query Parameters/Headers gửi lên).
  
    Bước 3 - Xem Payload & Response (Check Data): Chuyển sang tab Payload kiểm tra body gửi đi và tab Response/Preview để đọc thông báo lỗi JSON chi tiết trả về từ Backend.
  
    Bước 4 - Kết luận & Báo lỗi (Report/Fix): Tổng hợp mã lỗi kèm body/response cụ thể gửi cho đội ngũ Backend xử lý (hoặc tự sửa nếu do gửi sai tham số phía frontend).
  
  3  2 thao tác cần bật trên tab Network:
  
   -  Bật bộ lọc Fetch/XHR (để chỉ hiển thị riêng các request API/dữ liệu).
  
   -  Tích chọn ô Disable cache (đảm bảo request luôn lấy dữ liệu mới nhất từ server khi mở DevTools).
