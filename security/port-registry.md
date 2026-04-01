# Port Registry

> All open ports on VPS `141.95.159.128` with justification.

## Public Ports (Internet-Facing)

| Port | Protocol | Service | Justification |
|------|----------|---------|---------------|
| 22 | TCP | SSH | Server administration |
| 80 | TCP | nginx_proxy | HTTP (redirects to HTTPS) |
| 443 | TCP | nginx_proxy | HTTPS (SSL termination) |

## Application Ports (Direct IP Access)

| Port | Container | App | Justification |
|------|-----------|-----|---------------|
| 3000 | monitoring-grafana | Grafana | KPI monitoring dashboard |
| 8080 | kando-beta-frontend | Kando Network | Frontend access |
| 8081 | flitflow-beta-frontend | Flitflow | Frontend access |
| 8082 | become-beta-frontend | Become | Frontend access |
| 8083 | ai2human-beta-frontend | AI2Human | Frontend access |
| 8084 | cliniccare-beta-frontend | ClinicCare | Frontend access |
| 8085 | ze-ra-frontend | Ze-Ra | Frontend access |
| 8086 | ads-africa-holding | ADS Africa | Static site |
| 8087 | ads-rca | ADS RCA | Static site |
| 8088 | 2bintec-frontend | 2BINTEC App | Frontend access |
| 8089 | miloexplore-frontend | MiloExplore | Frontend access |
| 8090 | cyntia-frontend | CYNTIA | Frontend access |
| 8091 | grpx-frontend | GRX Pro | Frontend access |
| 8092 | cbao-frontend | CBAO | Frontend access |
| 8093 | neuramix-frontend | Neuramix | Frontend access |
| 8094 | translatyx-frontend | Translatyx | Frontend access |
| 8095 | apos-dashboard | A-POS Dashboard | Portfolio management |
| 8096 | kamachsystems-frontend | Kamach Systems | Frontend access |
| 8097 | ixfloo-frontend | Ixfloo | Frontend access |
| 8098 | khordia-frontend | Khordia V3 | Frontend access |
| 8099 | kgroup-frontend | Kgroup | Frontend access |
| 8101 | proscho-frontend | Proscho | Frontend access |
| 8000 | kando-beta-backend | Kando Network | API |
| 8005 | ze-ra-backend | Ze-Ra | API |
| 8008 | 2bintec-backend | 2BINTEC App | API |
| 8009 | miloexplore-backend | MiloExplore | API |
| 8010 | cyntia-backend | CYNTIA | API |
| 8011 | grpx-backend | GRX Pro | API |
| 8012 | cbao-backend | CBAO | API |
| 8014 | translatyx-backend | Translatyx | API |
| 8016 | kamachsystems-backend | Kamach Systems | API |
| 8017 | ixfloo-backend | Ixfloo | API |
| 8018 | khordia-backend | Khordia V3 | API |
| 8019 | kgroup-backend | Kgroup | API |
| 8021 | proscho-backend | Proscho | API |

## Internal Ports (Not Exposed Publicly)

| Port | Service | Purpose |
|------|---------|---------|
| 27017 | MongoDB (multiple) | Database (Docker internal only) |
| 9090 | Prometheus | Metrics scraping |
| 9100 | Node Exporter | Host metrics |
| 9115 | Blackbox Exporter | Endpoint monitoring |

## Recommendation

Backend API ports (8000-8021) are currently exposed directly on the host. For improved security:
- Route all API traffic through nginx reverse proxy (via `/api/` paths)
- Remove direct host port mappings for backend containers
- Only expose frontend ports + nginx (80/443)
