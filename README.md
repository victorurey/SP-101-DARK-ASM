# DARK Attack Surface Management
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

## Step 1: Install nmap
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

## Step 2: Install open-vas scanner
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

## Step 3: Install osquery.service
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

## Final System Check
Check for all services to be active and running
```bash
sudo systemctl status gvmd gsad ospd-openvas osqueryd
```

