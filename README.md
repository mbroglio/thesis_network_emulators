# Thesis Network Emulators

This repository collects network emulation scenarios used for a thesis-level comparison of:

- Mininet
- Containernet
- Kathara

The scenarios cover recurring networking topics such as static/dynamic routing, DNS hierarchies, BGP, load balancing, and performance logging.

## Repository Layout

- `Containernet/`
	- `DNS Simple/`: single DNS server plus clients
	- `DNS Kathara/`: multi-level DNS hierarchy (root, TLD, authoritative, web servers, clients)
	- `start.sh`: helper script to run a Containernet topology
- `Kathara/`
	- `ThesisLabs/`: scenario-based labs (BGP, DNS, OSPF, RIP, LoadBalancer, etc.)
	- `Performance Test/`: usage logging and graph generation
- `Mininet/`
	- `Thesis Scenarios/`: scenario-based Mininet equivalents
	- `Performance Test/`: usage logging and graph generation

## Requirements

Install and configure the following on Linux:

- Python 3.8+
- Docker
- Open vSwitch
- Mininet
- Containernet
- Kathara

Python packages used by graph scripts:

- matplotlib

Example installation for plotting:

```bash
python3 -m pip install matplotlib
```

## Quick Start

### 1) Containernet DNS Simple

```bash
cd "Containernet/DNS Simple"
chmod +x build-images.sh
./build-images.sh
sudo python3 containernet_dns_topo.py
```

### 2) Containernet DNS Hierarchy (Kathara-like)

```bash
cd "Containernet/DNS Kathara"
chmod +x build-images.sh
./build-images.sh
sudo python3 containernet_dns_topo.py
```

### 3) Kathara Labs

Each scenario in `Kathara/ThesisLabs/` has a `lab.conf` and startup files.

Typical flow:

```bash
cd Kathara/ThesisLabs/DNS
kathara lstart
kathara linfo
kathara connect pc1
# run tests
kathara lclean
```

### 4) Mininet Scenarios

Run the Mininet scenario scripts from the corresponding scenario folder under `Mininet/Thesis Scenarios/`.

Use `sudo` when needed by Mininet/OVS commands.

## Performance Logging and Plotting

Both Mininet and Kathara sections include tools to log resource usage over time and generate SVG graphs.

### Mininet

1. Start logging:

```bash
cd "Mininet/Performance Test"
chmod +x logger.sh
./logger.sh
```

2. Run your scenario.
3. Stop the logger (`Ctrl+C`).
4. Generate graphs:

```bash
python3 GraphCreator.py .
```

### Kathara

1. Start logging:

```bash
cd "Kathara/Performance Test"
chmod +x log_docker.sh
./log_docker.sh
```

2. Run your scenario.
3. Stop the logger (`Ctrl+C`).
4. Generate graphs:

```bash
python3 GraphCreator.py .
```

Generated graphs are saved as SVG files in output folders created by each graph script.

## Notes

- Many scenarios require root privileges due to virtual networking and namespace operations.
- Docker images for Containernet DNS scenarios must be built before launching topologies.
- If DNS tests fail, verify container IP addresses and `/etc/resolv.conf` inside emulated nodes.

## Suggested Validation Commands

Inside client nodes (Containernet/Kathara):

```bash
ping -c 3 192.168.0.5
nslookup www.example.com
dig @192.168.0.5 www.example.com
```

Adjust hostnames and DNS server IPs based on the active scenario.
