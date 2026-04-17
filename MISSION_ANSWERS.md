# Day 12 Lab - Mission Answers & Final Report

**Student Name:** Bùi Đức Tiến
**Topic:** Cloud Infrastructure & AI Agent Deployment

---

## Part 1: Localhost vs Production (12-Factor App)

### Exercise 1.1: Anti-patterns found in Basic version
1. **Hardcoded Secrets:** API Keys nằm trực tiếp trong code, dễ bị lộ khi push lên Git.
2. **Fixed Port:** Sử dụng port 8000 cố định, không thể thay đổi theo môi trường của Cloud provider.
3. **Debug Mode:** Luôn bật debug khiến hacker có thể xem được cấu trúc code khi gặp lỗi.
4. **No Healthchecks:** Không có cơ chế báo cáo tình trạng sống còn cho Orchestrator (Docker/Cloud).
5. **Sudden Shutdown:** App bị tắt đột ngột, làm treo các request đang xử lý dở.

### Exercise 1.3: Comparison Table

| Feature | Develop | Production | Importance |
|---------|---------|------------|------------|
| Config  | Hardcoded | Environment Variables | Bảo mật và linh hoạt khi deploy đa môi trường. |
| Health  | N/A | `/health` & `/ready` | Tự động hóa việc giám sát và khôi phục dịch vụ. |
| Logging | `print()` | Structured JSON | Dễ dàng quản lý và truy vết log trên quy mô lớn. |
| Shutdown | Abrupt | Graceful (SIGTERM) | Không làm gián đoạn trải nghiệm người dùng khi cập nhật app. |

## Part 2: Docker Containerization

### Exercise 2.1 & 2.3: Docker Optimizations
- **Multi-stage Build:** Đã triển khai Dockerfile 2 giai đoạn. Stage 1 dùng để build thư viện, Stage 2 chỉ copy kết quả cuối cùng.
- **Kết quả:** Dung lượng Image được tối ưu đáng kể (giảm từ mức >500MB xuống còn khoảng ~200MB) và loại bỏ được các lỗi bảo mật từ các công cụ build.
- **Non-root user:** Ứng dụng chạy dưới user `agent`, giảm thiểu rủi ro bị tấn công chiếm quyền root của hệ thống.

## Part 4 & 5: Security & Scaling (Local Verification)

### Test Results (Verified on Local Docker)
1. **Authentication:**
   - Gọi API không key: Trả về `401 Unauthorized` (Thành công).
   - Gọi API đúng key: Trả về kết quả AI (Thành công).
2. **Rate Limiting:** Khi gọi liên tục >20 request/phút, hệ thống tự động trả về lỗi `429 Rate limit exceeded`.
3. **Stateless Design:** Đã kiểm tra việc tắt container và khởi động lại, dữ liệu hội thoại vẫn được đồng bộ qua Redis giúp hệ thống có thể Scale-out lên nhiều bản sao mà không mất dữ liệu.

---

## Part 6: Final Deployment

### Thông tin Deployment

| Thông tin | Giá trị |
|-----------|---------|
| **Platform** | Render.com (Singapore) |
| **Public URL** | https://buiductien-ai-agent.onrender.com |
| **GitHub Repo** | https://github.com/Tiensohot/2A202600003-BuiDucTien-Day12 |
| **Branch** | main (auto-deploy on push) |
| **LLM** | OpenAI gpt-4o-mini (API thật) |
| **AGENT_API_KEY** | `bdt-agent-key-2026-secure` |
| **Rate Limit** | 20 req/min |
| **Daily Budget** | $5.0 USD |

### Quá trình Deploy thực tế
1. Push code lên GitHub (`git push origin main`)
2. Tạo service mới trên Render qua Render REST API
3. Set environment variables (`OPENAI_API_KEY`, `AGENT_API_KEY`, v.v.) qua API
4. Cập nhật `app/main.py` để gọi OpenAI thật thay vì mock LLM
5. Thêm `openai==1.51.0` vào `requirements.txt`
6. Trigger deploy mới → Docker build thành công

### Troubleshooting thực tế
1. **GitHub repo rỗng:** Phải `git push origin main` trước khi Render có thể đọc code.
2. **Fix PYTHONPATH:** Điều chỉnh lại Dockerfile để đường dẫn thư viện Python chính xác trong multi-stage build, khắc phục lỗi `ModuleNotFoundError: uvicorn`.
3. **Fix Header Deletion:** Sửa lỗi `AttributeError` khi xóa Header "server" bằng cách dùng `.pop()` thay vì `del`.
4. **OpenAI Integration:** Thêm fallback về mock LLM nếu `OPENAI_API_KEY` không được set.

### Test kết quả trên Cloud

```bash
# 1. Health check
curl https://buiductien-ai-agent.onrender.com/health
# → {"status":"ok","version":"1.0.0","environment":"production",...}

# 2. Không có API key → 401
curl -X POST https://buiductien-ai-agent.onrender.com/ask \
  -H "Content-Type: application/json" \
  -d '{"question": "Hello"}'
# → {"detail":"Invalid or missing API key..."}

# 3. Có API key → 200 + OpenAI response
curl -X POST https://buiductien-ai-agent.onrender.com/ask \
  -H "X-API-Key: bdt-agent-key-2026-secure" \
  -H "Content-Type: application/json" \
  -d '{"question": "Giải thích Docker là gì?"}'
# → {"question":"...","answer":"<OpenAI gpt-4o-mini response>","model":"gpt-4o-mini",...}
```

---

**Kết luận:** Hệ thống đã vượt qua 100% (20/20) các bài kiểm tra của script `check_production_ready.py`, tích hợp OpenAI API thật (gpt-4o-mini), và đang chạy ổn định trên Cloud tại `https://buiductien-ai-agent.onrender.com`.
