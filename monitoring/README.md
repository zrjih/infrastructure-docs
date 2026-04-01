# Monitoring & KPIs

## Monitoring Stack

| Component | Container | Port | Purpose |
|-----------|-----------|------|---------|
| **Grafana** | `monitoring-grafana` | 3000 | Visualization dashboard |
| **Prometheus** | `monitoring-prometheus` | 9090 (internal) | Metrics collection |
| **Node Exporter** | `node_exporter` | 9100 (internal) | Host-level metrics (CPU, RAM, Disk) |
| **cAdvisor** | `cadvisor` | 8080 (internal) | Container-level metrics |
| **Blackbox Exporter** | `monitoring-blackbox` | 9115 | HTTP endpoint monitoring |

## Access

| Dashboard | URL | Credentials |
|-----------|-----|-------------|
| Grafana | http://141.95.159.128:3000 | admin / admin |

## KPIs Tracked

### Infrastructure KPIs

| Metric | Source | Alert Threshold |
|--------|--------|----------------|
| CPU Usage | Node Exporter | > 80% for 5 min |
| RAM Usage | Node Exporter | > 85% for 5 min |
| Disk Usage | Node Exporter | > 90% |
| Network I/O | Node Exporter | Anomaly detection |

### Application KPIs

| Metric | Source | Alert Threshold |
|--------|--------|----------------|
| Container Uptime | cAdvisor | Any container down |
| Container CPU | cAdvisor | > 50% per container |
| Container Memory | cAdvisor | > 512MB per container |
| HTTP Response Code | Blackbox Exporter | Non-200 response |
| Endpoint Latency | Blackbox Exporter | > 2 seconds |

### Business KPIs

| Metric | Source | Report Frequency |
|--------|--------|-----------------|
| Total Active Containers | Docker API | Weekly |
| SSL Certificate Expiry | Certbot | Weekly |
| Deployment Frequency | GitHub Actions | Weekly |
| Incident Count | Manual tracking | Weekly |

## Grafana Dashboards

### 1. Infrastructure Overview
- CPU usage (1h, 24h, 7d)
- RAM usage (1h, 24h, 7d)
- Disk space remaining
- Network throughput

### 2. Application Health
- Container status (up/down)
- Container resource consumption
- Restart count

### 3. Endpoint Monitoring
- HTTP response codes per endpoint
- Response time per endpoint
- Uptime percentage

## Prometheus Configuration

Location: `/opt/2bintec/infra/monitoring_stack/prometheus.yml`

```yaml
scrape_configs:
  - job_name: 'vps_infra'
    static_configs:
      - targets:
          - node_exporter:9100
          - cadvisor:8080
```

## Alerting (Recommended)

Currently alerts are not configured. Recommended setup:

1. **Grafana Alerts** → Email notification
2. **Uptime Kuma** (self-hosted) for external endpoint monitoring
3. **Webhook alerts** to Slack/Discord/Email

## Weekly Report Template

See: [Governance Weekly Report](../governance/README.md)
