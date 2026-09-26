# Basic Network Scanning with Nmap

## Project Overview
This project demonstrates fundamental network scanning and reconnaissance techniques using Nmap. The objective was to scan a local, intentionally vulnerable Metasploitable 2 virtual machine from a Kali Linux attacker machine to identify open ports, running services, service versions, and the underlying operating system. 

## What is Nmap?
Nmap (Network Mapper) is a free, open-source utility for network discovery and security auditing. Security professionals use Nmap to:
- Discover live hosts on a network.
- Identify open ports and the services running on them.
- Detect operating system and service version details.
- Map network topology and identify potential attack surfaces.

## Why Network Scanning Matters
Network scanning is the foundational first step in any security assessment or penetration test. It allows security analysts to:
- Identify unauthorized or unnecessary services running on a network.
- Discover potential entry points that attackers could exploit.
- Maintain an accurate inventory of network assets and software versions.
- Verify firewall configurations and network segmentation.

## Ethical Use Guidelines
**IMPORTANT**: Only scan systems you own or have explicit written permission to scan. Unauthorized network scanning is illegal in many jurisdictions and violates computer fraud laws. For this task, I only scanned a local Metasploitable 2 VM that I own and control in an isolated Virtual Machine (VM) environment.

## Lab Setup
- **Scanner**: Kali Linux VM (Attacker)
- **Target**: Metasploitable 2 VM (Intentionally Vulnerable)
- **Network**: VMware Host-only / NAT (Isolated lab environment)
- **Target IP**: `192.168.109.129`
- **Target MAC Address**: `00:0C:29:F2:E4:EF` (VMware)

## Installation Steps
Nmap comes pre-installed on Kali Linux. To verify the installation, the following command was used:
```bash
nmap --version
