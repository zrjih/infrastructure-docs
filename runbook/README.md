# DevOps Runbook

## Table of Contents

1. [Deployment Procedure](#deployment-procedure)
2. [Incident Management](#incident-management)
3. [Common Operations](#common-operations)
4. [Troubleshooting Guide](#troubleshooting-guide)

---

## Deployment Procedure

### Standard Application Deployment (New Repo)

**Pre-requisites:**
- Push access to the GitHub repository
- VPS SSH access (root@141.95.159.128)
- Personal Access Token (PAT)

**Steps:**

1. **Clone locally & analyze**
   ```bash
   git clone https://<PAT>@github.com/yvonkamach-commits/<repo>.git
   ```

2. **Create governance file** (`repo.classification.yml`)
   ```yaml
   classification: BETA
   zone: B
   network: net_apps
   type: APP
   sensitivity: STANDARD
   owner: yvonkamach-commits
   ```

3. **Create backend Dockerfile**
   ```dockerfile
   FROM python:3.11-slim
   WORKDIR /app
   COPY requirements.txt .
   RUN pip install --no-cache-dir -r requirements.txt
   COPY . .
   EXPOSE 8000
   CMD ["uvicorn", "server:app", "--host", "0.0.0.0", "--port", "8000"]
   ```

4. **Create frontend Dockerfile**
   ```dockerfile
   FROM node:20-alpine AS build
   WORKDIR /app
   COPY package.json yarn.lock* ./
   RUN yarn install --ignore-engines
   COPY . .
   ENV REACT_APP_BACKEND_URL=
   RUN yarn build
   FROM nginx:alpine
   COPY --from=build /app/build /usr/share/nginx/html
   RUN echo 'server { listen 80; server_name localhost; root /usr/share/nginx/html; index index.html; location / { try_files $uri $uri/ /index.html; } }' > /etc/nginx/conf.d/default.conf
   EXPOSE 80
   CMD ["nginx", "-g", "daemon off;"]
   ```

5. **Create docker-compose.yml** (allocate next available ports)

6. **Handle emergentintegrations dependency**
   - Comment out `emergentintegrations==0.1.0` in `requirements.txt`
   - Copy mock package from existing deployment

7. **Remove Emergent branding** from `frontend/public/index.html`

8. **Create CI/CD workflow** (`.github/workflows/deploy.yml`)

9. **Push to GitHub**
   ```bash
   git add . && git -c commit.gpgsign=false commit -m "deploy: Dockerization" && git push origin main
   ```

10. **Deploy on VPS**
    ```bash
    mkdir -p /srv/apps/<app>
    cd /srv/apps/<app>
    git clone https://<PAT>@github.com/yvonkamach-commits/<repo>.git .
    docker compose up -d --build
    docker logs <app>-backend --tail 20
    ```

### Domain + HTTPS Setup

See: [Domain Setup Guide](../runbook/domain-setup.md)

### Rollback Procedure

```bash
# 1. Identify the last working commit
cd /srv/apps/<app>
git log --oneline -5

# 2. Rollback to previous commit
git checkout <commit-hash>

# 3. Rebuild
docker compose up -d --build

# 4. Verify
docker logs <app>-backend --tail 20
curl -I http://141.95.159.128:<port>
```

---

## Incident Management

### Severity Levels

| Level | Description | Response Time | Example |
|-------|------------|---------------|---------|
| P1 - Critical | Service down, all users affected | < 15 min | nginx_proxy down, VPS unreachable |
| P2 - High | Single service down | < 30 min | App backend crash loop |
| P3 - Medium | Degraded performance | < 2 hours | High CPU, slow response |
| P4 - Low | Minor issue, no impact | < 24 hours | Log warning, cosmetic bug |

### Incident Response Steps

1. **Identify** — Check monitoring dashboard, container logs
2. **Contain** — Isolate affected service if needed
3. **Diagnose** — `docker logs <container>`, check resource usage
4. **Fix** — Apply patch, rollback, or restart
5. **Verify** — Confirm service restored
6. **Document** — Log incident in weekly report

### Quick Diagnostics

```bash
# Container status
docker ps -a

# Container logs
docker logs <container> --tail 50

# Resource usage
docker stats --no-stream

# Disk usage
df -h

# Network connectivity
docker exec nginx_proxy ping -c1 <container>

# Nginx status
docker exec nginx_proxy nginx -t
```

---

## Common Operations

### Restart a service
```bash
cd /srv/apps/<app>
docker compose restart
```

### Update a service (pull latest code)
```bash
cd /srv/apps/<app>
git pull origin main
docker compose up -d --build
```

### View all containers
```bash
docker ps -a --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"
```

### Renew SSL certificates
```bash
docker run --rm \
  -v nginx_certs:/etc/letsencrypt \
  -v nginx_web_root:/var/www/html \
  certbot/certbot renew
docker exec nginx_proxy nginx -s reload
```

### Add domain to existing app
See: [Domain Setup Guide](domain-setup.md)
