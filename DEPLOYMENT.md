# Deployment Information

## Public URL
> https://buiductien-ai-agent.onrender.com

## Platform
Render (Singapore region)

## Service Info
- **Service ID:** `srv-d7h100tckfvc739n8ffg`
- **Dashboard:** https://dashboard.render.com/web/srv-d7h100tckfvc739n8ffg
- **GitHub Repo:** https://github.com/Tiensohot/2A202600003-BuiDucTien-Day12
- **Branch:** main (auto-deploy on push)

## Test Commands

### 1. Health Check Test
```bash
curl https://buiductien-ai-agent.onrender.com/health
```
*(Kết quả mong đợi: `{"status": "ok", "environment": "production", ...}`)*

### 2. API Test (Không khai báo Key)
```bash
curl -X POST https://buiductien-ai-agent.onrender.com/ask \
  -H "Content-Type: application/json" \
  -d '{"question": "Hello"}'
```
*(Kết quả mong đợi: Bị chặn và báo 401 Unauthorized)*

### 3. API Test (Có authentication X-API-Key)
```bash
curl -X POST https://buiductien-ai-agent.onrender.com/ask \
  -H "X-API-Key: bdt-agent-key-2026-secure" \
  -H "Content-Type: application/json" \
  -d '{"question": "Hello cloud platform!"}'
```
*(Kết quả mong đợi: Báo 200, AI Agent trả lời)*

## Environment Variables Đã Set up (Trên Cloud)
- `ENVIRONMENT=production`
- `APP_VERSION=1.0.0`
- `AGENT_API_KEY=bdt-agent-key-2026-secure`
- `JWT_SECRET=bdt-jwt-secret-2026`
- `RATE_LIMIT_PER_MINUTE=20`
- `DAILY_BUDGET_USD=5.0`

## Screenshots Đính Kèm
*(Bạn hãy chụp vài bức ảnh màn hình chạy thành công trên Postman, Cloud dashboard rồi up vào thư mục con là thư mục `screenshots` và để link hình vô đây)*
- [Giao diện Cloud đang chạy service](screenshots/dashboard.png)
- [Test thành công cURL / Postman](screenshots/test.png)
