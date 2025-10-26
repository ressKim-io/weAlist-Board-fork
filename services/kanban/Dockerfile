FROM python:3.11-alpine AS builder

WORKDIR /app

RUN apk add --no-cache gcc musl-dev postgresql-dev

COPY requirements.txt .
RUN pip install --no-cache-dir --user -r requirements.txt

FROM python:3.11-alpine

WORKDIR /app

RUN apk add --no-cache libpq curl

COPY --from=builder /root/.local /root/.local
ENV PATH=/root/.local/bin:$PATH

# .dockerignore를 통해 tests/, docs 등은 제외됨
COPY . .

# 환경변수 기본값 (docker-compose에서 오버라이드 가능)
ENV PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

HEALTHCHECK --interval=30s --timeout=3s \
  CMD curl -f http://localhost:8000/health || exit 1

EXPOSE 8000

# Production mode (--reload 제거)
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000", "--workers", "4"]
