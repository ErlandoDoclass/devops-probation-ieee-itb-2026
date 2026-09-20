# 🚀 DevOps Probation IEEE ITB 2026

A DevOps implementation for the IEEE ITB 2026 probation starter application.

This project focuses on making the application easier to run, maintain, and verify through containerization, orchestration, environment management, health checks, logging, and Continuous Integration.

---

## 📌 Project Overview

The application consists of three main services:

- **Frontend** — React + Vite
- **Backend** — Node.js + Express
- **Database** — MongoDB

All services are containerized using Docker and orchestrated using Docker Compose.

---

## 🏗️ Deployment Architecture

```text
                    Browser
                       |
          +------------+------------+
          |                         |
          v                         v
Frontend Container           Backend Container
React + Vite                 Node.js + Express
Port 5173                    Port 5000
                                    |
                                    v
                             MongoDB Container
                               Port 27017
                                    |
                                    v
                              Docker Volume
                               mongo_data
```

### Request Flow

```text
Browser
  ↓
Frontend
  ↓ API Request
Backend
  ↓
MongoDB
  ↓
Backend Response
  ↓
Frontend
```

Frontend:

```text
http://localhost:5173
```

Backend API:

```text
http://localhost:5000/api
```

Backend health endpoint:

```text
http://localhost:5000/health
```

---

## 🧰 Tech Stack

| Category | Technology |
|---|---|
| Frontend | React, Vite |
| Backend | Node.js, Express |
| Database | MongoDB |
| Containerization | Docker |
| Orchestration | Docker Compose |
| CI | GitHub Actions |
| Version Control | Git & GitHub |

---

## 📁 Project Structure

```text
devops-probation-ieee-itb-2026/
│
├── .github/
│   └── workflows/
│       └── ci.yml
│
├── backend/
│   ├── Dockerfile
│   ├── .dockerignore
│   ├── .env.example
│   └── ...
│
├── frontend/
│   ├── Dockerfile
│   ├── .dockerignore
│   ├── .env.example
│   └── ...
│
├── .gitignore
├── docker-compose.yml
└── README.md
```

---

## ⚙️ Prerequisites

Make sure the following tools are installed:

- Git
- Docker
- Docker Compose

Check Docker:

```bash
docker --version
```

Check Docker Compose:

```bash
docker compose version
```

Node.js does not need to be installed on the host machine when running the full application through Docker.

---

## 🔐 Environment Configuration

Environment variables are used so configuration is not hardcoded directly into the application source code.

### Backend

```env
PORT=5000
MONGO_URI=mongodb://mongodb:27017/ieee_devops
```

Example configuration:

```text
backend/.env.example
```

### Frontend

```env
VITE_API_URL=http://localhost:5000/api
```

Example configuration:

```text
frontend/.env.example
```

Sensitive `.env` files are excluded through `.gitignore`.

---

## ▶️ Running the Application

Clone the repository:

```bash
git clone <repository-url>
```

Enter the project directory:

```bash
cd devops-probation-ieee-itb-2026
```

Build and start all services:

```bash
docker compose up --build -d
```

Then open:

```text
http://localhost:5173
```

Check backend health:

```text
http://localhost:5000/health
```

---

## 📊 Check Service Status

```bash
docker compose ps
```

Expected services:

```text
frontend
backend
mongodb
```

The backend should eventually report a healthy status.

---

## ❤️ Health Check

The backend provides:

```text
GET /health
```

Example response:

```json
{
  "status": "healthy",
  "database": "connected"
}
```

Docker Compose also checks this endpoint automatically.

```yaml
healthcheck:
  test: ["CMD", "wget", "--spider", "-q", "http://localhost:5000/health"]
  interval: 10s
  timeout: 5s
  retries: 5
```

This allows Docker to verify that the backend is not only running, but actually responding correctly.

---

## ♻️ Reliability

The services use:

```yaml
restart: unless-stopped
```

This allows Docker to restart a service if it unexpectedly stops, unless it was manually stopped by the user.

---

