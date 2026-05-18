# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Common Commands

### Lab Management (Containerlab)

- Deploy lab: `containerlab deploy -t lab.clab.yaml`
- Inspect lab: `containerlab inspect -t lab.clab.yaml`
- Destroy lab: `containerlab destroy -t lab.clab.yaml`
- Redeploy lab: `containerlab redeploy -t lab.clab.yaml`

### Device Configuration

- Enable Arista eAPI (required for NAPALM):
  `docker exec clab-cisco_lab-r1 bash -c "echo -e 'enable\nconfigure\nmanagement api http-commands\nno shut' | /usr/bin/Cli"`

### Environment Setup

- Set Nornir credentials:
  `export NR_NORNIR_USERNAME=admin`
  `export NR_NORNIR_PASSWORD=admin`

## Architecture & Structure

### Overview

The project defines a network topology consisting of one Arista cEOS router (R1) and two Cisco IOL switches (S1, S2), managed by Nornir and deployed via Containerlab.

### Key Components

- **Topology Definition**: `lab.clab.yaml` defines the Containerlab layout and node images.
- **Nornir Configuration**: `config.yaml` contains the Nornir settings.
- **Inventory System**: Located in `inventory/`
      - `hosts.yaml`: Individual device definitions (IPs, groups, roles).
      - `groups.yaml`: Platform-specific settings (OS type, connection options).
      - `defaults.yaml`: Global defaults and credentials.
- **Artifacts**: `backups/` stores configuration backups retrieved from devices.

### Network Layout

- Management Network: `192.168.10.0/24`
- R1 (Arista cEOS): `192.168.10.10`
- S1 (Cisco IOL): `192.168.10.11`
- S2 (Cisco IOL): `192.168.10.12`
