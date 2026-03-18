# Conduit App – DevOps Assessment

A containerised deployment of the [RealWorld](https://github.com/gothinkster/realworld) example app using a Go/Gin backend and Angular frontend.

## Architecture Overview

- **Frontend**: Angular app served via Nginx
- **Backend**: Go Gin REST API
- **Database**: SQLite (persisted via Docker volume)
- **Reverse Proxy**: Nginx routes traffic based on domain name
- **CI/CD**: GitHub Actions builds Docker images on every push
```
Browser → Nginx → sam.nqb8.co     → Angular (port 80)
                → api.sam.nqb8.co → Go API (port 8080)
                                  → SQLite (volume)
```

## Prerequisites

- Go 1.23+
- Node.js 18+
- Docker & Docker Compose
- GCC (for CGO/SQLite on Windows: install via MSYS2)

## Run Locally

### Backend
```bash
cd golang-gin-realworld-example-app
cp .env.example .env
mkdir -p data
CGO_ENABLED=1 go run hello.go
```

### Frontend
```bash
cd angular-realworld-example-app
npm install --legacy-peer-deps
ng serve
```

## Run with Docker
```bash
docker compose up -d
```

## Deployment
```bash
ssh sam@144.21.53.104
git clone https://github.com/Sammy-samzy/devops-assessment.git
cd devops-assessment
docker compose up -d
```

## Public URLs

- Frontend: http://sam.nqb8.co
- Backend: http://api.sam.nqb8.co

## Decisions

- **SQLite over PostgreSQL**: The app already used SQLite via GORM, keeping it avoids adding an extra container dependency
- **Multi-stage Docker builds**: Keeps final images small by separating build and runtime environments
- **Nginx as reverse proxy**: Single entry point routing traffic to frontend and backend by domain name
- **Legacy peer deps flag**: The Angular project has a known dependency conflict with jasmine-core, resolved with --legacy-peer-deps
- **CGO enabled**: SQLite requires CGO; GCC is installed in the builder stage to support this
**SQLite with persistent volume**: The app's GORM setup is built around SQLite. 
  Rather than rewriting the database layer, SQLite is run inside Docker with a 
  named volume (`conduit_sqlite_data`) to persist data across container restarts. 
  This is sufficient for this deployment scale.