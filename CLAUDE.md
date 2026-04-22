# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 1. Overview
This repository is a network lab automation environment using **Containerlab** for topology deployment and **Nornir** for automation tasks. It is designed to be used with MCP (Model Context Protocol) tools to allow LLM-based assistants to interact with the network devices.

## 2. Architecture
- **Topology Management:** `lab.clab.yaml` defines the Containerlab topology.
- **Network Automation:** Nornir is used to manage and configure devices.
- **Inventory:** Managed via Nornir's `SimpleInventory` plugin:
  - `inventory/hosts.yaml`: Individual device definitions.
  - `inventory/groups.yaml`: Group and platform-specific configurations.
  - `inventory/defaults.yaml`: Global credentials and defaults.
- **Configuration:** `config.yaml` contains the Nornir configuration.
- **Devices:**
  - **R1:** Arista cEOS router (IP: 192.168.10.10).
  - **S1, S2:** Cisco IOL switches (IPs: 192.168.10.11, 192.168.10.12).

## 3. Common Commands

### Lab Deployment (Containerlab)
- **Deploy lab:** `containerlab deploy -t lab.clab.yaml`
- **Verify deployment:** `containerlab inspect -t lab.clab.yaml`
- **Destroy lab:** `containerlab destroy -t lab.clab.yaml`
- **Redeploy lab:** `containerlab redeploy -t lab.clab.yaml`

### Device Management
- **Enable eAPI on R1 (Arista):**
  ```bash
  docker exec clab-cisco_lab-r1 bash -c "echo -e 'enable\nconfigure\nmanagement api http-commands\nno shut' | /usr/bin/Cli"
  ```
- **Check container status:** `docker ps | grep clab-`
- **View container logs:** `docker logs clab-cisco_lab-r1`

### Nornir/Python (if running locally)
- Ensure Nornir dependencies are installed: `nornir`, `nornir-napalm`, `nornir-netmiko`, `netmiko`, `napalm`, `pyyaml`.
- Use environment variables for credentials:
  ```bash
  export NR_NORNIR_USERNAME=admin
  export NR_NORNIR_PASSWORD=admin
  ```

## 4. Troubleshooting
- **Arista eAPI error:** Ensure eAPI is enabled on R1 via the `docker exec` command above.
- **Authentication failed:** Check `inventory/defaults.yaml` or environment variables.
- **Connection timeouts:** Check `inventory/groups.yaml` for `conn_timeout` settings.
