# Network Topology

## Docker Networks

| Network | Zone | Purpose | Connected Services |
|---------|------|---------|-------------------|
| `net_apps` | B | Standard application containers | All app frontends, backends, DBs, nginx_proxy |
| `net_secure` | C | Sensitive/IP-protected repos | kamachsystems, ixfloo |
| `net_web` | C | Public-facing corporate sites | corporate_web, nginx_proxy |
| `net_infra` | E | Infrastructure & monitoring | grafana, prometheus, node_exporter, cadvisor, nginx_proxy |
| `net_socle` | A | Base platform (reserved) | — |
| `net_ecom` | D | E-commerce (reserved) | — |

## Network Segmentation Policy

```
┌─────────────────────────────────────────────────────┐
│                    VPS (141.95.159.128)              │
│                                                      │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐           │
│  │ net_web  │  │ net_apps │  │net_secure│           │
│  │ Zone C   │  │ Zone B   │  │ Zone C   │           │
│  │          │  │          │  │          │           │
│  │ Public   │  │ Standard │  │ IP-      │           │
│  │ websites │  │ apps     │  │ Sensitive│           │
│  └────┬─────┘  └────┬─────┘  └────┬─────┘           │
│       │              │              │                │
│  ┌────▼──────────────▼──────────────▼─────┐          │
│  │          nginx_proxy                    │          │
│  │    (connected to all app networks)      │          │
│  └─────────────────┬──────────────────────┘          │
│                    │                                  │
│  ┌─────────────────▼──────────────────────┐          │
│  │          net_infra (Zone E)             │          │
│  │    Monitoring + Management              │          │
│  └─────────────────────────────────────────┘          │
└──────────────────────────────────────────────────────┘
```

## Access Control (nginx)

| Type | CIDR | Purpose |
|------|------|---------|
| VPN | `10.8.0.0/24` | OpenVPN tunnel |
| Docker Internal | `172.16.0.0/12` | Inter-container communication |
| Admin Whitelist | Configured per owner request | IP provided by owner added to nginx allow list |
| Public | `0.0.0.0/0` | Denied by default on restricted sites |

## DNS Configuration

All domains use OVH DNS with A records pointing to `141.95.159.128`.

| Domain | A Record | CNAME (www) | Registrar |
|--------|----------|-------------|-----------|
| kamachgroup.com | 141.95.159.128 | — | OVH |
| grxpro.com | 141.95.159.128 | — | OVH |
| 2bintec.com | 141.95.159.128 | — | OVH |
| khordia.com | 141.95.159.128 | — | OVH |
| prospcho.com | 141.95.159.128 | — | OVH |
| p-rax.io | 141.95.159.128 | — | OVH |
