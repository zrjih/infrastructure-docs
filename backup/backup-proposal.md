# Backup Infrastructure Proposal

> **Prepared by:** Zohir Rayhan (DevOps Engineer)  
> **Date:** April 1, 2026  
> **Status:** Awaiting Approval

---

## Executive Summary

Currently, all application data (MongoDB databases, SSL certificates, nginx configurations) resides solely on the production VPS. **If the production server fails, all data is permanently lost** — there is no off-site backup.

This proposal recommends procuring a **dedicated backup VPS** in a separate datacenter to ensure business continuity and disaster recovery.

---

## Current Risk Assessment

| Data | Current Location | Backup? | Risk Level |
|------|-----------------|---------|------------|
| Application source code | GitHub repositories | ✅ Cloud-hosted | 🟢 Low |
| Docker Compose configs | GitHub repositories | ✅ Cloud-hosted | 🟢 Low |
| MongoDB databases (23 apps) | Docker volumes on production VPS | ❌ None | 🔴 Critical |
| Nginx domain configs (6 domains) | Production VPS | ❌ None | 🔴 Critical |
| SSL certificates (5 domains) | Docker volume | ❌ None | 🟡 Medium (regenerable) |
| Grafana dashboards + configs | Docker volume on production VPS | ❌ None | 🟡 Medium |

**Bottom line:** If the VPS goes down permanently, we lose all user data, database records, and configurations.

---

## Proposed Solution

### Dedicated Backup Server

| Item | Specification |
|------|--------------|
| **Type** | OVH VPS (minimal — backup storage only) |
| **Plan** | VPS Starter or equivalent (~€3–5/month) |
| **Storage** | 20–40 GB SSD (sufficient for compressed DB dumps) |
| **Location** | Different OVH datacenter from production |
| **Access** | SSH key-only authentication (no password) |
| **Purpose** | Off-site automated backup storage |

### Why a Separate Server?

> Storing backups on the same machine as production data defeats the purpose of backups. If the server is compromised, corrupted, or suffers hardware failure, both production AND backup data are lost simultaneously.

---

## Backup Architecture

```
┌───────────────────────┐                ┌───────────────────────┐
│   PRODUCTION VPS      │    encrypted   │    BACKUP VPS         │
│   (current server)    │    transfer    │    (new server)       │
│                       │ ─────────────▶ │                       │
│  MongoDB (23 apps)    │    daily @2AM  │  /backups/daily/      │
│  Nginx configs        │                │  /backups/weekly/     │
│  SSL certificates     │                │  /backups/monthly/    │
│  Monitoring configs   │                │                       │
└───────────────────────┘                └───────────────────────┘
```

---

## Backup Schedule

| Data Type | Frequency | Retention | Est. Size |
|-----------|-----------|-----------|-----------|
| MongoDB databases | **Daily** (2:00 AM UTC) | 7 days rolling | ~200MB |
| Nginx configs | **Daily** | 30 days | <1MB |
| SSL certificates | **Weekly** | 90 days | <1MB |
| Docker Compose files | **Daily** | 30 days | <1MB |
| Full snapshot | **Monthly** | 6 months | ~250MB |

**Estimated total storage:** <5GB (well within a 20GB VPS)

---

## Recovery Scenarios

### Scenario 1: Single Database Recovery
- **Time:** ~5 minutes
- **Process:** Download backup dump → restore into target container → verify data integrity

### Scenario 2: Nginx/SSL Config Recovery
- **Time:** ~2 minutes
- **Process:** Download configs from backup → extract to production paths → reload nginx

### Scenario 3: Full Disaster Recovery (New VPS)
- **Time:** ~2 hours
- **Process:**
  1. Provision new OVH VPS
  2. Install Docker + create Docker networks
  3. Clone all repos from GitHub
  4. Restore nginx configs + SSL certs from backup
  5. Build and start all containers
  6. Restore MongoDB data from backup
  7. Update DNS A records to new IP
  8. Verify all services

---

## Implementation Timeline

| Phase | Task | Duration |
|-------|------|----------|
| 1 | Procure backup VPS (OVH) | 1 day |
| 2 | Configure secure access between servers | 30 minutes |
| 3 | Deploy automated backup system | 1 hour |
| 4 | Schedule automated daily execution | 15 minutes |
| 5 | Run first full backup + verify | 30 minutes |
| 6 | Test full restore procedure | 1 hour |
| 7 | Update access registry with backup credentials | 15 minutes |
| **Total** | | **~1 working day** |

---

## Cost

| Item | Monthly Cost |
|------|-------------|
| Backup VPS (OVH Starter) | ~€3–5 |
| **Total additional cost** | **~€3–5/month** |

> This is a minimal investment for protecting all application data, user records, and configurations across 23+ applications.

---

## Action Required

To proceed, we need:

1. ✅ **Approval** to procure the backup VPS (~€3–5/month)
2. ✅ **Decision** on which OVH datacenter (should differ from current production datacenter)
3. ✅ **Confirmation** of backup retention periods (proposed: 7 days daily, 6 months monthly)

Once approved, implementation can be completed within **1 working day**.

---

## Summary

| Current State | Proposed State |
|--------------|---------------|
| ❌ No off-site backup | ✅ Daily automated off-site backup |
| ❌ Full data loss risk | ✅ Recovery within 2 hours |
| ❌ No restore procedure | ✅ Documented + tested restore |
| ❌ Single point of failure | ✅ Redundant data storage |
| — | 💰 Cost: ~€3–5/month |
