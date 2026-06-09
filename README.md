# Dynatrace Extension 2.0 — Proxmox VE (Dynatrace Managed)

This is the **Dynatrace Managed** version of the Proxmox VE Extension 2.0. It targets older Managed deployments (not 3rd-gen SaaS) and uses the **classic Dynatrace dashboard JSON format** instead of the `version: 21` DQL format.

For the SaaS / 3rd-gen version see: [proxmox-extension](https://github.com/faultylogic/proxmox-extension)

## Key differences from the SaaS version

| | SaaS version | This (Managed) version |
|---|---|---|
| `minDynatraceVersion` | `1.333` | `1.250` |
| Dashboard format | `version: 21` DQL tiles | Classic `DATA_EXPLORER` tiles |
| Dashboard variables | Cascading variable selectors | No variables — all data split by dimension; filter via native Dynatrace UI |

## Metrics collected

Identical to the SaaS version. Full metric list is in `extension/extension.yaml`. Highlights:

- **Cluster**: nodes online/total, HA quorate, unprotected guests
- **Nodes**: CPU usage/iowait, load average, memory, swap, root disk, network, ZFS ARC, Linux PSI pressure, services, updates, tasks, uptime
- **Physical disks**: SMART health and key SMART attributes
- **VMs**: status, CPU, memory (used/total/host/balloon), disk I/O and used, network, PSI pressure, snapshot count
- **VM Guest Agent**: in-guest filesystem usage, NIC RX/TX/errors/dropped
- **LXC containers**: status, CPU, memory (used/total/host), swap, disk I/O and used, network, PSI pressure, snapshot count
- **Storage**: used/available/total, backup count per storage
- **HA**: resource running state, quorum
- **Replication**: fail count, last duration, error flag
- **Ceph**: health, OSDs, capacity, I/O throughput, monitor count, flags

## Build and deploy

Requirements: Python 3.9+, `dt-sdk` CLI, a signing certificate.

```bash
# Create and activate a virtualenv
python3 -m venv .venv
source .venv/bin/activate

# Install the extension and its dependencies into the extension lib
pip install dt-extensions-sdk
pip install . --target extension/lib --no-deps

# Build the extension zip
dt-sdk build

# Sign and upload to your Managed cluster
dt-sdk sign --target dist/custom_proxmox-1.0.0.zip --key /path/to/developer.pem
# Upload via Dynatrace UI: Settings > Monitoring > Extensions 2.0 > Upload extension
```

The extension uses **API token authentication** against the Proxmox VE API. Configure it via Settings > Monitoring > Extensions 2.0 after upload.

## Dashboards

Two dashboards are bundled:

- **Proxmox VE Overview** — all clusters, nodes, VMs, containers, storage, HA, and Ceph in one scrollable dashboard
- **Proxmox Guest Detail** — focused view of all VMs and LXC containers with guest-agent metrics

Both use the classic `DATA_EXPLORER` tile format compatible with Dynatrace Managed 1.250+.
