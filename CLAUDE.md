# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Common Commands

### Lab Management (Containerlab)

- Deploy lab: `containerlab deploy -t lab.clab.yaml`
- Inspect lab: `containerlab inspect -t lab.clab.yaml`
- Destroy lab: `containerlab destroy -t lab.clab.yaml`
- Redeploy lab: `containerlab redeploy -t lab.clab.yaml`

### Device Configuration

- Enable eAPI on CSR1000v (required for NAPALM; cEOS enables it by default):
  `docker exec clab-cisco_lab-r1 bash -c "echo -e 'enable\nconfigure\nmanagement api http-commands\nno shut' | /usr/bin/Cli"`

### Environment Setup

- Set Nornir credentials:
  `export NR_NORNIR_USERNAME=admin`
  `export NR_NORNIR_PASSWORD=admin`

## Architecture & Structure

### Overview

The project defines a network topology consisting of one Cisco CSR1000v router (R1) and one switch: an Arista cEOS switch (S1), managed by Nornir and deployed via Containerlab.

### Key Components

- **Topology Definition**: `lab.clab.yaml` defines the Containerlab layout and node images.
- **Nornir Configuration**: `config.yaml` contains the Nornir settings.
- **Inventory System**: Located in `inventory/` - `hosts.yaml`: Individual device definitions (IPs, groups, roles). - `groups.yaml`: Platform-specific settings (OS type, connection options). - `defaults.yaml`: Global defaults and credentials.
- **Artifacts**: `backups/` stores configuration backups retrieved from devices.

### Network Layout

- Management Network: `192.168.10.0/24`
- R1 (Cisco CSR1000v): `192.168.10.10`
- S1 (Arista cEOS): `192.168.10.11`

### Interface Mapping (Cisco CSR1000v)

The Cisco CSR1000v uses a specific mapping between Containerlab `ethX` interfaces and Cisco IOS interfaces:

- `eth0` $\rightarrow$ `GigabitEthernet1` (Management) - Default IP `10.0.0.15` is assigned by QEMU DHCP.
- `eth1` $\rightarrow$ `GigabitEthernet2` (First data port)
- `eth2+` $\rightarrow$ `GigabitEthernet3+` (Subsequent data ports)

### Interface Mapping (Arista cEOS)

The Arista cEOS uses a different mapping scheme between Containerlab `ethX` interfaces and EOS interfaces:

- `eth0` $\rightarrow$ `Management0`/`Management1` (Management) — inherits Docker-assigned IP
- `eth1` $\rightarrow$ `Ethernet1` (First data port)
- `eth2+` $\rightarrow$ `Ethernet2+` (Subsequent data ports)

**User-defined mapping** (cEOS >= 4.28.0F):
Bind a custom JSON file to `/mnt/flash/EosIntfMapping.json` via `binds`:

```json
{
  "ManagementIntf": { "eth0": "Management1" },
  "EthernetIntf": {
    "eth1": "Ethernet1/1",
    "eth2": "Ethernet2/1"
  }
}
```

**`INTFTYPE=et`**: Set this env var to use `et` naming (e.g., `et1`, `et2`) instead of `eth`.

**Underscore naming**: Use underscores for breakout-style names (e.g., `eth1_1` \rightarrow `Ethernet1/1`). Incompatible with user-defined mapping.
