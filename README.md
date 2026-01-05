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

## Setup

Start the lab:

```bash
clab deploy -t lab.clab.yaml
```

Run automation tasks using Nornir with the provided configuration.

## Topology

```bash
    R1 (cEOS)
   /         \
S1 (IOL)   S2 (IOL)
```

The lab uses the following management IP addresses:

- R1: 192.168.10.10
- S1: 192.168.10.11
- S2: 192.168.10.12
