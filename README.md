# PatchMon

✅ **PatchMon** is a lightweight patch management and monitoring platform that helps you track and manage OS/package updates across agents. It uses a simple service stack (Postgres + Redis + Backend + Frontend) and is designed to be run with Docker Compose for easy deployment.

---

## 🚀 Key Benefits

- **Centralized patch monitoring** — View/update patch status across all registered agents from a single dashboard.
- **Scalable and resilient** — Uses Postgres for persistent storage and Redis for caching/queues; services are health-checked in Compose.
- **Easy deployment** — Ships as Docker images for frontend and backend; a single `docker compose up -d` starts the whole stack.
- **Secure by configuration** — Secrets, CORS, and server connection settings are configurable via environment variables.

---

## 🔧 Architecture (from `patchmon.yml`)

- **database**: Postgres 17 (persistent volume: `postgres_data`)
- **redis**: Redis 7 (persistent volume: `redis_data`)
- **backend**: `ghcr.io/patchmon/patchmon-backend:latest` (connects to DB & Redis)
- **frontend**: `ghcr.io/patchmon/patchmon-frontend:latest` (exposed on port 3000)
- **volumes**: `postgres_data`, `redis_data`, `agent_files`

> The compose file includes healthchecks for DB and Redis and uses `depends_on` with service health conditions for ordered startup.

---

## ⚠️ Important configuration notes

Before starting, update these secret values in `patchmon.yml` (or supply via environment override):

- **Database password**: `POSTGRES_PASSWORD` (services.database.environment)
- **Redis password**: the `redis` service command (`redis-server --requirepass <password>`) and its healthcheck auth
- **JWT secret**: `JWT_SECRET` (services.backend.environment)

Security tips:
- Use a strong random secret: `openssl rand -hex 64`
- Do not expose default passwords (the compose file uses simple placeholders)
- Set `CORS_ORIGIN`, `SERVER_PROTOCOL`, `SERVER_HOST`, and `SERVER_PORT` to match how users and agents will access the server

---

## 🧭 Quick start (Docker Compose)

1. Edit `patchmon.yml` and replace placeholder secrets and host values:

   - `POSTGRES_PASSWORD`: set a strong DB password
   - Redis command: `redis-server --requirepass <your-redis-password>` and update the healthcheck auth
   - `JWT_SECRET`: set a strong JWT secret
   - `CORS_ORIGIN` and `SERVER_*`: set your frontend URL and server access settings

2. Start the stack:

```bash
docker compose -f patchmon.yml up -d
```

3. Check logs and service status:

```bash
docker compose -f patchmon.yml logs -f
docker compose -f patchmon.yml ps
```

4. Access the frontend in your browser (default): `http://localhost:3000`

---

## 🔁 Updating images

To update to the latest backend/frontend images and restart:

```bash
docker compose -f patchmon.yml pull
docker compose -f patchmon.yml up -d
```

---

## ✅ Troubleshooting

- If the backend says it cannot connect to Postgres or Redis, verify `POSTGRES_PASSWORD`, `DATABASE_URL`, and `REDIS_PASSWORD` match the configured values.
- If the frontend cannot reach the backend, check `CORS_ORIGIN` and `SERVER_*` variables.
- Use `docker compose -f patchmon.yml logs <service>` to inspect specific service logs.

---

## 📚 Additional resources

- Backend image: `ghcr.io/patchmon/patchmon-backend`
- Frontend image: `ghcr.io/patchmon/patchmon-frontend`

---
