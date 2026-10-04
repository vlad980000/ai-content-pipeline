# AI Content Pipeline
FastAPI + Celery + Jetson AI inference pipeline

## Архитектура
```
├── api/                    # FastAPI application
│   ├── main.py            # Entry point, lifespan, middleware
│   ├── config.py          # Pydantic settings
│   ├── routes/            # API endpoints
│   ├── middleware/        # Auth, rate limiting, logging
│   └── schemas.py         # Pydantic models
├── workers/               # Celery workers
│   ├── base.py            # Shared worker config
│   ├── image_worker.py    # Inpainting, upscaling
│   ├── video_worker.py    # Face swap, video generation
│   └── audio_worker.py    # Voice clone, TTS (Jetson)
├── services/              # Business logic
├── models/                # ML model wrappers
├── infra/                 # Docker, env, nginx
├── tests/                 # Tests
├── requirements/          # Dependency files
└── scripts/               # Utility scripts
```

## Быстрый старт
```bash
# 1. Set up env
cp infra/.env.example infra/.env

# 2. Start services
cd infra
docker-compose up -d redis minio

# 3. Run API
python -m api.main --reload

# 4. Run worker
celery -A workers.base.app worker -l info
```

## Workers
- `image_worker` — inpainting, upscaling (SD/ControlNet)
- `video_worker` — face swap, video gen (SVD/IP-Adapter)
- `audio_worker` — voice clone, TTS (RVC/XTTS) — Jetson

## API
- `POST /api/v1/jobs/create` — создать задачу
- `GET /api/v1/jobs/{id}` — статус задачи
- `GET /api/v1/models` — список моделей
- `POST /api/v1/auth/telegram` — Telegram auth
