# Tempest DFIR Investigation

This repository documents the investigation of the **Tempest SOC Level 1 Capstone Challenge**.

The goal of this investigation was to analyze attacker activity using:

- Packet capture (PCAP)
- Sysmon logs
- Windows Event Logs

The investigation reconstructs the attacker kill chain from **initial compromise to persistence**.

---

## Key Findings

Initial Access
Malicious Microsoft Word document

Exploit
CVE-2022-30190 (Follina)

Payload Delivery
certutil download of malicious executable

Privilege Escalation
SeImpersonatePrivilege exploitation using PrintSpoofer

Command and Control
167.71.199.191

Pivoting
Reverse SOCKS proxy via Chisel

Persistence
Malicious service creation

---

## Skills Demonstrated

- Network forensic analysis
- Sysmon log investigation
- Windows event analysis
- Malware hash intelligence
- Attack chain reconstruction
- Threat intelligence pivoting

---

## Tools Used

Wireshark  
PowerShell  
Sysmon  
Event Viewer  
VirusTotal  
Threat Intel Sources
