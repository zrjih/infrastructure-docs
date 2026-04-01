# Domain Setup Guide

## Prerequisites

| Item | Value |
|------|-------|
| **VPS IP** | `141.95.159.128` |
| **Nginx configs (host)** | `/opt/2bintec/infra/nginx/conf.d/` |
| **SSL certs volume** | `nginx_certs` → `/etc/letsencrypt` |
| **Web root volume** | `nginx_web_root` → `/var/www/html` |
| **Nginx container** | `nginx_proxy` |

## Step 1: DNS Configuration

Add records in domain registrar (OVH):

| Type | Name | Value |
|------|------|-------|
| A | `@` | `141.95.159.128` |
| CNAME | `www` | `yourdomain.com` |

Verify:
```bash
dig +short yourdomain.com
dig +short www.yourdomain.com
```

## Step 2: Create HTTP Nginx Config

```bash
cat > /opt/2bintec/infra/nginx/conf.d/yourdomain.conf << 'EOF'
server {
    listen 80;
    server_name yourdomain.com www.yourdomain.com;
    location ~ /.well-known/acme-challenge/ { root /var/www/html; }
    location / {
        proxy_pass http://FRONTEND_CONTAINER:80;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
EOF
docker exec nginx_proxy nginx -s reload
```

## Step 3: Get SSL Certificate

```bash
docker run --rm \
  -v nginx_certs:/etc/letsencrypt \
  -v nginx_web_root:/var/www/html \
  certbot/certbot certonly \
  --webroot -w /var/www/html \
  -d yourdomain.com -d www.yourdomain.com \
  --email admin@zohirrayhan.me \
  --agree-tos --non-interactive
```

## Step 4: Update to HTTPS

```bash
cat > /opt/2bintec/infra/nginx/conf.d/yourdomain.conf << 'EOF'
server {
    listen 80;
    server_name yourdomain.com www.yourdomain.com;
    location ~ /.well-known/acme-challenge/ { root /var/www/html; }
    location / { return 301 https://$host$request_uri; }
}

server {
    listen 443 ssl;
    server_name yourdomain.com www.yourdomain.com;
    ssl_certificate /etc/letsencrypt/live/yourdomain.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/yourdomain.com/privkey.pem;

    location / {
        proxy_pass http://FRONTEND_CONTAINER:80;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    location /api/ {
        proxy_pass http://BACKEND_CONTAINER:8000/api/;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
EOF
docker exec nginx_proxy nginx -s reload
```

## Step 5: Verify

```bash
curl -I https://yourdomain.com
docker exec nginx_proxy nginx -t
```

## Certificate Renewal

```bash
docker run --rm \
  -v nginx_certs:/etc/letsencrypt \
  -v nginx_web_root:/var/www/html \
  certbot/certbot renew
docker exec nginx_proxy nginx -s reload
```

Auto-renewal cron:
```
0 3 1 * * docker run --rm -v nginx_certs:/etc/letsencrypt -v nginx_web_root:/var/www/html certbot/certbot renew && docker exec nginx_proxy nginx -s reload
```
