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
| Hệ điều hành | _(điền)_ |
| Ngày thực hiện | _(điền)_ |
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

> Phần này điền sau khi chạy thực tế. Thay `_(chưa chạy)_` bằng kết quả thật.

| ID | Kết quả thực tế | Status code | Thời gian (ms) | Đạt / Không đạt |
|---|---|---|---|---|
| TC01 | _(chưa chạy)_ | | | |
| TC02 | _(chưa chạy)_ | | | |
| TC03 | _(chưa chạy)_ | | | |
| TC04 | _(chưa chạy)_ | | | |
| TC05 | _(chưa chạy)_ | | | |
| TC06 | _(chưa chạy)_ | | | |
| TC07 | _(chưa chạy)_ | | | |
| TC08 | _(chưa chạy)_ | | | |
| TC09 | _(chưa chạy)_ | | | |
| TC10 | _(chưa chạy)_ | | | |
| TC11 | _(chưa chạy)_ | | | |
| TC12 | _(chưa chạy)_ | | | |
| TC13 | _(chưa chạy)_ | | | |
| TC14 | _(chưa chạy)_ | | | |

**Tổng kết:** _(số test đạt / tổng số test)_

### 6.1. Minh họa kết quả

Chèn ảnh chụp màn hình vào thư mục `images/` và đặt tên theo mẫu bên dưới.

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

Điền sau khi kiểm thử. Mỗi lỗi ghi rõ: mã lỗi, test case liên quan, mô tả, các bước tái hiện, kết quả mong đợi, kết quả thực tế và mức độ nghiêm trọng (Cao / Trung bình / Thấp).

| Mã lỗi | Test case | Mô tả | Mức độ | Trạng thái |
|---|---|---|---|---|
| BUG-01 | TC07 | Khi thiếu trường bắt buộc, API có thể trả về mã lỗi máy chủ (500) thay vì lỗi dữ liệu đầu vào (400). _(kiểm chứng khi chạy)_ | Trung bình | Mở |

## 8. Khuyến nghị

- Chuẩn hóa mã lỗi: trả về 400 khi dữ liệu đầu vào không hợp lệ thay vì 500.
- Thống nhất mã trả về khi xóa: hiện tại trả 201, nên xem xét 200 hoặc 204 theo quy ước REST.
- Bổ sung kiểm tra định dạng ngày (`checkin`, `checkout`) và kiểm tra `checkout` sau `checkin`.
- Bổ sung kiểm thử tự động trong pipeline CI bằng Newman để phát hiện lỗi hồi quy.

## 9. Kết luận

_(Viết sau khi có kết quả: nêu số test đạt, các lỗi chính, và đánh giá tổng quan chất lượng API.)_

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
