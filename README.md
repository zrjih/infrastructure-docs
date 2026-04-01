# 2BINTEC Infrastructure Documentation

> **Owner:** Yvon Kamach  
> **Managed by:** Zohir Rayhan (DevOps Engineer)  
> **Last Updated:** 2026-04-01

## Repository Structure

```
infrastructure-docs/
├── architecture/       # System architecture, network topology, service inventory
├── runbook/           # Deployment procedures, incident management
├── security/          # Access control, secrets, port registry, audit
├── cicd/              # CI/CD pipeline documentation, rollback procedures
├── backup/            # Backup strategy, restore procedures
├── monitoring/        # Monitoring stack, KPI dashboards
└── governance/        # Weekly reports, governance procedures
```

## Quick Links

| Document | Description |
|----------|-------------|
| [Architecture Overview](architecture/README.md) | System architecture + diagram |
| [Service Inventory](architecture/service-inventory.md) | All deployed services |
| [Network Topology](architecture/network-topology.md) | Network zones + segmentation |
| [DevOps Runbook](runbook/README.md) | Deployment + incident management |
| [Security Playbook](security/README.md) | Access control + key management |
| [Access Registry](security/access-registry.md) | All accounts + permissions |
| [Port Registry](security/port-registry.md) | Open ports + justification |
| [CI/CD Pipeline](cicd/README.md) | Pipeline stages + rollback |
| [Backup & Recovery](backup/README.md) | Backup strategy + restore |
| [Monitoring & KPIs](monitoring/README.md) | Dashboard + alerting |
| [Governance](governance/README.md) | Reporting + procedures |

## Infrastructure Summary

| Item | Value |
|------|-------|
| **VPS Provider** | OVH |
| **Server** | vps-06e8dcac.vps.ovh.net |
| **IP** | 141.95.159.128 |
| **OS** | Ubuntu (Linux 5.15) |
| **Storage** | 160GB SSD |
| **Docker** | Compose v3.8 |
| **Reverse Proxy** | nginx:alpine |
| **Monitoring** | Grafana + Prometheus |
| **SSL** | Let's Encrypt (Certbot) |
| **CI/CD** | GitHub Actions |
| **Active Services** | 20+ containers |
| **Active Domains** | 5 (SSL) |

## Ownership

All infrastructure, accounts, credentials, and documentation are **100% owned by Yvon Kamach**. No exclusive or undocumented access is retained by any individual.
