🐈 The Cat API Testing Project - https://api.thecatapi.com/v1
📅 Thời gian thực hiện: 19/10/2025 - 26/10/2025
👤 Tester: Hoàng Đỉnh

---
OVERVIEW

Hello! Đây là dự án thể hiện cách tôi thực hiện kiểm thử API, tôi tập trung vào các thao tác thêm, sửa, xóa và kiểm tra dữ liệu hình ảnh về mèo. Mục tiêu là đảm bảo hệ thống hoạt động ổn định và nhanh chóng ngay cả khi xử lý nhiều flow phức tạp.

---
QUICK SETUP & DEMO

1. Tải về và nhập vào cả 2 file là: Collection (Hoàng Đỉnh - The Cat API.postman_collection.json) và the Environment (MY_SERVICE_API_KEY=DEV.postman_environment.json).

2. Chọn đúng môi trường MY_SERVICE_API_KEY=DEV Environment để sử dụng .

3. Chạy Collection Image Validation (CRUD) quá trình sẽ tự động thực hiện và kiểm tra kết quả.

---
PROJECT STRUCTURE

Hoàng Đỉnh - The Cat API.postman_collection.json: 	Chứa collection với test scripts
MY_SERVICE_API_KEY=DEV.postman_environment.json:	Chứa Environment file có API key
Test Flow.xlsx:										Minh họa logic kiểm thử
schemas/											Định nghĩa JSON schema
screenshots/										Screenshots của test results,variables,request...etc.


---
TEST FLOW LOGIC

Quy trình kiểm thử được xây dựng theo luồng có điều kiện, nghĩa là chỉ tiếp tục nếu bước trước thành công:

1. **GET /breeds**
   - ✅ Status code 200
   - ✅ JSON schema validation cho tất cả giống mèo
   - ✅ Lưa lại biến breed_id cho upload image
   - ✅ Kiểm tra dữ liệu giống mèo có đúng tên, nguồn gốc và mã quốc gia

2. **POST /images/upload**
   - ✅ Status code 201
   - ✅ JSON Schema validation cho uploaded image
   - ✅ Lưa lại thông tin cho tất cả các trường `expected_*` variables, UploadImage_id (primary key) cho những test sau.
   - ✅ Chỉ tiếp tục nếu tải lên thành công.

3. **GET /images?limit=10**
   - ✅ Status code 200
   - ✅ Đảm bảo ảnh tải lên phản hồi thành công
   - ✅ So sánh các trường với `expected_*` variables bởi lọc bằng khóa UploadImage_id (primary key).
   - ✅ JSON Schema validation cho mục ảnh của tôi.
   - ✅ Chỉ tiếp tục nếu UploadImage_id có trong mục ảnh của tôi.

4. **GET /images/{image_id}**
   - ✅ Status code 200
   - ✅ Kiểm tra thông tin chi tiết ảnh có khớp với hình ảnh đã tải lên.
   - ✅ JSON Schema validation and field checks for get detail image
   - ✅ Chỉ tiếp tục nếu dữ liệu ảnh tải lên khớp với mục ảnh của tôi.

5. **DELETE /images/{image_id}**
   - ✅ Status code 204 (no content)
   - ✅ Delete uploaded image
   - 🔁 Thử lại tối đa 3 lần nếu xóa không thành công.
   - ✅ Kiểm tra lại mục ảnh của tôi để xác nhận đã xóa bằng cách thử lại **GET /images?limit=10**
        ->  Nếu UploadImage_id không được tìm thấy trong my image, hoàn thành test!.
		
		
---
PERFORMANCE & RELIABILITY TECHNIQUES

Để đảm bảo tốc độ và độ ổn định, dự án đã áp dụng nhiều kỹ thuật:

** Thực thi có điều kiện

	- Sử dụng pm.execution.setNextRequest() để điều hướng luồng kiểm thử dựa trên kết quả từng bước.

	- Dừng ngay lập tức nếu bước quan trọng bị lỗi (ví dụ: thiếu UploadImage_id).

** Quản lý biến động

	- Lưu trữ dữ liệu phát sinh trong quá trình kiểm thử như UploadImage_id, base_url, api-key, v.v.

	- Giúp kiểm tra chéo giữa các bước với độ chính xác cao.

** Kiểm tra định dạng dữ liệu phản hồi

	- Sử dụng thư viện Ajv để kiểm tra dữ liệu phản hồi có đúng định dạng quy định.

	- Cung cấp thông báo lỗi chi tiết để dễ dàng xử lý.

** Cơ chế thử lại thông minh

	- Áp dụng cơ chế thử lại cho các yêu cầu xóa (tối đa 3 lần).

	- Tăng độ tin cậy khi phản hồi từ API không ổn định.

** Kiểm tra hiệu năng

	- Kiểm tra thời gian phản hồi (ví dụ: dưới 500ms) và kích thước dữ liệu (ví dụ: dưới 2KB).

	- Đảm bảo API hoạt động nhanh và nhẹ.

** Kiểm tra tiêu chuẩn bảo mật

	- Xác minh các tiêu đề bảo mật như x-frame-options và x-content-type-options.

	- Đảm bảo tuân thủ các nguyên tắc bảo mật cơ bản.


---
TEST SUMMARY

_ Tổng số kiểm thử: 36
_ Thời gian thực hiện: khoảng 10.6 giây
_ Thời gian phản hồi trung bình: khoảng 1.6 giây
_ Tỷ lệ thành công: trên 90%
_ Các lỗi được ghi lại chi tiết và có cơ chế dừng kiểm thử khi cần thiết


---
SPRINT & QUY TRÌNH AGILE

Dự án được triển khai theo phương pháp Agile với 4 giai đoạn (sprint), mỗi giai đoạn hoàn thành một phần quan trọng của bộ kiểm thử tự động:

Sprint 1 (19.10.25 - 20.10.25): API Exploration & Environment Setup
- Nghiên cứu tài liệu của The Cat API
- Xác định mục tiêu và các điểm kiểm thử
- Cấu hình môi trường kiểm thử và các biến cần thiết
- Thiết kế luồng kiểm thử bằng Excel

Sprint 2 (21.10.25 - 24.10.25): Collection Design & Test Scripting
- Tạo bộ kiểm thử với các yêu cầu thêm, sửa, xóa, kiểm tra
- Viết kịch bản kiểm thử cho từng điểm kiểm thử
- Validate schemas bằng Ajv
- Áp dụng điều kiện kiểm thử và dynamic variables

Sprint 3 (25.10.25 - 25.10.25): Automation & Performance Validation
- Tự động hóa toàn bộ quy trình kiểm thử bằng Collection Runner
- Thêm kiểm tra thời gian phản hồi và kích thước dữ liệu
- Kiểm tra tiêu chuẩn bảo mật
- Tối ưu hóa cơ chế thử lại và xử lý lỗi

Sprint 4 (26.10.25 - 26.10.25): Documentation & Finalization
- Chụp ảnh màn hình và lưu kết quả kiểm thử
- Hoàn thiện tài liệu hướng dẫn sử dụng
- Sắp xếp lại các tệp dự án, định dạng dữ liệu và ảnh chụp


---
NOTES:
- Dự án được thực hiện độc lập bởi một người kiểm thử duy nhất.
- Có sử dụng các công cụ tự động hỗ trợ kiểm thử:

 + Collection runner

 + Postman Environment

 + Ajv - "Another JSON Schema Validator"
 
