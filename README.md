# SOC Home Lab

A practical Security Operations Center home lab designed to build hands-on experience in:

- SIEM monitoring
- Endpoint telemetry
- Detection engineering
- Incident response
- Digital forensics
- Network detection
- Active Directory security
- MITRE ATT&CK mapping
- Attack simulation

## Lab Architecture

The lab is distributed across two physical systems.

### SOC / Analyst Side

- Laptop: Intel Core i7-11800H
- RAM: 16 GB
- Host OS: Windows
- Hypervisor: VMware Workstation Pro
- SOC VM: Ubuntu Server 24.04 LTS
- SIEM: Wazuh

Current SOC components:

- Wazuh Manager
- Wazuh Indexer
- Wazuh Dashboard

### Enterprise / Attack Side

- Desktop: AMD Ryzen 5 7600
- RAM: 32 GB
- Host OS: Windows
- Hypervisor: VMware Workstation Pro

Current enterprise systems:

- `DC01`
  - Windows Server 2022
  - Active Directory Domain Services
  - DNS Server
  - Domain: `soclab.local`
  - Wazuh Agent
  - Sysmon

Planned systems:

- `WIN11-01` Windows endpoint
- `KALI01` attacker system
- Linux server
- Suricata NDR sensor
- OPNsense firewall
- Splunk environment

## Current Architecture

```text
                         Desktop PC
                  Ryzen 5 7600 / 32 GB
                             |
                    VMware Workstation
                             |
             +---------------+---------------+
             |                               |
      SOC-WAZUH01                          DC01
             |                               |
   Ubuntu Server 24.04              Windows Server 2022
             |                               |
    +--------+--------+              +-------+-------+
    |        |        |              |               |
  Wazuh    Wazuh    Wazuh       Active Directory   DNS
 Manager  Indexer Dashboard            |
                                       |
                                  soclab.local
                                       |
                                       |
                                Wazuh Agent
                                       |
                                    Sysmon
                                       |
                                       +------------+
                                                    |
                                                    v
                                               Wazuh SIEM
```

## Telemetry Flow

```text
Windows activity
      |
      v
Windows Event Logs
      |
      +-------------------+
      |                   |
      v                   v
Native Security Logs    Sysmon
                          |
                          v
              Microsoft-Windows-Sysmon/Operational
                          |
                          v
                     Wazuh Agent
                          |
                          v
                    Wazuh Manager
                          |
                          v
                  Detection Rules
                          |
                          v
                 MITRE ATT&CK Mapping
                          |
                          v
                  Wazuh Dashboard
```

## Documentation

- [Wazuh SOC Server Deployment](setup/01-soc-laptop-wazuh.md)
- [Windows Server Domain Controller Deployment](setup/02-dc01.md)
- [Lab Architecture](architecture/lab-architecture.md)

## Current Progress

### SOC Infrastructure

- [x] Create Ubuntu Server VM
- [x] Install Ubuntu Server 24.04 LTS
- [x] Install Wazuh Manager
- [x] Install Wazuh Indexer
- [x] Install Wazuh Dashboard
- [x] Verify Wazuh services
- [x] Access Wazuh dashboard from the Windows host
- [x] Move the Wazuh VM to the desktop environment
- [x] Verify Wazuh after migration

### Enterprise Infrastructure

- [x] Deploy Windows Server 2022
- [x] Rename server to `DC01`
- [x] Install Active Directory Domain Services
- [x] Install DNS Server
- [x] Create the `soclab.local` Active Directory forest
- [x] Validate domain configuration
- [ ] Create Active Directory organizational units
- [ ] Create lab users and groups
- [ ] Deploy `WIN11-01`
- [ ] Join `WIN11-01` to `soclab.local`

### Monitoring and Detection

- [x] Verify connectivity between `DC01` and Wazuh
- [x] Install Wazuh agent on `DC01`
- [x] Confirm `DC01` appears as an active Wazuh endpoint
- [x] Verify Windows telemetry reaches Wazuh
- [x] Correct Windows Server time zone
- [x] Install Sysmon on `DC01`
- [x] Configure Wazuh to collect Sysmon telemetry
- [x] Validate Sysmon Event ID 1 in Wazuh
- [x] Confirm Wazuh detection rules process Sysmon events
- [x] Confirm MITRE ATT&CK mappings
- [ ] Configure advanced Windows audit policies
- [ ] Install Wazuh agent on `WIN11-01`
- [ ] Install Sysmon on `WIN11-01`
- [ ] Deploy Suricata
- [ ] Add Splunk

### Attack Simulation and Investigation

- [ ] Deploy Kali Linux
- [ ] Configure Atomic Red Team
- [ ] Perform first controlled attack simulation
- [ ] Build first custom detection
- [ ] Perform first SOC investigation
- [ ] Build incident timeline
- [ ] Classify True Positive / False Positive
- [ ] Map activity to MITRE ATT&CK
- [ ] Document incident findings

## Initial Detection Observations

Sysmon telemetry from `DC01` is now being ingested by Wazuh.

Wazuh has already generated alerts based on Sysmon process creation events.

Examples observed include:

- `net.exe` and `net1.exe` executing `net user`
- PowerShell activity
- `secedit.exe` execution

Wazuh mapped these activities to MITRE ATT&CK techniques including:

```text
T1087 - Account Discovery
T1059.001 - PowerShell
T1070.004 - File Deletion
```

Some of these events were generated by the Wazuh agent itself while performing security assessment tasks.

This demonstrates an important SOC principle:

```text
Alert != Incident
```

Alerts must be investigated in context before being classified as malicious.

Important investigation context includes:

- Parent process
- User context
- Command line
- Process tree
- Integrity level
- Execution source
- Host role
- Expected administrative or security activity

## Planned Detection and Investigation Workflow

```text
Attack Simulation
       |
       v
Enterprise Endpoint
       |
       v
Windows / Sysmon / Network Telemetry
       |
       v
Wazuh
       |
       v
Detection
       |
       v
SOC Investigation
       |
       +-- Event correlation
       +-- Timeline analysis
       +-- Process tree analysis
       +-- MITRE ATT&CK mapping
       +-- True Positive / False Positive classification
       +-- Incident documentation
```

## Future Development

The lab will later be extended with:

- Windows 11 domain endpoint
- Sysmon-based endpoint telemetry
- Advanced Windows auditing
- Suricata network detection
- Zeek network telemetry
- Splunk SIEM practice
- Velociraptor
- KAPE
- Volatility
- Sigma detection rules
- YARA
- Atomic Red Team
- SOC AI assistant integration with Wazuh

## Disclaimer

This lab is intended strictly for educational and defensive security purposes in an isolated environment.
