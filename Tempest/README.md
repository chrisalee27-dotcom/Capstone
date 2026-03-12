# Tempest Incident Investigation
<img width="1694" height="435" alt="Tempest" src="https://github.com/user-attachments/assets/5df30a08-77e2-4fa8-8ba4-7ee6818a2201" />

This investigation analyzes a simulated compromise of a Windows workstation.
The attacker gained access through a **malicious document**, established **command and control**, escalated privileges, and created **persistence mechanisms**.

---
## Attack Timeline

1. Victim downloads malicious document `free_magicules.doc`
2. Microsoft Word executes the payload
3. Follina exploit launches `msdt.exe`
4. PowerShell downloads `first.exe`
5. Malware connects to C2 server `167.71.199.191`
6. Attacker deploys Chisel reverse proxy
7. Privilege escalation via PrintSpoofer
8. New user `shion` created
9. User added to Administrators group
10. Persistence established via Windows service
# Evidence Integrity Verification

Before beginning analysis, the integrity of the provided evidence files was verified using SHA256 hashing.

Evidence:

* [PCAP SHA256 Hash](Screenshots/01_pcap_sha256_hash.png.png)
* [Sysmon Log SHA256](Screenshots/02_sysmon_evtx_sha256_hash.png.png)
* [Windows Event Log SHA256](Screenshots/03_windows_evtx_sha256_hash.png.png)

---

# Initial Access – Malicious Document

The victim downloaded a malicious Word document from a phishing server.

Evidence:

* [Malicious Document Download](Screenshots/04_malicious_document_free_magicules_doc.png.png)
* [Infected User Account](Screenshots/05_infected_user_benimaru.png.png)
* [WinWord Execution Event](Screenshots/06_winword_process_execution.png.png)

---

# Command and Control Infrastructure

Network traffic analysis revealed communication with attacker infrastructure.

Evidence:

* [C2 IP Address Identified](Screenshots/07_c2_ip_address_167.71.199.191.png.png)
* [Encoded Base64 Payload](Screenshots/08_base64_encoded_payload.png.png)

The attack leveraged the **Follina vulnerability (CVE-2022-30190)**.

Evidence:

* [MSDT Follina Exploit Execution](Screenshots/09_msdt_follina_exploit.png.png)

---

# Payload Delivery

Persistence artifacts were discovered within the Startup folder.

Evidence:

* [Startup Folder Persistence Artifact](Screenshots/10_startup_folder_persistence_update.zip.png.png)

The malware payload was downloaded using PowerShell and `certutil`.

Evidence:

* [PowerShell Certutil Payload Download](Screenshots/11_powershell_certutil_payload_download.png.png)
* [Payload SHA256 Hash](Screenshots/12_first_exe_sha256_hash.png.png)
* [Payload Execution Sysmon Event](Screenshots/13_first_exe_execution_sysmon_event.png.png)

---

# Network Traffic Analysis

Wireshark analysis identified HTTP requests to malicious infrastructure.

Evidence:

* [Wireshark HTTP Traffic Filter](Screenshots/14_wireshark_http_filter.png)
* [Phishing Server Index Request](Screenshots/15_phishteam_index_request.png)
* [TCP Stream HTTP Traffic](Screenshots/16_tcp_stream_http_traffic.png)
* [Wireshark HTTP Request Filter](Screenshots/17_wireshark_http_request_filter.png)
* [User Agent Investigation](Screenshots/18_wireshark_http_user_agent_filter.png.png)

Additional command and control communication was discovered.

Evidence:

* [ResolveCyber C2 Request](Screenshots/19_resolvecyber_http_request.png.png)

---

# Internal Reconnaissance

The attacker enumerated network services using system tools.

Evidence:

* [Netstat Listening Ports](Screenshots/20_netstat_listening_ports.png)
* [WinRM Port 5985 Listening](Screenshots/21_winrm_port_5985_listening.png.png)

---

# Reverse Proxy Setup

The attacker deployed **Chisel**, a reverse SOCKS proxy tool.

Evidence:

* [Chisel Reverse SOCKS Command](Screenshots/22_chisel_reverse_socks_command.png.png)
* [Chisel Binary SHA256 Hash](Screenshots/23_chisel_sha256_hash.png.png)
* [Chisel VirusTotal Detection](Screenshots/24_chisel_virustotal_detection.png.png)

WinRM was used as part of remote administration.

Evidence:

* [WinRM Port Explanation](Screenshots/25_winrm_port_5985_description.png.png)

---

# Privilege Escalation

Privilege escalation was achieved using **PrintSpoofer**.

Evidence:

* [Exploit Binary Sysmon Event](Screenshots/26_spf_exploit_binary_sysmon_event.png.png)
* [PrintSpoofer SHA256 Hash](Screenshots/27_printspoofer_sha256_hash.png.png)
* [PrintSpoofer VirusTotal Detection](Screenshots/28_printspoofer_virustotal_detection.png.png)
* [PrintSpoofer Exploit Documentation](Screenshots/29_printspoofer_exploit_reference.png.png)

---

# Additional Payload Delivery

Further executable downloads were identified within network traffic.

Evidence:

* [Executable Download Filter](Screenshots/30_wireshark_exe_download_filter.png.png)

---

# Account Creation

The attacker created a new user account.

Evidence:

* [New User Account Creation](Screenshots/31_new_user_account_creation_sysmon.png.png)

The attacker then added the account to the **Administrators group**.

Evidence:

* [Localgroup Administrator Command](Screenshots/32_net_localgroup_administrators_add_shion.png.png)

Windows security logs confirmed the account creation event.

Evidence:

* [Event ID 4720 Lookup](Screenshots/33_eventid_4720_account_creation_lookup.png.png)

---

# Privilege Assignment

The attacker successfully added the user to the administrators group.

Evidence:

* [Administrator Group Addition](Screenshots/34_admin_group_addition.png.png)
* [Event ID 4732 Lookup](Screenshots/35_windows_eventid_4732_lookup.png.png)

---

# Persistence

Persistence was established by creating a malicious Windows service.

Evidence:

* [SC Service Creation Persistence](Screenshots/36_sc_service_creation_persistence.png.png)

The command executed:

```
sc.exe \\TEMPEST create TempestUpdate2 binpath= C:\ProgramData\final.exe start= auto
```

This ensures the malware executes automatically when the system boots.

---
## MITRE ATT&CK Techniques

| Technique | Description |
|-----------|-------------|
| T1566 | Phishing |
| T1204 | User Execution |
| T1059 | Command Execution |
| T1105 | Ingress Tool Transfer |
| T1572 | Protocol Tunneling |
| T1134 | Access Token Manipulation |
| T1543 | Create or Modify System Process |
| T1078 | Valid Accounts |

## Detection Opportunities

Security teams could detect this attack through:

- Monitoring PowerShell downloads using `certutil`
- Alerting on unusual `msdt.exe` execution
- Detecting outbound traffic to unknown domains
- Monitoring creation of new administrator accounts
- Alerting on Windows service creation events
# Conclusion

The attacker successfully:

* Delivered a malicious document
* Exploited the Follina vulnerability
* Downloaded and executed malware payloads
* Established command and control communication
* Deployed a reverse SOCKS proxy
* Escalated privileges using PrintSpoofer
* Created a privileged user account
* Added the user to the Administrators group
* Installed persistence via a Windows service

This investigation demonstrates the workflow used by **SOC analysts and incident responders** to reconstruct attacker activity across network traffic, endpoint logs, and system artifacts.


