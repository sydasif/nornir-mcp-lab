# Network Lab Automation

This project provides network lab automation using Containerlab and Nornir.

## Overview

This lab environment creates a network topology with:

- 1 Arista cEOS router (R1)
- 2 Cisco IOL switches (S1, S2)

## Prerequisites

- Containerlab
- Docker
- Python 3.11+
- Nornir
- Netmiko, Napalm, Paramiko libraries

## Setup

Start the lab:

```bash
clab deploy -t lab.clab.yaml
```

Run automation tasks using Nornir with the provided configuration:

```bash
# Example inventory validation
python -c "from nornir import InitNornir; nr = InitNornir(config_file='config.yaml'); print(nr.inventory.hosts.keys())"
```

## Topology

```text
    R1 (cEOS)
   /         \
S1 (IOL)   S2 (IOL)
```

The lab uses the following management IP addresses:

- R1: 192.168.10.10 (Arista EOS)
- S1: 192.168.10.11 (Cisco IOS)
- S2: 192.168.10.12 (Cisco IOS)

## Inventory Structure

The inventory is organized in the `inventory/` directory:

- `hosts.yaml`: Individual device configurations
- `groups.yaml`: Device group definitions and platform-specific settings
- `defaults.yaml`: Global default parameters and connection options

Connection parameters include:

- SSH port: 22
- Default credentials: from environment variables `NR_NORNIR_USERNAME` and `NR_NORNIR_PASSWORD`
- Platform-specific settings for netmiko and napalm
- Connection timeouts and transport options

## Security

Credentials are managed through environment variables. Set the following before running automation:

```bash
export NR_NORNIR_USERNAME=admin
export NR_NORNIR_PASSWORD=admin
```
