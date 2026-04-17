# Deployment Information

## Public URL
> https://(CẬP_NHẬT_URL_RENDER_HOẶC_RAILWAY_CỦA_BẠN).app

## Platform
Railway / Render 

## Test Commands

### 1. Health Check Test
```bash
curl https://(CẬP_NHẬT_URL_CỦA_BẠN)/health
```
*(Kết quả mong đợi: `{"status": "ok", "environment": "production", ...}`)*

### 2. API Test (Không khai báo Key)
```bash
curl -X POST https://(CẬP_NHẬT_URL_CỦA_BẠN)/ask \
  -H "Content-Type: application/json" \
  -d '{"question": "Hello"}'
```
*(Kết quả mong đợi: Bị chặn và báo 401 Unauthorized)*

### 3. API Test (Có authentication X-API-Key)
```bash
curl -X POST https://(CẬP_NHẬT_URL_CỦA_BẠN)/ask \
  -H "X-API-Key: dev-key-change-me" \
  -H "Content-Type: application/json" \
  -d '{"question": "Hello cloud platform!"}'
```
*(Kết quả mong đợi: Báo 200, AI Agent trả lời)*

## Environment Variables Đã Set up (Trên Cloud)
- `PORT`
- `ENVIRONMENT=production`
- `AGENT_API_KEY=dev-key-change-me`
- `RATE_LIMIT_PER_MINUTE=20`
- `DAILY_BUDGET_USD=5.0`
- `JWT_SECRET=super_secret_jwt`

## Screenshots Đính Kèm
*(Bạn hãy chụp vài bức ảnh màn hình chạy thành công trên Postman, Cloud dashboard rồi up vào thư mục con là thư mục `screenshots` và để link hình vô đây)*
- [Giao diện Cloud đang chạy service](screenshots/dashboard.png)
- [Test thành công cURL / Postman](screenshots/test.png)
