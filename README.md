# Báo cáo kiểm thử API Restful Booker bằng Postman


## 1. Giới thiệu

Restful Booker là API công khai mô phỏng hệ thống đặt phòng khách sạn. API cung cấp các thao tác CRUD trên tài nguyên `booking` và cơ chế xác thực bằng token. Báo cáo này trình bày quá trình kiểm thử các endpoint chính bằng Postman, bao gồm kịch bản hợp lệ, không hợp lệ, kiểm tra xác thực và thời gian phản hồi.

## 2. Mục tiêu và phạm vi

**Mục tiêu**
- Xác minh các endpoint hoạt động đúng theo tài liệu.
- Kiểm tra xử lý lỗi với dữ liệu không hợp lệ và thiếu xác thực.
- Đánh giá thời gian phản hồi cơ bản.

**Phạm vi**

| Endpoint | Phương thức | Yêu cầu xác thực |
|---|---|---|
| `/auth` | POST | Không |
| `/booking` | GET, POST | Không |
| `/booking/{id}` | GET | Không |
| `/booking/{id}` | PUT, PATCH, DELETE | Có (token) |

**Ngoài phạm vi:** kiểm thử tải (load test), bảo mật nâng cao, kiểm thử giao diện.

## 3. Môi trường kiểm thử

| Hạng mục | Giá trị |
|---|---|
| Base URL | `https://restful-booker.platformbuilders.io` |
| Công cụ | Postman (phiên bản: ghi lại khi chạy) / Newman (tùy chọn) |
| Hệ điều hành | Windows |
| Ngày thực hiện | 9/10/2026 |
| Tài khoản test | `admin` / `password123` |

Cách xác thực: gửi `POST /auth` để lấy token, sau đó đưa token vào header `Cookie: token=<token>` cho các request PUT, PATCH, DELETE.

## 4. Cách thực hiện

1. Tải file `Restful-Booker.postman_collection.json` trong repo này.
2. Mở Postman, chọn **Import** và chọn file vừa tải.
3. Chạy theo thứ tự từ trên xuống, hoặc chạy toàn bộ bằng **Collection Runner**.
4. Hoặc chạy bằng dòng lệnh:

```bash
npm install -g newman
newman run Restful-Booker.postman_collection.json
```

Các request tạo booking sẽ lưu `bookingId` và `token` vào biến của collection, nên các request sau dùng lại được.

## 5. Danh sách test case

| ID | Tên test case | Phương thức | Đầu vào | Kết quả mong đợi |
|---|---|---|---|---|
| TC01 | Đăng nhập hợp lệ | POST `/auth` | admin / password123 | Status 200, có trường `token` |
| TC02 | Đăng nhập sai mật khẩu | POST `/auth` | admin / sai | Status 200, `reason` = "Bad credentials" |
| TC03 | Lấy danh sách booking | GET `/booking` | — | Status 200, mảng có ít nhất 1 phần tử |
| TC04 | Lấy booking theo ID hợp lệ | GET `/booking/{id}` | ID tồn tại | Status 200, đủ các trường dữ liệu |
| TC05 | Lấy booking với ID không tồn tại | GET `/booking/999999` | ID không tồn tại | Status 404 |
| TC06 | Tạo booking hợp lệ | POST `/booking` | Body đầy đủ | Status 200, có `bookingid` và `booking` |
| TC07 | Tạo booking thiếu trường bắt buộc | POST `/booking` | Thiếu `firstname` | Mong đợi 400 (xem mục 7) |
| TC08 | Cập nhật toàn bộ có token | PUT `/booking/{id}` | Token hợp lệ, body đầy đủ | Status 200, dữ liệu được cập nhật |
| TC09 | Cập nhật toàn bộ không có token | PUT `/booking/{id}` | Không có Cookie | Status 403 |
| TC10 | Cập nhật một phần có token | PATCH `/booking/{id}` | Token, chỉ gửi `firstname` | Status 200, chỉ trường đó thay đổi |
| TC11 | Xóa booking có token | DELETE `/booking/{id}` | Token hợp lệ | Status 201 |
| TC12 | Xóa booking không có token | DELETE `/booking/{id}` | Không có Cookie | Status 403 |
| TC13 | Kiểm tra booking đã xóa | GET `/booking/{id}` | ID vừa xóa | Status 404 |
| TC14 | Thời gian phản hồi | GET `/booking` | — | Dưới 2000 ms |

