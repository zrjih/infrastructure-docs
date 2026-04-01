# Service Inventory

> Last Updated: 2026-04-01  
> Source: Live VPS `docker ps -a` + nginx configs

## Active Services

| # | App | Container Names | Zone | Frontend Port | Backend Port | Domain | SSL | Status |
|---|-----|----------------|------|--------------|-------------|--------|-----|--------|
| 1 | Prax (Project Radar) | `prax-frontend`, `prax-backend`, `prax-mongo` | B | 32769 | internal | Configured | ❌ | ✅ Running |
| 2 | Corporate Website | `corporate_web` | C | internal | — | Configured | ✅ | ✅ Running |
| 3 | Kando Network | `kando-beta-frontend`, `kando-beta-backend`, `kando-beta-db` | B | 8080 | 8000 | — | ❌ | ✅ Running |
| 4 | Flitflow | `flitflow-beta-frontend`, `flitflow-beta-backend`, `flitflow-beta-db` | B | 8081 | internal | — | ❌ | ⚠️ Running (BETA) |
| 5 | Become | `become-beta-frontend`, `become-beta-backend`, `become-beta-db` | B | 8082 | internal | — | ❌ | ✅ Running |
| 6 | AI2Human | `ai2human-beta-frontend`, `ai2human-beta-backend`, `ai2human-beta-db` | B | 8083 | internal | — | ❌ | ⚠️ Running (BETA) |
| 7 | ClinicCare | `cliniccare-beta-frontend`, `cliniccare-beta-backend`, `cliniccare-beta-db` | B | 8084 | internal | — | ❌ | ⚠️ Running (BETA) |
| 8 | Ze-Ra | `ze-ra-frontend`, `ze-ra-backend`, `ze-ra-db` | B | 8085 | 8005 | — | ❌ | ✅ Running |
| 9 | ADS Africa Holding | `ads-africa-holding` | C | 8086 | — | — | ❌ | ✅ Running |
| 10 | ADS RCA | `ads-rca` | C | 8087 | — | — | ❌ | ✅ Running |
| 11 | 2BINTEC App | `2bintec-frontend`, `2bintec-backend`, `2bintec-db` | B | 8088 | 8008 | — | ❌ | ✅ Running |
| 12 | MiloExplore | `miloexplore-frontend`, `miloexplore-backend`, `miloexplore-db` | B | 8089 | 8009 | — | ❌ | ✅ Running |
| 13 | CYNTIA | `cyntia-frontend`, `cyntia-backend`, `cyntia-db` | B | 8090 | 8010 | — | ❌ | ✅ Running |
| 14 | GRX Pro | `grpx-frontend`, `grpx-backend`, `grpx-db` | B | 8091 | 8011 | Configured | ✅ | ✅ Running |
| 15 | CBAO (BRG LAB) | `cbao-frontend`, `cbao-backend`, `cbao-db` | B | 8092 | 8012 | — | ❌ | ✅ Running |
| 16 | Neuramix | `neuramix-frontend`, `neuramix-backend`, `neuramix-db` | B | 8093 | internal | — | ❌ | ⚠️ Running (BETA) |
| 17 | Translatyx | `translatyx-frontend`, `translatyx-backend`, `translatyx-db` | B | 8094 | 8014 | — | ❌ | ✅ Running |
| 18 | A-POS Dashboard | `apos-dashboard` | B | 8095 | — | — | ❌ | ✅ Running |
| 19 | Kamach Systems | `kamachsystems-frontend`, `kamachsystems-backend`, `kamachsystems-db` | C | 8096 | 8016 | — | ❌ | ✅ Running |
| 20 | Ixfloo | `ixfloo-frontend`, `ixfloo-backend`, `ixfloo-db` | C | 8097 | 8017 | — | ❌ | ✅ Running |
| 21 | Khordia V3 | `khordia-frontend`, `khordia-backend`, `khordia-db` | B | 8098 | 8018 | Configured | ✅ | ✅ Running |
| 22 | Kgroup | `kgroup-frontend`, `kgroup-backend`, `kgroup-db` | B | 8099 | 8019 | Configured | ✅ | ✅ Running |
| 23 | Proscho | `proscho-frontend`, `proscho-backend`, `proscho-db` | B | 8101 | 8021 | Configured | ✅ | ✅ Running |

## Infrastructure Services

| Service | Container | Port | Network | Purpose |
|---------|-----------|------|---------|---------|
| Nginx Proxy | `nginx_proxy` | 80, 443 | net_apps, net_infra, net_web | Reverse proxy + SSL termination |
| Grafana | `monitoring-grafana` | 3000 | net_infra | KPI dashboard |
| Prometheus | `monitoring-prometheus` | 9090 (internal) | net_infra | Metrics collection |
| Node Exporter | `node_exporter` | 9100 (internal) | net_infra | Host metrics |
| cAdvisor | `cadvisor` | 8080 (internal) | net_infra | Container metrics |
| Blackbox Exporter | `monitoring-blackbox` | 9115 | net_infra | Endpoint monitoring |

## Domain → Container Routing (Nginx)

| Domain Config | Frontend Target | Backend Target | SSL | Access Control | HSTS |
|--------------|----------------|---------------|-----|---------------|------|
| `2bintec.conf` | `corporate_web:80` | — | ✅ | Restricted (owner whitelist) | ❌ |
| `grxpro.conf` | `grpx-frontend:80` | `grpx-backend:8000/` | ✅ | Public | ✅ |
| `kamachgroup.conf` | `kgroup-frontend:80` | — | ✅ | Restricted (owner whitelist) | ❌ |
| `khordia.conf` | `khordia-frontend:80` | `khordia-backend:8000/api/` | ✅ | Public | ❌ |
| `prax.conf` | `prax-frontend:80` | — | ❌ | Restricted (VPN + owner whitelist) | ❌ |
| `prospcho.conf` | `proscho-frontend:80` | `proscho-backend:8000/api/` | ✅ | Public | ❌ |

## SSL Certificates (Let's Encrypt)

| Domain | Cert Path | Status |
|--------|----------|--------|
| 2bintec.com | `/etc/letsencrypt/live/2bintec.com/` | ✅ Active |
| grxpro.com | `/etc/letsencrypt/live/grxpro.com/` | ✅ Active |
| kamachgroup.com | `/etc/letsencrypt/live/kamachgroup.com/` | ✅ Active |
| khordia.com | `/etc/letsencrypt/live/khordia.com/` | ✅ Active |
| prospcho.com | `/etc/letsencrypt/live/prospcho.com/` | ✅ Active |


## Port Allocation Map

| Range | Purpose |
|-------|---------|
| 80, 443 | Nginx Proxy (public — SSL) |
| 3000 | Grafana (monitoring) |
| 8080-8089 | Earlier app deployments |
| 8090-8099 | Recent app deployments |
| 8100+ | Latest app deployments |
| 9090, 9100, 9115 | Monitoring (internal) |

## Total Container Count

| Type | Count |
|------|-------|
| Application containers | 56 |
| Infrastructure containers | 6 |
| Stopped/exited | 1 (certbot — runs on demand) |
| **Total** | **63** |
