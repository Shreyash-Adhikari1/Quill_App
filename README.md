# Quill

A secure note-sharing and social platform built with Express, Next.js, and MongoDB.

## Prerequisites

- Docker Desktop
- mkcert

## Setup

### 1. Generate TLS Certificates

Install mkcert, then create local HTTPS certificates:

```powershell
mkcert -install
New-Item -ItemType Directory -Force Quill_Backend\certs
New-Item -ItemType Directory -Force Quill_Frontend\certs
mkcert -key-file Quill_Backend\certs\server.key -cert-file Quill_Backend\certs\server.crt localhost
mkcert -key-file Quill_Frontend\certs\server.key -cert-file Quill_Frontend\certs\server.crt localhost
```

The backend reads `Quill_Backend/certs/server.key` and `Quill_Backend/certs/server.crt` at startup. These files are mounted read-only into Docker so private keys are not baked into images.

### 2. Configure Environment Variables

```powershell
Copy-Item .env.example .env
```

Open `.env` and fill in all blank values. Keep `.env` local because it contains database, JWT, session, OAuth, and email secrets.

### 3. Start the Application

```powershell
docker compose up --build
```

This starts MongoDB, the HTTPS backend, and the Next.js frontend together.

### 4. Access the Application

- Frontend: `https://localhost:3000`
- Backend API: `https://localhost:5000/api`
- Backend health check: `https://localhost:5000/api/health`

## Useful Commands

```powershell
# Run in the background
docker compose up --build -d

# View all logs
docker compose logs -f

# View one service
docker compose logs -f backend

# Stop containers while keeping MongoDB data
docker compose down

# Stop containers and wipe MongoDB data
docker compose down -v

# Rebuild only the backend
docker compose up --build backend
```

## Docker Layout

- `docker-compose.yml` orchestrates MongoDB, backend, and frontend.
- `Quill_Backend/Dockerfile` builds the Express API into a non-root production container.
- `Quill_Frontend/Dockerfile` builds the Next.js standalone server into a non-root production container.
- Docker volumes persist MongoDB data, backend logs, and uploaded files across rebuilds.
