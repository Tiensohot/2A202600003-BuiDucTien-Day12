# AI Agent Production-Ready Deployment
**Sinh viên thực hiện:** Bùi Đức Tiến

---

## 🚀 Giới thiệu dự án
Đây là dự án hoàn thiện cho **Day 12: Hạ tầng Cloud và Deployment**. Ứng dụng là một AI Agent được xây dựng bằng FastAPI, tối ưu hóa bằng Docker Multi-stage và đã được triển khai thành công trên Render.com với OpenAI API (gpt-4o-mini).

### 🔗 Liên kết nhanh (Quick Links)
- **Báo cáo Lab (Answers Pt 1-5):** [MISSION_ANSWERS.md](./MISSION_ANSWERS.md)
- **Thông tin Deployment (Cloud URL & Test):** [DEPLOYMENT.md](./DEPLOYMENT.md)
- **Hệ thống đang chạy (Health Check):** [https://buiductien-ai-agent.onrender.com/health](https://buiductien-ai-agent.onrender.com/health)
- **GitHub Repo:** [https://github.com/Tiensohot/2A202600003-BuiDucTien-Day12](https://github.com/Tiensohot/2A202600003-BuiDucTien-Day12)

### 🧪 Hướng dẫn Test nhanh (Fast Test)
Dùng lệnh cURL sau để hỏi AI ngay lập tức qua Cloud:
```bash
curl -X POST https://buiductien-ai-agent.onrender.com/ask \
  -H "X-API-Key: bdt-agent-key-2026-secure" \
  -H "Content-Type: application/json" \
  -d '{"question": "Hello Agent!"}'
```
*(API Key: `bdt-agent-key-2026-secure`)*

---

## 🛠️ Công nghệ sử dụng
- **Backend:** FastAPI (Python 3.11)
- **LLM:** OpenAI API (`gpt-4o-mini`)
- **Containerization:** Docker Multi-stage Build
- **Platform:** Render.com (Singapore region, Free tier)
- **CI/CD:** GitHub → Render auto-deploy on push

---

## 📂 Cấu Trúc Dự Án

```
06-lab-complete/
├── app/
│   ├── main.py         # Entry point — FastAPI app + OpenAI integration
│   └── config.py       # 12-factor config từ environment variables
├── utils/
│   └── mock_llm.py     # Fallback khi không có OpenAI key
├── Dockerfile          # Multi-stage, non-root user, < 500 MB
├── docker-compose.yml  # Full stack local
├── render.yaml         # Render deployment config
└── requirements.txt
```

---

## Checklist Deliverable

- [x] Dockerfile (multi-stage, < 500 MB)
- [x] docker-compose.yml (agent + redis)
- [x] .dockerignore
- [x] Health check endpoint (`GET /health`)
- [x] Readiness endpoint (`GET /ready`)
- [x] API Key authentication (`X-API-Key` header)
- [x] Rate limiting (20 req/min)
- [x] Cost guard ($5/day budget)
- [x] Config từ environment variables (12-factor)
- [x] Structured JSON logging
- [x] Graceful shutdown (SIGTERM)
- [x] OpenAI API integration (gpt-4o-mini)
- [x] Public URL deployed trên Render.com

---

## Chạy Local

```bash
# 1. Setup
cp .env.example .env
# Thêm OPENAI_API_KEY vào .env

# 2. Chạy với Docker Compose
docker compose up

# 3. Test
curl http://localhost:8000/health

# 4. Test endpoint với API key
curl -H "X-API-Key: dev-key-change-me" \
     -X POST http://localhost:8000/ask \
     -H "Content-Type: application/json" \
     -d '{"question": "What is deployment?"}'
```

---

## Deploy lên Render

```bash
# 1. Push code lên GitHub
git push origin main

# Render tự động đọc render.yaml và deploy
# Hoặc dùng Render API:
curl -X POST https://api.render.com/v1/services/<service-id>/deploys \
  -H "Authorization: Bearer <render-api-key>"
```

---

## Kiểm Tra Production Readiness

```bash
python check_production_ready.py
```

Script này kiểm tra tất cả items trong checklist. Kết quả: **20/20 passed**.
