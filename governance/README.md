# Governance & Reporting

## Weekly Report Structure

Every week, a structured report is delivered covering:

1. **Infrastructure Health** — CPU, RAM, disk status
2. **Application Uptime** — All endpoints reachable
3. **Resource Usage** — Anomaly detection
4. **Key Deliverables** — New deployments, fixes, improvements
5. **Incidents** — Any incidents and resolution
6. **Action Items** — Upcoming planned work

## Report Delivery

| Channel | Format | Frequency |
|---------|--------|-----------|
| Email | Professional summary | Weekly (Friday) |
| Upwork | Milestone submission | Weekly |
| GitHub | This repository | Continuous |

## Repository Classification

Every deployed repository MUST contain a `repo.classification.yml` file:

```yaml
classification: BETA        # BETA, PRODUCTION
zone: B                      # A (Socle), B (Apps), C (Web/Secure), D (Ecom), E (Infra)
network: net_apps            # Docker network
type: APP                    # APP, WEB, API, STATIC
sensitivity: STANDARD        # STANDARD, IP_SENSITIVE
owner: yvonkamach-commits    # GitHub org
deployed_by: zrjih           # Operator
deployment_date: "2026-XX-XX"
```

## Deployment Checklist

Before any deployment, verify:

- [ ] `repo.classification.yml` present
- [ ] Dockerfile(s) created
- [ ] `docker-compose.yml` created
- [ ] `emergentintegrations` mock (if needed)
- [ ] Emergent branding removed
- [ ] CI/CD workflow created
- [ ] Environment variables configured
- [ ] Pushed to GitHub
- [ ] Deployed on VPS
- [ ] Container health verified
- [ ] setup_log.md updated

## Change Management

All infrastructure changes must be:

1. **Documented** — Updated in this repository
2. **Tested** — Verified before production
3. **Reversible** — Rollback procedure available
4. **Communicated** — Reported in weekly report

## Continuous Improvement

| Area | Current State | Target |
|------|--------------|--------|
| Documentation | ✅ Comprehensive | Keep updated |
| Monitoring | ✅ Grafana + Prometheus | Add alerting |
| Backup | ⚠️ No automated backup | Separate backup server |
| Security | ⚠️ Basic | Vault + key rotation |
| CI/CD | ✅ Auto-deploy | Add testing stage |
| Logging | ⚠️ Per-container | Centralized logging |
