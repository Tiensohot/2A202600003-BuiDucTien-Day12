# Day 12 Lab - Mission Answers

## Part 1: Localhost vs Production

### Exercise 1.1: Anti-patterns found in Develop version
1. API Key và mật khẩu bị Hardcode trực tiếp vào source code.
2. Port chạy ứng dụng bị set cứng, không thể tùy chọn linh hoạt.
3. Thiết lập Debug=True khi chạy ở môi trường thực tế (Leak thông tin lỗi).
4. Không có API Health Check (`/health`) để theo dõi sức khoẻ khi container chạy lỗi.
5. Thiếu quy trình đóng ứng dụng an toàn (Graceful Shutdown), ngắt thẳng khi có tín hiệu tắt khiến request bị rớt.

### Exercise 1.3: Comparison table

| Feature | Basic (Develop) | Advanced (Production) | Tại sao quan trọng? |
|---------|-----------------|-----------------------|---------------------|
| Config | Hardcode | Env vars (.env) | Đảm bảo bảo mật thông tin nhạy cảm, dễ cấu hình trên Cloud mà không cần đổi code. |
| Health check | Không có | Có (/health) | Giúp Docker hoặc Platform giám sát và tự động Restart container nếu app bị treo cứng. |
| Logging | print() | JSON format | Dễ dàng tìm kiếm truy vấn log bằng các công cụ như Kibana, CloudWatch, bao gồm cả timestamp chuẩn. |
| Shutdown | Đột ngột | Graceful | Duy trì kết nối hiện tại cho xong request rồi mới tắt hẳn, không gây ra lỗi HTTP 5xx từ phía user. |


## Part 2: Docker

### Exercise 2.1: Dockerfile questions
1. Base image là gì?: `python:3.11-slim`
2. Working directory là gì?: `/app`
3. Tại sao COPY requirements.txt trước?: Tận dụng caching của Docker. Nếu source code thay đổi nhưng thư viện vẫn vậy thì không cần tải lại các module, giúp build nhanh hơn.
4. CMD vs ENTRYPOINT khác nhau thế nào?: ENTRYPOINT coi container như một chương trình thực thi với cội nguồn cố định. Còn CMD khởi tạo các định nghĩa tham số mặc định và có thể bị ghi đè lên lúc gọi lệnh `docker run`.

### Exercise 2.3: Image size comparison (Multi-stage)
* Develop Image Size: To vì chứa các file build dependencies và C-compiler dư thừa.
* Production Image Size (Multi-stage build): Rất nhỏ (< 200MB) vì stage sau cùng chỉ copy thư viện đã được build mà không kèm source gốc của system build nữa.


## Part 3: Cloud Deployment

### Exercise 3.1: Railway / Render deployment
- Deploy thành công thông qua việc upload source code và chỉ định file `railway.toml` hoặc `render.yaml`.
- Hệ thống trên Cloud tự động override số thứ tự PORT qua môi trường (Environment Variable).


## Part 4: API Security

### Exercise 4.1-4.3: Test results
- Auth Check: Khi không thêm `<X-API-Key>`, gọi request POST tới `/ask` trả về `HTTP 401 Unauthorized`. Thêm header đúng key thì nhận kết quả response 200.
- Rate Limit check: Áp dụng gọi curl API vượt quá 20 lần trong vòng 1 phút thì Request sẽ trả về HTTP 429 (Rate Limit Exceeded). Ngăn spam hệ thống.

### Exercise 4.4: Cost guard implementation
- Hệ thống tính toán sử dụng lượng Token Input và Output của LLM để tính ra USD. Biến lưu được cộng dần lên thành Daily Cost trong Redis (hoặc in-memory dictionary). Ở đầu mỗi request gọi API, code sẽ kiểm tra chi phí hiện tại, nếu `total >= BUDGET`, quăng lỗi `HTTP 503` bắt client làm lại sau.


## Part 5: Scaling & Reliability

### Exercise 5.1-5.5: Implementation notes
- Thiết lập Redis và Nginx Load Balancer bằng `docker-compose.yml`. Tách biệt toàn bộ trạng thái ghi nhớ hội thoại ra khỏi app FastAPI (stateless app) vào Redis database.
- Scalability: Khi scale app theo cấp số nhân thành nhiều replicas (`docker compose --scale agent=3`), load balancer trải đều lưu lượng, khi 1 instance crash thì request sau vào node khác vẫn không bị rớt dữ liệu session. Mọi hệ thống đều thông suốt.