## 💾 Database Persistence

MongoDB data is stored using a Docker volume:

```text
mongo_data
```

The volume is mounted to:

```text
/data/db
```

Compose configuration:

```yaml
volumes:
  - mongo_data:/data/db
```

This keeps application data available when containers are restarted or recreated.

Check available Docker volumes:

```bash
docker volume ls
```

---

## 🌐 Docker Networking

Docker Compose automatically creates a shared network for the application services.

The backend connects to MongoDB using:

```text
mongodb://mongodb:27017/ieee_devops
```

Here, `mongodb` is the Docker Compose service name.

The backend does not use:

```text
localhost:27017
```

because `localhost` inside the backend container refers to the backend container itself.

---

## 📝 Logging

View all logs:

```bash
docker compose logs
```

Backend logs:

```bash
docker compose logs backend
```

Follow backend logs:

```bash
docker compose logs -f backend
```

Frontend logs:

```bash
docker compose logs frontend
```

MongoDB logs:

```bash
docker compose logs mongodb
```

---

## 🤖 Continuous Integration

GitHub Actions workflow:

```text
.github/workflows/ci.yml
```

The CI workflow runs automatically on:

- Push
- Pull Request

### Backend Job

The backend CI job:

1. Checks out the repository
2. Sets up Node.js 20
3. Installs dependencies using:

```bash
npm ci
```

### Frontend Job

The frontend CI job:

1. Checks out the repository
2. Sets up Node.js 20
3. Installs dependencies
4. Runs ESLint
5. Builds the application

```bash
npm ci
npm run lint
npm run build
```

If any required step fails, the workflow fails.

---

## 🛑 Stop Services

```bash
docker compose down
```

MongoDB data remains available because it is stored in the Docker volume.

---

## 🔄 Restart Services

```bash
docker compose restart
```

If code or Docker configuration changes:

```bash
docker compose up --build -d
```

---

## 🛠️ Troubleshooting

### Port Already in Use

Check running containers:

```bash
docker ps
```

Stop a conflicting container:

```bash
docker stop <container-name>
```

---

### Backend Is Unhealthy

Check backend logs:

```bash
docker compose logs backend
```

Check:

```text
http://localhost:5000/health
```

Then verify service status:

```bash
docker compose ps
```

---

### MongoDB Connection Failed

Check MongoDB logs:

```bash
docker compose logs mongodb
```

Verify the backend uses:

```text
mongodb://mongodb:27017/ieee_devops
```

---

### Frontend Cannot Reach Backend

Verify:

```env
VITE_API_URL=http://localhost:5000/api
```

Then check whether the backend is healthy:

```text
http://localhost:5000/health
```

---

## ⚠️ Known Limitations

- The frontend container currently uses the Vite development server.
- The starter backend does not provide automated tests.
- Advanced centralized logging is not implemented.
- Monitoring and alerting are not implemented.
- The current environment is intended primarily for local verification rather than production deployment.

---

## 🧠 AI Usage

AI tools were used as a learning and development assistant during this assignment.

The assistance was mainly used to:

- Explain DevOps concepts such as Docker, Docker Compose, environment variables, networking, health checks, and CI.
- Assist in reviewing and debugging Docker and GitHub Actions configurations.
- Assist in preparing operational documentation.

All configurations and technical decisions were reviewed, tested, and understood before being included in the final submission.

---

## ⚡ Quick Commands

| Action | Command |
|---|---|
| Start services | `docker compose up --build -d` |
| Check status | `docker compose ps` |
| View logs | `docker compose logs` |
| Follow backend logs | `docker compose logs -f backend` |
| Restart services | `docker compose restart` |
| Stop services | `docker compose down` |
| List volumes | `docker volume ls` |

---

## ✅ Implemented DevOps Features

- Dockerized frontend
- Dockerized backend
- MongoDB container
- Docker Compose orchestration
- Environment configuration
- Secret management
- Docker networking
- Database persistence
- Health checks
- Restart policies
- Accessible logs
- GitHub Actions CI
- Operational runbook
