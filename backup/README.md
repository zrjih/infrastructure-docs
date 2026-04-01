# Backup & Business Continuity

## Current State

| Component | Backup Status | Risk |
|-----------|--------------|------|
| Application Code | ✅ GitHub repositories (cloud) | 🟢 Low |
| Docker Compose configs | ✅ GitHub repositories (cloud) | 🟢 Low |
| Nginx configs | ❌ On production VPS only | 🔴 Critical |
| SSL Certificates | ❌ Docker volume only | 🟡 Medium |
| MongoDB Data | ❌ Docker volumes only | 🔴 Critical |
| Grafana Dashboards | ❌ On production VPS only | 🟡 Medium |
| Monitoring Config | ❌ On production VPS only | 🟡 Medium |

## Recommendation

> Storing backups on the same server as production defeats the purpose. If the VPS fails, both production data AND backups are lost.

A dedicated backup server in a separate datacenter is required. See the full proposal:

→ **[Backup Infrastructure Proposal](backup-proposal.md)**

## Backup Scope

| Data | Method | Frequency | Retention |
|------|--------|-----------|-----------|
| MongoDB dumps | Automated dump + transfer | Daily | 7 days |
| Nginx configs | Automated sync | Daily | 30 days |
| SSL certificates | Automated sync | Weekly | 90 days |
| Full snapshot | All combined | Monthly | 6 months |

## Recovery Time Objectives

| Scenario | Estimated Recovery |
|----------|-------------------|
| Single database restore | ~5 minutes |
| Nginx/SSL config restore | ~2 minutes |
| Full disaster recovery (new VPS) | ~2 hours |

## Status

⏳ **Awaiting approval** — Backup infrastructure proposal submitted for review.
