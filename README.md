# TEAM DARK Attack Surface Management
Attack Surface Management (ASM) is an approach to identifying, monitoring, and reducing entry points available to attackers. It aims to track processes running on web servers, cloud services, APIs, and internal systems that may be inadvertently exposed.

# Features
* Ubuntu Linux Virtual Machine: Runs on a virtual machine allowing flexibility and customizability
* Nmap open-port scanning: Fast, internal scanning system for detecting open ports
* OpenVAS Greenbone discovery tool: Integrated tool for showing processes running on ports
* Osquery data collection: SQL-based system for displaying important port data and process states
* External tools (Shodan): Additional scanning outside of the localhost

# Prerequisites
Before you begin, ensure you have the following installed:
* Virtual Box VM (v7.2.4) - [Download](https://www.virtualbox.org/)
* Ubuntu Linux Server (Jammy Jellyfish 22.04.5) - [Download](https://releases.ubuntu.com/jammy/)

# Quick Start Guide
Once Ubuntu Server is up and running in the virtual machine, we must install the three main programs of the system: nmap, openvas, and osquery.

### Step 1: Install nmap
Start with a system update and upgrade.
```bash
sudo apt update && sudo apt upgrade -y
```

Apt install the nmap package in its entirety.
```bash
sudo apt install nmap -y
```

Verify status
```bash
nmap -V
```

### Step 2: Install open-vas scanner
Ubuntu 22.04.5 uses the gvm package to run OpenVAS Greenbone.
Install dependencies and gvm
```bash
sudo apt install gvm -y
```

Setup gvm. This may take a few minutes and will prompt the user to create an admin username/password. Save this information for later.
```bash
sudo systemctl gvm-setup
```

Start services
```bash
sudo systemctl gvm-start
```

Verify status
```bash
sudo systemctl gvm-check-setup
```

### Step 3: Install osquery.service
Must be installed through repository.
```bash
sudo apt install gnupg -y
sudo mkdir -p /etc/apt/keyrings
sudo gpg --no-default-keyring --keyring /etc/apt/keyrings/osquery.gpg \ --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 1484120AC4E9F8A1A577AEEE97A80C63C9D8B80B
```

```bash
echo "deb [arch=amd64 signed-by=/etc/apt/keyrings/osquery.gpg] \ https://pkg.osquery.io/deb deb main" | sudo tee /etc/apt/sources.list.d/osquery.list
```

```bash
sudo apt update
sudo apt install osquery -y
```

Verify status
```bash
sudo systemctl status osqueryi
```

### Final System Check
Check for all services to be active and running
```bash
sudo systemctl status gvmd gsad ospd-openvas osqueryd
```

# How to use
### Running nmap scans
1. Update the system and check the status of programs
2. Run a simple nmap scan
```bash
sudo nmap -vv -stats-every 1s -sV -p 1-10 192.168.1.0/24
```
3. This should pull up a quick scan that outputs port info and states to the shell

### Running Osquery
1. Check all OpenVAS and Osquery programs are up and running
```bash
sudo systemctl gvm gsad ospd-openvas osqueryd
```
2. Run a query on listening ports
```bash
sudo odqueryi "SELECT pid, path, port, address, family FROM listening_ports;"
```
3. Examine query output for active ports and data bus activity

### Storing nmap into .log files
1. Check nmap status
```bash
sudo systemctl status nmap
```
2. Find local host address and save for later
```bash
ip addr all
```
3. Run command on ports 22, 80, 443, 3000 (ssh, http, https, ppp). Replace [localhost] with the address found in Step 2
```bash
sudo nmap -sV -p 22,80,443,3000 -v -stats-every 5s [localhost] -oN /tmp/demo_nmap.log
```
4. Output will show whether ports are open and closed and will display the port connection type

# Project Structure
```
DARK-ASM-SYSTEM-ARCHITECTURE
├── network-discovery/                     # External attack surface discovery layer
│   ├── nmap/                              # Port + service enumeration
│   │   └── scans/                          # Output results (XML, grepable, JSON) 
│   └── shodan/                            # (Optional) External internet asset scan
│       └── api-scripts/                    # Scripts for querying exposed assets
│
├── vulnerability-management/              # Full OpenVAS/GVM stack
│   ├── gvm/                                # Greenbone Vulnerability Manager
│   │   ├── gvmd.service                    # Manager daemon
│   │   ├── gvmd.db                         # PostgreSQL database for GVM
│   │   └── configs/                        # Scan configs, roles, permissions
│   │
│   ├── openvas-scanner/                   # OSPD-OpenVAS + Notus
│   │   ├── ospd-openvas.service            # Scanner daemon
│   │   ├── notus-scanner.service           # Lightweight fast local checks
│   │   └── /var/lib/openvas/               # Feed + signatures
│
├── endpoint-monitoring/                   # Continuous monitoring layer
│   ├── osquery/
│   │   ├── osqueryd.service                # Daemon for scheduled monitoring
│   │   ├── packs/                          # Query packs (vulns, processes, network)
│   │   └── logs/                           # Real-time or differential logs
└── └──
```
# Additional Documentation
* Nmap documentation - [View](https://nmap.org/docs.html)
* Openvas documentionn - [View](https://www.greenbone.net/en/documents/)
* Osquery documentation - [View](https://osquery.readthedocs.io/en/latest/)
* Shodan documentation - [View](https://developer.shodan.io/api)
* Graylog documentation - [View](https://go2docs.graylog.org/current/home.htm)
