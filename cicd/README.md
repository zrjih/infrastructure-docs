# CI/CD Pipeline Documentation

## Overview

All repositories use **GitHub Actions** for automated CI/CD. The pipeline follows a 3-stage process:

```
Governance Check → IP Evidence → Auto Deploy
```

## Pipeline Stages

### Stage 1: Governance Check

Verifies that `repo.classification.yml` exists in the repository root. This ensures every deployed application has been classified by zone and sensitivity level.

```yaml
governance-check:
  runs-on: ubuntu-latest
  steps:
    - uses: actions/checkout@v4
    - name: Verify Classification
      run: |
        if [ ! -f "repo.classification.yml" ]; then
          echo "ERROR: repo.classification.yml missing"
          exit 1
        fi
```

### Stage 2: IP Evidence

Generates a cryptographic proof of the deployment for intellectual property audit trail.

```yaml
ip-evidence:
  runs-on: ubuntu-latest
  needs: governance-check
  steps:
    - uses: actions/checkout@v4
    - name: Generate IP Evidence
      run: |
        echo "Repository: ${{ github.repository }}" > ip_evidence.txt
        echo "Commit: ${{ github.sha }}" >> ip_evidence.txt
        echo "Timestamp: $(date -u)" >> ip_evidence.txt
        echo "Author: ${{ github.actor }}" >> ip_evidence.txt
        sha256sum ip_evidence.txt
    - uses: actions/upload-artifact@v4
      with:
        name: ip-evidence
        path: ip_evidence.txt
```

### Stage 3: Deploy

SSH into VPS and pull latest code, rebuild containers.

```yaml
deploy:
  runs-on: ubuntu-latest
  needs: ip-evidence
  steps:
    - name: Deploy to VPS
      uses: appleboy/ssh-action@v1.0.0
      with:
        host: 141.95.159.128
        username: root
        key: ${{ secrets.VPS_SSH_KEY }}
        script: |
          cd /srv/apps/<app>
          git pull origin main
          docker compose up -d --build
```

## Required Secrets

Each repository needs:

| Secret | Value | Where to Configure |
|--------|-------|-------------------|
| `VPS_SSH_KEY` | SSH private key for root@141.95.159.128 | GitHub → Repo → Settings → Secrets |

## Trigger

Pipeline triggers on every push to `main` branch:
```yaml
on:
  push:
    branches: [main]
```

## Rollback Procedure

### Quick Rollback (< 2 minutes)

```bash
# SSH into VPS
ssh root@141.95.159.128

# Go to app directory
cd /srv/apps/<app>

# View recent commits
git log --oneline -5

# Rollback to previous commit
git checkout <commit-hash>

# Rebuild
docker compose up -d --build

# Verify
docker logs <app>-backend --tail 20
```

### Full Rollback (if image is corrupted)

```bash
# Stop containers
docker compose down

# Remove images
docker rmi $(docker images -q <app>-*)

# Reset to known good commit
git reset --hard <commit-hash>

# Full rebuild
docker compose up -d --build
```

### Rollback via GitHub

1. Go to the repository on GitHub
2. Revert the problematic commit (create revert PR)
3. Merge the revert → CI/CD auto-deploys the fix

## Pipeline Status

Check pipeline status at:
```
https://github.com/yvonkamach-commits/<repo>/actions
```

## Adding CI/CD to a New Repository

1. Create `.github/workflows/deploy.yml` (use template above)
2. Add `VPS_SSH_KEY` secret in GitHub repo settings
3. Push to `main` — pipeline runs automatically