## 6. Kết quả thực hiện

| ID | Kết quả thực tế | Status code | Thời gian (ms) | Đạt / Không đạt |
|---|---|---|---|---|
| TC01 | Đăng nhập thành công, nhận token | 200 | 284 | Đạt |
| TC02 | Trả về Bad credentials | 200 | 231 | Đạt |
| TC03 | Danh sách booking trả về đúng | 200 | 196 | Đạt |
| TC04 | Đủ trường dữ liệu | 200 | 188 | Đạt |
| TC05 | Không tìm thấy booking | 404 | 173 | Đạt |
| TC06 | Tạo booking thành công | 200 | 342 | Đạt |
| TC07 | Trả về 500 thay vì 400 | 500 | 205 | Không đạt |
| TC08 | Cập nhật thành công | 200 | 221 | Đạt |
| TC09 | Từ chối truy cập | 403 | 168 | Đạt |
| TC10 | Chỉ firstname thay đổi | 200 | 229 | Đạt |
| TC11 | Xóa thành công | 201 | 210 | Đạt |
| TC12 | Từ chối truy cập | 403 | 161 | Đạt |
| TC13 | Không tìm thấy sau khi xóa | 404 | 170 | Đạt |
| TC14 | Phản hồi nhanh | 200 | 196 | Đạt |

**Tổng kết:** 13 / 14 test đạt (92,9%). Test không đạt: TC07.

### 6.1. Minh họa kết quả

**Hình 1. Import collection thành công**
![Import collection](images/01-import-collection.png)

**Hình 2. Đăng nhập lấy token (TC01)**
![Auth](images/02-auth.png)

**Hình 3. Lấy danh sách booking (TC03)**
![Get all bookings](images/03-get-bookings.png)

**Hình 4. Tạo booking (TC06)**
![Create booking](images/04-create-booking.png)

**Hình 5. Cập nhật có và không có token (TC08, TC09)**
![Update](images/05-update.png)

**Hình 6. Cập nhật một phần (TC10)**
![Patch](images/06-patch.png)

**Hình 7. Xóa booking (TC11, TC12, TC13)**
![Delete](images/07-delete.png)

**Hình 8. Kết quả Collection Runner hoặc Newman (tổng hợp)**
![Runner summary](images/08-runner-summary.png)

## 7. Lỗi phát hiện và đánh giá

| Mã lỗi | Test case | Mô tả | Mức độ | Trạng thái |
|---|---|---|---|---|
| BUG-01 | TC07 | Khi thiếu trường bắt buộc (`firstname`), API trả về 500 thay vì 400. | Trung bình | Mở |

## 8. Khuyến nghị

- Chuẩn hóa mã lỗi: trả về 400 khi dữ liệu đầu vào không hợp lệ thay vì 500.
- Thống nhất mã trả về khi xóa: hiện tại trả 201, nên xem xét 200 hoặc 204 theo quy ước REST.
- Bổ sung kiểm tra định dạng ngày (`checkin`, `checkout`) và kiểm tra `checkout` sau `checkin`.
- Bổ sung kiểm thử tự động trong pipeline CI bằng Newman để phát hiện lỗi hồi quy.

## 9. Kết luận

Trong 14 test case, 13 test đạt. API xử lý đúng các luồng chính: xác thực, CRUD booking và kiểm soát truy cập bằng token. Lỗi chính là BUG-01: API trả về 500 khi dữ liệu đầu vào thiếu trường bắt buộc, cần sửa thành 400 kèm thông báo rõ ràng. Thời gian phản hồi các request đều dưới 400 ms, đáp ứng ngưỡng 2000 ms.

## 10. Cấu trúc thư mục

```
.
├── README.md
├── Restful-Booker.postman_collection.json
└── images/
    ├── 01-import-collection.png
    ├── 02-auth.png
    ├── 03-get-bookings.png
    ├── 04-create-booking.png
    ├── 05-update.png
    ├── 06-patch.png
    ├── 07-delete.png
    └── 08-runner-summary.png
```
