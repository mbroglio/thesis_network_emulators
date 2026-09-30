# Network Emulation Platforms: Comparative Analysis & Benchmarking

[![Linux](https://img.shields.io/badge/Platform-Linux-FCC624?logo=linux&logoColor=black)](https://www.kernel.org/)
[![Python](https://img.shields.io/badge/Python-3.8%2B-blue.svg?logo=python&logoColor=white)](https://www.python.org/)
[![Docker](https://img.shields.io/badge/Docker-20.x%2B-2496ED?logo=docker&logoColor=white)](https://www.docker.com/)
[![Mininet](https://img.shields.io/badge/Emulator-Mininet-brightgreen.svg)](http://mininet.org/)
[![Kathara](https://img.shields.io/badge/Emulator-Kathara-blueviolet.svg)](https://www.kathara.net/)
[![Containernet](https://img.shields.io/badge/Emulator-Containernet-red.svg)](https://containernet.github.io/)
[![FRRouting](https://img.shields.io/badge/Routing-FRRouting-lightgrey.svg)](https://frrouting.org/)
[![Thesis PDF](https://img.shields.io/badge/Thesis-PDF_Report-red?logo=adobeacrobatreader&logoColor=white)](Broglio_Matteo_RelFin_LT_25072025.pdf)

> Experimental framework, network topologies, and performance benchmarking testbed developed for the thesis **"Comparative Study of Network Emulators: A Detailed Analysis of Mininet and Kathara"** at Università degli Studi di Milano - Bicocca.

---

## 📋 Table of Contents
- [Thesis Report](#-thesis-report)
- [Project Overview](#-project-overview)
- [Emulators Comparison](#-emulators-comparison)
- [Emulation Scenarios](#-emulation-scenarios)
  - [Routing Protocols (BGP, OSPF, RIP, Static)](#1-routing-protocols)
  - [Hierarchical DNS Architecture](#2-hierarchical-dns-architecture)
  - [Load Balancing & High Availability](#3-load-balancing--high-availability)
  - [Complex Multi-Path Scenarios](#4-complex-multi-path-scenarios)
- [Performance Benchmarking Framework](#-performance-benchmarking-framework)
- [Prerequisites & System Setup](#-prerequisites--system-setup)
- [Execution Guide](#-execution-guide)
  - [1. Mininet Scenarios](#1-mininet-scenarios)
  - [2. Kathara Labs](#2-kathara-labs)
  - [3. Containernet Topologies](#3-containernet-topologies)
  - [4. Running Resource Usage Benchmarks](#4-running-resource-usage-benchmarks)
- [Troubleshooting & Housekeeping](#-troubleshooting--housekeeping)
- [Repository Structure](#-repository-structure)
- [Academic Credits & Citation](#-academic-credits--citation)

---

## 📄 Thesis Report

The complete thesis document is available in this repository:
- 📥 **[Download / View Thesis PDF (Broglio_Matteo_RelFin_LT_25072025.pdf)](Broglio_Matteo_RelFin_LT_25072025.pdf)**

### Thesis Details
- **Title**: *Comparative Study of Network Emulators: A Detailed Analysis of Mininet and Kathara*
- **Author**: Matteo Broglio (Matricola `899562`)
- **Supervisor (Relatore)**: Prof. Marco Savi
- **Co-Supervisor (Correlatore)**: Dott. Jacopo Talpini
- **Institution**: Università degli Studi di Milano - Bicocca (UNIMIB)
- **Department**: Dipartimento di Informatica, Sistemistica e Comunicazione (DISCo)
- **Academic Year**: 2024 - 2025

### Abstract & Core Findings
Network emulation is critical for testing, research, and education by replicating realistic network behaviors inside controlled sandbox environments. This work analyzes architectural designs, deployment overhead, runtime scalability, and usability across **Mininet**, **Kathara**, and their intersection in **Containernet**:
- **Mininet**: Delivers outstanding resource efficiency and sub-second startup times by exploiting Linux kernel network namespaces (`netns`) and Open vSwitch (OVS). Ideal for large-scale routing graphs on constrained hardware, though sharing the underlying host filesystem and kernel.
- **Kathara**: Leverages containerization (Docker) and virtual collision domains to enforce strict isolation, cross-platform portability (Linux, macOS, Windows), and reproducible lab environments, at the expense of higher CPU and memory overhead during container lifecycle events.
- **Containernet**: Acts as a bridge, embedding Docker containers into Mininet's programmable OpenFlow data plane.

---

## 🌐 Project Overview

This repository provides an empirical, reproducible testbed implementing functionally equivalent networking scenarios across all three emulators.

Key objectives:
1. **Functional Equivalence**: Verifying identical behavior and convergence times across emulators for standard L2/L3 services, dynamic routing, and DNS resolution.
2. **Protocol Integration**: Deploying production-grade protocol stacks including **FRRouting (FRR)** for BGP/OSPF/RIP and **BIND9** for hierarchical DNS.
3. **Resource Profiling**: Measuring and visualizing real-time CPU utilization (`%CPU`) and virtual memory footprint (`VSZ`, `%MEM`) under automated benchmarking runs.

---

## ⚖️ Emulators Comparison

| Dimension | Mininet | Kathara | Containernet |
| :--- | :--- | :--- | :--- |
| **Virtualization Unit** | Linux Network Namespaces (`netns`) | Docker Containers | Docker Containers & Host Processes |
| **Data Plane / Switching** | Open vSwitch (OVS) / Linux Bridges | Software Collision Domains (Bridges) | Open vSwitch (OVS) / Linux Bridges |
| **Routing Suite** | Host FRRouting (multi-instance `/etc/frr`) | `kathara/frr` Docker Image | Container-embedded daemons |
| **SDN / OpenFlow** | Native, full OpenFlow support | No native OpenFlow (L2/L3 focused) | Full OpenFlow via OVS |
| **Application Isolation** | Shared filesystem, isolated network | Completely isolated container filesystems | Completely isolated container filesystems |
| **Topology Definition** | Python API (`Mininet()`) | Declarative configuration (`lab.conf`) | Python API (`Containernet()`) |
| **Resource Overhead** | Minimal CPU and memory footprint | Moderate (container lifecycle overhead) | Moderate (Docker + OVS orchestration) |

---

## 🛠️ Emulation Scenarios

All scenarios are duplicated or functionally mapped across platforms:

| Scenario | Category | Description | Mininet | Kathara | Containernet |
| :--- | :--- | :--- | :---: | :---: | :---: |
| **TwoHosts** | Baseline | Two nodes connected via L2 switch; baseline latency and resource consumption | ✅ | ✅ | — |
| **Client - Server / WebServer** | Application | Client host interacting with an HTTP web server | ✅ | ✅ | — |
| **StaticRouting** | L3 Forwarding | Multi-hop network with static routes configured across gateways | ✅ | ✅ | — |
| **RIP** | Dynamic Routing | Distance-vector routing with FRRouting `ripd` across redundant links | ✅ | ✅ | — |
| **OSPF** | Dynamic Routing | Link-state routing with FRRouting `ospfd` across multi-router mesh | ✅ | ✅ | — |
| **BGP** | Exterior Gateway | Inter-AS eBGP peering (AS 10 & AS 20) exchanging internal prefixes | ✅ | ✅ | — |
| **DNS (Simple & Hierarchical)** | Network Services | Complete DNS resolution hierarchy from Root to TLD to Authoritative | ✅ | ✅ | ✅ |
| **LoadBalancer** | High Availability | L4/L7 load distribution balancing requests across 3 backend web servers | ✅ | ✅ | — |
| **Multiple Solutions** | Complex Mesh | Multi-AS network with multiple paths, dynamic routing, and redundant services | ✅ | ✅ | — |

### 1. Routing Protocols
Routing scenarios in Mininet and Kathara rely on **FRRouting (FRR)**:
- **RIP**: Demonstrates automatic route convergence and recovery upon link failure.
- **OSPF**: Validates link-state database synchronization and shortest path computation.
- **BGP**: Establishes autonomous system peering between `as10r1` (AS 10) and `as20r1` (AS 20), announcing customer and server subnets.

### 2. Hierarchical DNS Architecture
The DNS lab replicates the global domain name resolution hierarchy:
```
                      [ Root DNS (.) ]
                        /          \
              [ TLD .it ]          [ TLD .com ]
                   |                    |
        [ unimib.it (Auth) ]    [ amazon.com (Auth) ]
                 |                      |
          [ Web Server ]         [ Web Server ]
```
- **Kathara & Containernet**: Deploys independent BIND9 DNS servers (`dnsroot`, `dnsit`, `dnscom`, `dnsunimib`, `dnsamazon`) and HTTP servers (`wsunimib`, `wsamazon`).
- Validated via recursive queries issued from client nodes using `dig` and `nslookup`.

### 3. Load Balancing & High Availability
- A dedicated reverse proxy / load balancer distributes incoming HTTP client traffic across three distinct backend servers (`ws1`, `ws2`, `ws3`).

### 4. Complex Multi-Path Scenarios
- High-density topology featuring multi-homed routers (`as20r1` to `as20r5`), dynamic rerouting, and policy routing.

---

## 📊 Performance Benchmarking Framework

To quantify the runtime footprint of each emulator, automated monitoring tools track resource consumption:

- **Logging Scripts**:
  - `Mininet/Performance Test/logger.sh`: Polls processes matching `python`, `frr`, `mininet`, `ovs`, `iperf` every second.
  - `Kathara/Performance Test/log_docker.sh`: Polls processes matching `kathara` and `docker` every second.
  - Recorded metrics: Process ID, `%CPU`, `%MEM`, Virtual Memory Size (`VSZ` in KB), and process command.
- **Graph Generator (`GraphCreator.py`)**:
  - Parses time-series log files (`mininet_usage.log`, `kathara_usage.log`).
  - Computes aggregated CPU and Memory curves relative to experiment execution time.
  - Exports publication-ready SVG charts (`*_cpu_usage.svg`, `*_memory_usage.svg`).

---

## 💻 Prerequisites & System Setup

A Linux system (Ubuntu 20.04/22.04 LTS recommended) with root privileges is required.

### 1. System Packages & Python
```bash
sudo apt-get update
sudo apt-get install -y python3 python3-pip python3-venv openvswitch-switch frr docker.io
python3 -m pip install matplotlib
```

### 2. Emulator Installations
- **Mininet**:
  ```bash
  sudo apt-get install -y mininet
  ```
- **Kathara**:
  Follow instructions at [Kathara Official Documentation](https://www.kathara.net/):
  ```bash
  sudo add-apt-repository ppa:kathara/ppa
  sudo apt-get update
  sudo apt-get install -y kathara
  ```
- **Containernet**:
  Follow installation steps at [Containernet GitHub](https://github.com/containernet/containernet).

---

## 🚀 Execution Guide

### 1. Mininet Scenarios

#### Standard Topologies (e.g., Static Routing)
```bash
cd "Mininet/Thesis Scenarios/StaticRouting"
sudo python3 StaticRouting.py
```

#### FRR-based Topologies (BGP, OSPF, RIP)
Prior to launching routing scenarios, initialize the FRR daemon directories:
```bash
cd "Mininet/Thesis Scenarios/BGP"

# 1. Setup FRR node directories and permissions
chmod +x config_frr.sh
sudo ./config_frr.sh

# 2. Run the Mininet topology
sudo python3 BGP.py
```

### 2. Kathara Labs

Kathara scenarios reside in `Kathara/ThesisLabs/<scenario>/`:
```bash
cd "Kathara/ThesisLabs/DNS"

# Start the lab (deploys containers and connects collision domains)
kathara lstart

# Inspect active nodes
kathara linfo

# Open a shell on the client PC
kathara connect pc1

# Inside pc1: test DNS resolution
dig @192.168.0.5 www.unimib.it
ping -c 3 192.168.0.2

# Clean up the lab
kathara lclean
```

### 3. Containernet Topologies

#### Simple DNS
```bash
cd "Containernet/DNS Simple"
chmod +x build-images.sh
./build-images.sh
sudo python3 containernet_dns_topo.py
```

#### Hierarchical DNS (Kathara-like)
```bash
cd "Containernet/DNS Kathara"
chmod +x build-images.sh
./build-images.sh
sudo python3 containernet_dns_topo.py
```

### 4. Running Resource Usage Benchmarks

1. **Launch the logger in background**:
   ```bash
   cd "Mininet/Performance Test"   # or "Kathara/Performance Test"
   chmod +x logger.sh
   ./logger.sh &
   LOGGER_PID=$!
   ```

2. **Execute your scenario** (e.g., run `BGP.py` or `kathara lstart`).
3. **Terminate logging**:
   ```bash
   kill -SIGINT $LOGGER_PID
   ```
4. **Generate vector performance plots**:
   ```bash
   python3 GraphCreator.py .
   ```
   Outputs will be saved as SVG files in the current folder.

---

## 🧹 Troubleshooting & Housekeeping

Because emulation tools allocate virtual interfaces, kernel namespaces, and software switches, cleanup commands are essential when topologies exit unexpectedly:

- **Clean Mininet & Open vSwitch State**:
  ```bash
  sudo mn -c
  sudo killall -9 frr
  ```
- **Clean Kathara & Docker Containers**:
  ```bash
  kathara wipe -f
  # Or brute-force purge running emulator containers:
  docker rm -f $(docker ps -aq --filter "name=kathara") 2>/dev/null || true
  ```
- **Containernet Cleanup**:
  ```bash
  sudo mn -c
  docker rm -f $(docker ps -aq --filter "name=containernet") 2>/dev/null || true
  ```

---

## 📁 Repository Structure

```text
├── Broglio_Matteo_RelFin_LT_25072025.pdf  # Full Thesis Report PDF
├── README.md                              # Comprehensive Documentation
│
├── Containernet/
│   ├── DNS Kathara/                       # Multi-level hierarchical DNS (Root, TLD, Auth, Web)
│   │   ├── build-images.sh                # Docker build script for BIND9 container images
│   │   └── containernet_dns_topo.py
│   ├── DNS Simple/                        # Single-server DNS testbed
│   │   ├── build-images.sh
│   │   └── containernet_dns_topo.py
│   └── start.sh                           # Helper startup script
│
├── Kathara/
│   ├── Performance Test/                  # Logging daemon and plotting script
│   │   ├── log_docker.sh
│   │   └── GraphCreator.py
│   └── ThesisLabs/                        # Kathara declarative network labs
│       ├── BGP/                           # eBGP inter-AS lab with FRR
│       ├── Client - Server/               # Web server lab
│       ├── DNS/                           # Hierarchical BIND9 DNS lab
│       ├── LoadBalancer/                  # Multi-backend HTTP load balancer
│       ├── MultipleSolutions/             # Multi-path resilient routing mesh
│       ├── OSPF/                          # Multi-router OSPF mesh
│       ├── RIP/                           # Dynamic RIP routing
│       ├── StaticRouting/                 # Multi-hop static routing
│       ├── TwoHosts/                      # Two hosts baseline lab
│       └── WebServer/                     # HTTP server lab
│
└── Mininet/
    ├── Performance Test/                  # Process monitor and SVG graph generator
    │   ├── logger.sh
    │   └── GraphCreator.py
    └── Thesis Scenarios/                  # Mininet Python-driven topologies
        ├── BGP/                           # FRR eBGP topology
        ├── Client - Server/               # HTTP client-server scenario
        ├── DNS/                           # Local DNS resolution
        ├── LoadBalancer/                  # L4/L7 load balancer scenario
        ├── Multiple Solutions/            # Multi-AS topology with FRR routing
        ├── OSPF/                          # FRR OSPF topology
        ├── RIP/                           # FRR RIP topology
        ├── StaticRouting/                 # Static IP forwarding topology
        └── TwoHosts/                      # Two hosts switched topology
```

---

## 🎓 Academic Credits & Citation

This research was conducted as part of the Final Examination for the Bachelor's Degree in Computer Science (**Laurea Triennale in Informatica**) at **Università degli Studi di Milano - Bicocca**:

- **Candidate**: Matteo Broglio
- **Advisor**: Prof. Marco Savi
- **Co-Advisor**: Dott. Jacopo Talpini
- **Department**: Dipartimento di Informatica, Sistemistica e Comunicazione (DISCo)
- **Academic Year**: 2024 / 2025
