# Network Design

## Overview

This homelab is an isolated virtual network designed for learning Windows Active Directory administration and cybersecurity. The environment provides a controlled space for configuring enterprise services, managing domain users and endpoints, and performing security testing without interacting with devices on my normal home network.

The lab currently runs in VMware Workstation on an HP Z2 workstation.

## Network Architecture

**Network:** `10.10.10.0/24`  
**VMware Network:** `VMnet2`  
**Addressing:** Static IPv4  
**DHCP:** Disabled  
**Active Directory Domain:** `corp.lab`

### Current Hosts

| Hostname | Operating System | IP Address | Role |
|---|---|---|---|
| DC01 | Windows Server 2025 | `10.10.10.10` | Domain Controller / DNS |
| KALI01 | Kali Linux | `10.10.10.20` | Security testing workstation |
| LAB-PC01 | Windows 11 Enterprise | `10.10.10.30` | Domain-joined workstation |

## Network Layout

```text
                    VMnet2
                10.10.10.0/24
                       |
        +--------------+--------------+
        |              |              |
        |              |              |
      DC01           KALI01         LAB-PC01
   10.10.10.10    10.10.10.20    10.10.10.30
        |              |              |
 Windows Server     Kali Linux     Windows 11
   2022 DC          Security       Enterprise
        |
    AD DS + DNS
        |
     corp.lab
```

## Domain Controller

`DC01` provides the core Active Directory infrastructure for the lab.

Current services include:

- Active Directory Domain Services (AD DS)
- DNS
- Domain authentication
- Centralized user and group management

The Active Directory forest/domain is:

```text
corp.lab
```

The Domain Controller uses the static address:

```text
10.10.10.10
```

Domain clients use `10.10.10.10` as their DNS server so that Active Directory resources and domain controllers can be discovered correctly.

## Windows Workstation

`LAB-PC01` is a Windows 11 Enterprise workstation joined to the `corp.lab` domain.

Network configuration:

```text
IP Address:    10.10.10.30
Subnet:        255.255.255.0 (/24)
DNS Server:    10.10.10.10
```

Domain users created in Active Directory can authenticate to this workstation without requiring separate local accounts.

For example, the domain account:

```text
CORP\alan.bassett
```

can authenticate to `LAB-PC01` using credentials managed by Active Directory.

## Kali Linux

The Kali Linux VM provides a separate system for network discovery, enumeration, and future security testing against the lab.

Network configuration:

```text
IP Address:    10.10.10.20
Subnet:        255.255.255.0 (/24)
```

Connectivity between Kali and the Domain Controller was verified using ICMP:

```bash
ping 10.10.10.10
```

Future testing will include service enumeration and Active Directory security assessment from this system.

## Design Decisions

### Static Addressing

Static IPv4 addressing is currently used because the environment contains a small number of systems and predictable addresses simplify configuration and troubleshooting.

The Domain Controller in particular requires a predictable address because domain clients use `10.10.10.10` for DNS.

### Isolated Network

The virtual machines are connected through a dedicated VMware `VMnet2` network rather than being directly exposed to the normal LAN.

This provides an isolated environment for security experimentation and allows intentionally vulnerable configurations to be tested with less risk to unrelated devices.

### Centralized DNS

Windows domain clients use the Domain Controller as their DNS server rather than a public DNS resolver.

Active Directory relies heavily on DNS for discovering services such as domain controllers and authentication infrastructure.

## Planned Expansion

Future additions to the environment may include:

- Group Policy configuration
- Additional Windows workstations
- Additional Windows Server systems
- File server and role-based file permissions
- Second Domain Controller
- Centralized logging and SIEM
- Sysmon deployment
- Active Directory enumeration from Kali
- Vulnerability testing
- Attack-and-defense exercises
- Expansion across multiple physical virtualization hosts
