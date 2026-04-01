# GitHub Secrets Configuration

> All secrets are configured and operational across all repositories.

## Configured Secrets

| Secret Name | Purpose | Value |
|-------------|---------|-------|
| `VPS_HOST` | Production server IP address | Server IP |
| `VPS_USER` | SSH login user | `root` |
| `VPS_SSH_KEY` | SSH private key for auto-deploy | RSA key |

## How to Update Secrets (if needed)

1. Go to the repository on GitHub
2. Navigate to **Settings** → **Secrets and variables** → **Actions**
3. Click the secret to update → **Update secret**
4. Paste the new value → **Save**

## Repository Status

All repositories have secrets configured and CI/CD pipelines active:

| Repository | `VPS_HOST` | `VPS_USER` | `VPS_SSH_KEY` |
|-----------|-----------|-----------|--------------|
| `khordia240326V3` | ✅ | ✅ | ✅ |
| `Kgroup250326` | ✅ | ✅ | ✅ |
| `proscho` | ✅ | ✅ | ✅ |
| `grxpro` | ✅ | ✅ | ✅ |
| `translatyx` | ✅ | ✅ | ✅ |
| `cbao1` | ✅ | ✅ | ✅ |
| `neuramix` | ✅ | ✅ | ✅ |
| `kamachsystems` | ✅ | ✅ | ✅ |
| `ixfloo` | ✅ | ✅ | ✅ |
| `MILOEXPLORE` | ✅ | ✅ | ✅ |
| `CYNTIA01110126` | ✅ | ✅ | ✅ |
| `Flitflow01280126` | ✅ | ✅ | ✅ |
| `ai2human01280126` | ✅ | ✅ | ✅ |
| `cliniccare01260126` | ✅ | ✅ | ✅ |
| `prax02210126` | ✅ | ✅ | ✅ |
| `ze-ra01100226` | ✅ | ✅ | ✅ |
| `2bintec01100226` | ✅ | ✅ | ✅ |
| `2bintecV1` | ✅ | ✅ | ✅ |
| `ads-africa-holding01100228` | ✅ | ✅ | ✅ |
| `ads-rca01100226` | ✅ | ✅ | ✅ |

## Security Notes

- All secrets are encrypted by GitHub — they cannot be viewed after being set
- Only repository owner and admins can manage secrets
- Secrets are injected at runtime during CI/CD pipeline execution
- No secrets are stored in plain text anywhere in the codebase
