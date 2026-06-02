# Network Lab Automation with Nornir MCP

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)
[![Containerlab](https://img.shields.io/badge/Containerlab-0.73-green.svg)](https://containerlab.dev/)
[![Python](https://img.shields.io/badge/Python-3.11+-yellow.svg)](https://www.python.org/)

Enterprise-grade network lab automation using Containerlab and Nornir, controllable via MCP (Model Context Protocol).

This lab is used to test the [Nornir MCP Server](https://github.com/sydasif/nornir-mcp-server) after any new changes.

## Overview

This project provides a reproducible network topology for testing and automation, featuring:

- **1 Cisco CSR1000v Router** (R1) - IOS-XE 17.3.5
- **1 Arista cEOS Switch** (S1) - EOS 4.32.0F

The environment is designed for network automation testing with support for multi-vendor configurations.

## Topology

```
                    Management Network (192.168.10.0/24)
                    ┌─────────────────────────────────┐
                    │                                 │
              ┌─────┴─────┐                           │
              │           │                           │
           ┌──┴───┐   ┌──┴───┐                        │
           │  R1  │   │  S1  │                        │
           │ IOS  │   │ EOS  │                        │
           └──┬───┘   └──┬───┘                        │
              │           │                           │
              └───────────┴───────────────────────────┘
```

### Management IP Addresses

| Device | Hostname | IP Address    | Platform   | OS Version |
| ------ | -------- | ------------- | ---------- | ---------- |
| Router | r1       | 192.168.10.10 | cisco_ios  | 17.3.5     |
| Switch | s1       | 192.168.10.11 | arista_eos | 4.32.0F    |

## Prerequisites

| Requirement    | Version | Description             |
| -------------- | ------- | ----------------------- |
| Containerlab   | 0.50+   | Lab topology deployment |
| Docker         | 20.10+  | Container runtime       |
| Python         | 3.11+   | Runtime environment     |
| Docker Compose | 2.0+    | Container orchestration |

### Python Dependencies

```txt
nornir>=3.0
nornir-napalm>=0.3
nornir-netmiko>=0.3
netmiko>=4.0
napalm>=4.0
pyyaml>=6.0
```

## Quick Start

### 1. Deploy the Lab

```bash
# Deploy all containers
containerlab deploy -t lab.clab.yaml

# Verify deployment
containerlab inspect -t lab.clab.yaml
```

### 2. Enable Management APIs

```bash
# Enable eAPI on Arista devices (required for NAPALM)
docker exec clab-cisco_lab-s1 bash -c "echo -e 'enable\nconfigure\nmanagement api http-commands\nno shut' | /usr/bin/Cli"
```

### 3. Verify Connectivity

```bash
# Test SSH connectivity
ssh admin@192.168.10.10   # R1
ssh admin@192.168.10.11   # S1

# Default credentials: admin/admin
```

## MCP Tools

This project exposes Nornir functionality via MCP (Model Context Protocol) for integration with LLM-based assistants.

### Available Tools

| Tool                    | Description                                      | Filters                   |
| ----------------------- | ------------------------------------------------ | ------------------------- |
| `get_device_facts`      | Retrieve device information (vendor, OS, uptime) | hostname, group, platform |
| `list_network_devices`  | List inventory with details                      | query_type, details       |
| `get_interfaces`        | Get interface status and details                 | hostname, group, platform |
| `get_interfaces_ip`     | Get IP addressing information                    | hostname, group, platform |
| `get_device_configs`    | Retrieve running/startup configs                 | hostname, group, platform |
| `get_bgp_neighbors`     | Get BGP neighbor information                     | hostname, group, platform |
| `run_show_commands`     | Execute show commands via SSH                    | hostname, group, platform |
| `send_config_commands`  | Send configuration changes                       | hostname, group, platform |
| `backup_device_configs` | Save configs to local disk                       | hostname, group, platform |
| `run_napalm_getter`     | Execute NAPALM getters                           | getters, getters_options  |

### Filter Parameters

All tools support filtering by:

- `hostname`: Specific device IP address
- `group`: Device group (e.g., `cisco`, `arista`)
- `platform`: Network OS type (e.g., `eos`, `ios`)

## Project Structure

```
.
├── config.yaml              # Nornir configuration
├── lab.clab.yaml            # Containerlab topology
├── inventory/
│   ├── hosts.yaml           # Device definitions
│   ├── groups.yaml          # Group configurations
│   └── defaults.yaml        # Global defaults
├── backups/                 # Config backup directory
└── README.md
```

### Inventory Configuration

**hosts.yaml** - Individual device parameters:

```yaml
R1:
  hostname: 192.168.10.11
  groups: [cisco]
  data:
    role: router
    site: lab
```

**groups.yaml** - Platform-specific settings:

```yaml
cisco:
  platform: ios
  connection_options:
    netmiko:
      platform: cisco_ios
      extras:
        conn_timeout: 10
```

**defaults.yaml** - Global credentials:

```yaml
username: admin
password: admin
```

## Troubleshooting

### Connection Issues

```bash
# Check container status
docker ps | grep clab-

# View container logs
docker logs clab-cisco_lab-r1

# Test network connectivity
ping 192.168.10.11

# Check SSH access
ssh -v admin@192.168.10.11
```

### Lab Management

```bash
# Stop the lab
containerlab destroy -t lab.clab.yaml

# Redeploy (destroy + deploy)
containerlab redeploy -t lab.clab.yaml

# Clean up completely
docker stop $(docker ps -q --filter "name=clab-")
docker system prune -f
```

### Common Issues

| Issue                            | Solution                                          |
| -------------------------------- | ------------------------------------------------- |
| eAPI connection refused (Arista) | Enable with `management api http-commands`        |
| Authentication failed            | Set `NR_NORNIR_USERNAME` and `NR_NORNIR_PASSWORD` |
| Container not found              | Run `containerlab deploy -t lab.clab.yaml`        |
| Timeout errors                   | Increase `conn_timeout` in groups.yaml            |

## Security

> **Warning**: This is a lab environment. Never expose credentials in production.

Credentials are managed via environment variables:

```bash
export NR_NORNIR_USERNAME=admin
export NR_NORNIR_PASSWORD=admin
```

For production deployments, use secret management solutions (Vault, AWS Secrets Manager, etc.).

## License

MIT License - See [LICENSE](LICENSE) for details.

## Contributing

1. Fork the repository
2. Create a feature branch
3. Submit a pull request

## References

- [Containerlab Documentation](https://containerlab.dev/)
- [Nornir Documentation](https://nornir.readthedocs.io/)
- [NAPALM Documentation](https://napalm-automation.net/)
- [Netmiko Documentation](https://ktbyers.github.io/netmiko/)
