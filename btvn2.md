## Phần 1. Phân tích

**cookies (~4kb):**
- dung lượng rất nhỏ, dùng lưu token đăng nhập, định danh người dùng
- bị gửi kèm theo mỗi request lên server nên nhét nhiều sẽ tốn băng thông, chậm
- server đọc và kiểm soát được

**cache (mb/gb):**
- dung lượng lớn, dùng lưu file tĩnh như ảnh, css, js
- không gửi lên server, giúp tải trang nhanh và tiết kiệm mạng
- không dùng để định danh, và có thể bị cũ (ví dụ giao diện đổi rồi mà vẫn hiện màu cũ)

**đánh đổi:** cookies nhỏ nhưng quan trọng cho đăng nhập, cache lớn nhưng chỉ hợp cho dữ liệu tĩnh và dễ bị cũ. nên cookies chỉ giữ token, còn dữ liệu nặng như giỏ hàng thì lưu ở server.

## Phần 2. Triển khai

lỗi hiện tại do dùng session cookie (không có thời hạn) nên đóng trình duyệt là mất. cần đổi sang persistent cookie bằng cách thêm Max-Age.

```
Set-Cookie: session_token=abc123xyz; Max-Age=604800; Path=/; HttpOnly; Secure; SameSite=Strict
```

- `session_token=abc123xyz`: token định danh shipper, giỏ hàng lưu ở server gắn với token này nên mở lại vẫn còn
- `Max-Age=604800`: sống 7 ngày (tính bằng giây), đóng trình duyệt hay load lại vẫn không mất
- `Path=/`: dùng cho toàn bộ trang web
- `HttpOnly`: javascript phía client không đọc được cookie (document.cookie không thấy), chống bị đánh cắp qua xss, đáp ứng ràng buộc đề bài
- `Secure`: cookie chỉ gửi qua https, tránh bị nghe lén trên đường truyền
- `SameSite=Strict`: chỉ gửi cookie khi request từ chính trang web, chống giả mạo request

## Phần 3. Kiểm thử

hard reload để xóa cache cũ:

1. windows: nhấn `Ctrl + Shift + R` hoặc `Ctrl + F5`
2. macbook: nhấn `Cmd + Shift + R`
3. cách khác: nhấn f12, chuột phải vào nút reload trên trình duyệt, chọn "Empty Cache and Hard Reload"
4. nếu vẫn chưa được: nhấn `Ctrl + Shift + Delete`, chọn "Cached images and files", bấm xóa. không tick cookies để khỏi bị đăng xuất.