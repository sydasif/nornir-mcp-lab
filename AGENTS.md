# AGENTS.md

## Lab Topology

- **R1**: Arista cEOS (192.168.10.10) - requires `management api http-commands` enabled for NAPALM
- **S1/S2**: Cisco IOL (192.168.10.11/192.168.10.12)

## Container Management

```bash
# Deploy lab
containerlab deploy -t lab.clab.yaml

# Enable eAPI on R1 (required for NAPALM getters)
docker exec clab-cisco_lab-r1 bash -c "echo -e 'enable\nconfigure\nmanagement api http-commands\nno shut' | /usr/bin/Cli"

# Inspect lab
containerlab inspect -t lab.clab.yaml

# Destroy lab
containerlab destroy -t lab.clab.yaml
```

## Credentials

Credentials are in `inventory/defaults.yaml` (admin/admin). For MCP tools, the Nornir MCP server handles authentication automatically.

## Nornir Config

- Config: `config.yaml`
- Inventory: `inventory/` (hosts.yaml, groups.yaml, defaults.yaml)

## MCP Tools

All tools support filtering by `hostname`, `group`, or `platform`:
- `get_device_facts` / `list_network_devices` / `get_interfaces`
- `get_interfaces_ip` / `get_device_configs` / `get_bgp_neighbors`
- `run_show_commands` / `send_config_commands`
- `backup_device_configs` / `run_napalm_getter`

## Important Notes

- R1 (Arista) BGP getters will fail if BGP is not configured (expected error: "BGP inactive")
- NAPALM on R1 requires eAPI enabled; SSH fallback via Netmiko works for show commands
- Backups saved to `backups/` with timestamp format: `{hostname}_{YYYYMMDD}_{HHMMSS}.cfg`
