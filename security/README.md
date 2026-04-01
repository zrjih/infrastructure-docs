# Security Playbook

## Table of Contents

1. [Access Control Policy](#access-control-policy)
2. [Secrets Management](#secrets-management)
3. [Key Rotation Policy](#key-rotation-policy)
4. [Audit & Logging](#audit--logging)
5. [Network Security](#network-security)

---

## Access Control Policy

### Principle of Least Privilege

- Each application runs in its own Docker container with no host access
- MongoDB instances are not exposed publicly (internal Docker ports only)
- Admin access restricted by IP whitelist (`150.228.0.0/16`) on sensitive sites
- VPN access available for remote management (`10.8.0.0/24`)

### SSH Access

| Rule | Value |
|------|-------|
| Protocol | SSH (Port 22) |
| Root Login | Enabled (password) |
| Key Auth | Configured (GitHub deploy keys) |
| Password Auth | Enabled for `root` and `yvon` |

### Recommended Hardening (TODO)

- [ ] Disable root password login, use SSH keys only
- [ ] Change SSH port from 22 to non-standard
- [ ] Implement fail2ban for brute-force protection
- [ ] Set up 2FA for SSH access

---

## Secrets Management

### Current State

| Secret Type | Storage | Status |
|------------|---------|--------|
| VPS SSH Keys | GitHub Secrets (`VPS_SSH_KEY`) | ✅ Secured |
| GitHub PAT | Local environment only | ⚠️ Rotate regularly |
| MongoDB | Internal Docker (no password) | ⚠️ Add auth |
| Grafana | Default admin/admin | ⚠️ Change immediately |
| JWT Secrets | Docker compose env vars | ✅ Per-app unique |
| SSL Certs | Docker volume `nginx_certs` | ✅ Auto-managed by Certbot |

### GitHub Secrets Configuration

Each repo should have:

| Secret | Purpose |
|--------|---------|
| `VPS_SSH_KEY` | SSH private key for CI/CD auto-deploy |

### Recommended Improvements

1. **Short-term:** Move all env vars to `.env` files (not committed to git)
2. **Medium-term:** Implement HashiCorp Vault for centralized secrets
3. **Long-term:** Integrate Vault with CI/CD pipeline for dynamic secrets

---

## Key Rotation Policy

| Asset | Rotation Frequency | Procedure |
|-------|-------------------|-----------|
| VPS Root Password | Every 90 days | Change via `passwd root` |
| GitHub PAT | Every 60 days | Regenerate in GitHub Settings |
| JWT Secrets | On rotation cycle | Update in docker-compose, rebuild |
| SSL Certificates | Auto (90 days) | Certbot auto-renew |
| Grafana Password | Every 90 days | Change in Grafana UI |
| SSH Keys | Every 180 days | Regenerate, update GitHub Secrets |

---

## Audit & Logging

### Container Logs

All containers output to Docker's logging driver:

```bash
# View logs
docker logs <container> --tail 100

# Follow logs
docker logs <container> -f

# All container logs with timestamps
docker logs <container> --timestamps
```

### Application Audit Logs

Applications with security modules log to `/app/audit.log` inside containers:

```bash
docker exec <backend-container> cat /app/audit.log
```

### Nginx Access Logs

```bash
docker exec nginx_proxy cat /var/log/nginx/access.log
docker exec nginx_proxy cat /var/log/nginx/error.log
```

### Recommended: Centralized Logging

- [ ] Deploy ELK Stack (Elasticsearch + Logstash + Kibana) or Loki + Grafana
- [ ] Configure Docker logging driver to forward to centralized system
- [ ] Set up log retention policy (30 days minimum)

---

## Network Security

### Firewall Rules (nginx level)

| Site | Policy | Allowed |
|------|--------|---------|
| p-rax.io | Restricted | VPN + Admin IP only |
| kamachgroup.com | Restricted | Owner-provided IP whitelist only |
| 2bintec.com | Restricted | Owner-provided IP whitelist only |
| grxpro.com | Public | All |
| khordia.com | Public | All |
| prospcho.com | Public | All |

### Docker Network Isolation

- Containers on `net_apps` cannot reach containers on `net_secure`
- `nginx_proxy` is the only container connected to multiple networks
- MongoDB containers have no public port mapping (internal only)

### Open Ports (VPS Level)

See: [Port Registry](port-registry.md)
