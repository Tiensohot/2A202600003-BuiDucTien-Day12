# AI Agent Production-Ready Deployment
**Sinh viên thực hiện:** Đỗ Trọng Minh

---

## 🚀 Giới thiệu dự án
Đây là dự án hoàn thiện cho **Day 12: Hạ tầng Cloud và Deployment**. Ứng dụng là một AI Agent được xây dựng bằng FastAPI, tối ưu hóa bằng Docker Multi-stage và đã được triển khai thành công trên Cloud.

### 🔗 Liên kết nhanh (Quick Links)
- **Báo cáo Lab (Answers Pt 1-5):** [MISSION_ANSWERS.md](./MISSION_ANSWERS.md)
- **Thông tin Deployment (Cloud URL & Test):** [DEPLOYMENT.md](./DEPLOYMENT.md)
- **Hệ thống đang chạy (Health Check):** [https://day12-ha-tang-cloud-va-deployment.onrender.com/health](https://day12-ha-tang-cloud-va-deployment.onrender.com/health)

### 🧪 Hướng dẫn Test nhanh (Fast Test)
Dùng lệnh cURL sau để hỏi AI ngay lập tức qua Cloud:
```bash
curl -X POST https://day12-ha-tang-cloud-va-deployment.onrender.com/ask \
  -H "X-API-Key: 66d2fdf910617b84f915801ee6d2472f" \
  -H "Content-Type: application/json" \
  -d '{"question": "Hello Agent!"}'
```
*(Key mặc định: `66d2fdf910617b84f915801ee6d2472f`)*

---

## 🛠️ Công nghệ sử dụng
- **Backend:** FastAPI (Python 3.11)
- **Database:** Redis (Session management & Rate limiting)
- **Containerization:** Docker & Docker Compose
- **Platform:** Render.com (Cloud Deployment)

---

## 📂 Phân tích cấu trúc dự án

## Checklist Deliverable

- [x] Dockerfile (multi-stage, < 500 MB)
- [x] docker-compose.yml (agent + redis)
- [x] .dockerignore
- [x] Health check endpoint (`GET /health`)
- [x] Readiness endpoint (`GET /ready`)
- [x] API Key authentication
- [x] Rate limiting
- [x] Cost guard
- [x] Config từ environment variables
- [x] Structured logging
- [x] Graceful shutdown
- [x] Public URL ready (Railway / Render config)

---

## Cấu Trúc

```
06-lab-complete/
├── app/
│   ├── main.py         # Entry point — kết hợp tất cả
│   ├── config.py       # 12-factor config
│   ├── auth.py         # API Key + JWT
│   ├── rate_limiter.py # Rate limiting
│   └── cost_guard.py   # Budget protection
├── Dockerfile          # Multi-stage, production-ready
├── docker-compose.yml  # Full stack
├── railway.toml        # Deploy Railway
├── render.yaml         # Deploy Render
├── .env.example        # Template
├── .dockerignore
└── requirements.txt
```

---

## Chạy Local

```bash
# 1. Setup
cp .env.example .env

# 2. Chạy với Docker Compose
docker compose up

# 3. Test
curl http://localhost/health

# 4. Lấy API key từ .env, test endpoint
API_KEY=$(grep AGENT_API_KEY .env | cut -d= -f2)
curl -H "X-API-Key: $API_KEY" \
     -X POST http://localhost/ask \
     -H "Content-Type: application/json" \
     -d '{"question": "What is deployment?"}'
```

---

## Deploy Railway (< 5 phút)

```bash
# Cài Railway CLI
npm i -g @railway/cli

# Login và deploy
railway login
railway init
railway variables set OPENAI_API_KEY=sk-...
railway variables set AGENT_API_KEY=your-secret-key
railway up

# Nhận public URL!
railway domain
```

---

## Deploy Render

1. Push repo lên GitHub
2. Render Dashboard → New → Blueprint
3. Connect repo → Render đọc `render.yaml`
4. Set secrets: `OPENAI_API_KEY`, `AGENT_API_KEY`
5. Deploy → Nhận URL!

---

## Kiểm Tra Production Readiness

```bash
python check_production_ready.py
```

Script này kiểm tra tất cả items trong checklist và báo cáo những gì còn thiếu.
