# AI Agent Dev Environment

[![Validate Docker Compose](https://github.com/ariejan/ai-agent-stack/actions/workflows/validate-docker-compose.yml/badge.svg)](https://github.com/ariejan/ai-agent-stack/actions/workflows/validate-docker-compose.yml)
[![MIT License](https://img.shields.io/github/license/ariejan/ai-agent-stack)](https://github.com/ariejan/ai-agent-stack/blob/main/LICENSE)
![Docker](https://img.shields.io/badge/docker-ready-blue?logo=docker)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat-square)](https://github.com/ariejan/ai-agent-stack/compare)
[![Last Commit](https://img.shields.io/github/last-commit/ariejan/ai-agent-stack)](https://github.com/ariejan/ai-agent-stack/commits/main)

## 🌐 Service URLs (Default)

- MinIO: [http://localhost:9000](http://localhost:9000)
- n8n: [http://localhost:5678](http://localhost:5678)

---

## 🔑 Connection Info for Tools

Below are the default connection details for each core service. Adjust as needed in your `.env` file.

### n8n
- **Host:** `localhost`
- **Port:** `5678`
- **Database:** PostgreSQL (shared with other services)
- **Example connection string:**
  - Web UI: [http://localhost:5678](http://localhost:5678)

### PostgreSQL (pgvector)
- **Credential:** `Postgres`
- **Host:** `postgres`
- **Port:** `5432`
- **Username:** `postgres`
- **Password:** `postgres`
- **Database:** `vector_db`


### MinIO (Bitnami)
- **Credential:** `S3 Account`
- **Endpoint:** `http://localhost:9000`
- **Region:** Does not matter
- **Access Key ID:** `minioadmin`
- **Secret Access Key:** `minioadmin`
- **Force Path Style:** `true`
- **Ignore SSL Issues (insecure):** `true`
- **Default buckets:** `my-bucket` (auto-created on first run, change via `MINIO_DEFAULT_BUCKETS`)


### Redis
- **Credential:** `Redis`
- **Password:** `redispassword`
- **User:** `<empty>`
- **Host:** `redis`
- **Port:** `6379`
- **Database Number:** `0`

### Qdrant
- **Credential:** `QdrantApi`
- **Qdrant URL:** `http://localhost:6333`
- **API Key:** `changeme`

---

This repository provides a one-stop Docker Compose setup for local AI Agent development on macOS (M1/ARM64). All core components—vector stores, databases, caches, and orchestration frameworks—are containerized and wired together. Tweak a few top-level environment variables, then spin up/down with a single command.

---

## 🚀 Project Overview

- **Vector Stores**: Qdrant + PostgreSQL (pgvector extension)
- **Databases & Caches**: Redis (session/state), MinIO (S3-like object store)
- **Workflow Automation**: n8n (with persistent storage, PostgreSQL backend)

All services expose HTTP endpoints on localhost, isolating dependencies and ensuring reproducible setups across your team.

---

## 🔧 Prerequisites

- Docker Desktop (with experimental ARM64 support)
- `docker-compose` CLI (v2+)
- Git (to clone this repo)

Ensure Docker is running before proceeding.

---

## 🛠️ Services Included & Ports

| Service               | Image                                  | Port | Description                                   |
| --------------------- | -------------------------------------- | ---- | --------------------------------------------- |
| MinIO                 | `bitnami/minio:latest`                | 9000 | S3-compatible object storage                  |
| PostgreSQL + pgvector | `ankane/pgvector:postgres`             | 5432 | Relational DB with embedding support          |
| Qdrant                | `qdrant/qdrant:latest`                 | 6333 | Vector search engine (API key enforced)       |
| Redis                 | `redis:7-alpine`                       | 6379 | In-memory cache/pub-sub for sessions/workflow |
| n8n                   | `n8n/n8n:latest`                       | 5678 | Workflow automation                           |

---

## ⚙️ Environment Variables

All environment variables and their defaults are now defined in the `.env` file in the project root. This file is loaded automatically by Docker Compose and can also be auto-loaded in your shell with [direnv](https://direnv.net/).

- Copy `.env.example` to `.env` and adjust as needed.
- **Do not commit your `.env` file**—it is gitignored by default.
- You may override any variable by exporting it in your shell before running Docker Compose.

```dotenv
# .env.example
MINIO_ROOT_USER=minioadmin
MINIO_ROOT_PASSWORD=minioadmin
POSTGRES_USER=postgres
POSTGRES_PASSWORD=postgres
POSTGRES_DB=vector_db
REDIS_PASSWORD=redispassword
QDRANT__SERVICE__API_KEY=changeme
OPENAI_API_KEY=your_openai_key_here
```

> **Tip:** Keep `.env` out of version control if it contains secrets. Use `.env.example` as a template for teammates.

---

## ▶️ Usage

1. **Build & start** all services:

   ```bash
   docker-compose up --build -d
   ```

2. **View logs** (tail all):

   ```bash
   docker-compose logs -f
   ```

3. **Access UIs**:

   - MinIO: [http://localhost:9000](http://localhost:9000)
   - n8n: [http://localhost:5678](http://localhost:5678)


4. **Stop & remove**:

   ```bash
   docker-compose down
   ```

---

## 🔄 Customization & Extensions

- **Add Ollama**: Uncomment or add the `ollama` service snippet in `docker-compose.yml`.
- **SSL / Reverse Proxy**: Plug in an Nginx or Caddy service in front of HTTP ports.
- **Model Volumes**: Mount alternative model directories under `./models`.
- **Env vars**: Change any default at the top without diving into each service block.

---

## 📂 Directory Structure

```
├── agent/                  # (Optional) custom agent code + Dockerfile

├── .env                    # Your local overrides (ignored by default)
└── docker-compose.yml      # This orchestration setup
```

---

## 🐞 Troubleshooting

- **Container fails to start?** Check `docker-compose logs <service>` for errors (e.g., credential mismatches).
- **Port conflicts?** Modify host-side ports (e.g., `"9001:9000"`).
- **Data persistence issues?** Ensure Docker volumes (`minio-data`, `postgres-data`, etc.) aren’t corrupted.
- **Platform mismatch warning?** Some images may not have ARM64 support. Use `--platform linux/amd64` or seek ARM-compatible images.
- **direnv not loading .env?** Run `direnv allow .` in your project directory.

---

Happy developing! 🚀
