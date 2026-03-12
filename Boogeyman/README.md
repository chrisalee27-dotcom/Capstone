# Boogeyman DFIR Investigation

## Overview

This investigation analyzes a simulated security incident involving a phishing email that led to system compromise, attacker enumeration activity, and data exfiltration.

The objective of the investigation was to analyze forensic artifacts and reconstruct the attacker’s actions from initial access through data exfiltration.

Artifacts analyzed during this investigation:

- Email artifact (`dump.eml`)
- Network capture (`capture.pcapng`)
- Windows Event Logs (`powershell.evtx`)
- Parsed PowerShell logs (`powershell.json`)

The investigation revealed that the attacker used a phishing email containing a malicious shortcut file which executed PowerShell commands to download attacker tools, enumerate the system, and exfiltrate sensitive data using DNS tunneling.

---
## Attack Timeline

1. Phishing email delivered to victim
2. Password-protected archive opened
3. Malicious LNK executed
4. PowerShell payload downloaded
5. Seatbelt enumeration executed
6. Sensitive database accessed
7. KeePass credential database discovered
8. Data exfiltration via DNS tunneling
# 1. Phishing Email Analysis

---
## Tools Used

- Thunderbird (Email analysis)
- LNKParse3 (Shortcut analysis)
- jq (PowerShell log parsing)
- Wireshark / Tshark (Network analysis)
- CyberChef (Data decoding)
- KeePassXC (Credential database analysis)

---

## MITRE ATT&CK Techniques Observed

| Technique | ID |
|----------|----|
Phishing | T1566 |
Command Execution via PowerShell | T1059 |
System Enumeration | T1082 |
Data Exfiltration Over DNS | T1048 |

---

The investigation began with analysis of the suspicious email contained in the artifact `dump.eml`.

The email was sent from a suspicious external domain and contained a password-protected attachment designed to bypass email scanning systems.

Evidence:

- [01_email_sender](Screenshots/01_email_sender.png)
- [02_email_recipient](Screenshots/02_email_recipient.png)

Further header analysis revealed that the DKIM signature referenced a third-party domain.

Evidence:

- [03_DKIM_Signature_3rd_Party](Screenshots/03_DKIM_Signature_3rd_Party.png)

---

# 2. Password-Protected Attachment

The phishing email included a password-protected archive attachment.

Password protection is a common tactic used by attackers to bypass automated email security scanners.

Evidence:

- [04_Password_Encoded_Attachment](Screenshots/04_Password_Encoded_Attachment.png)

After extracting the archive, a malicious Windows shortcut file was discovered.

---

# 3. Malicious Shortcut File Analysis

The extracted attachment contained a malicious `.lnk` file.  
Using the `lnkparse` tool, the shortcut file was analyzed to determine its behavior.

Evidence:

- [05_lnkparse_Invoice](Screenshots/05_lnkparse_Invoice.png)

Analysis revealed the shortcut executed a PowerShell command designed to download additional attacker tools.

---

# 4. PowerShell Activity Analysis

PowerShell logs were parsed using `jq` to review executed commands.

Example command used during analysis:

```bash
cat powershell.json | jq -r '.ScriptBlockText'
```

Evidence:

- [07_cat_powershell_json_jq](Screenshots/07_cat_powershell_json_jq.png)

The logs revealed commands used to download attacker tools and perform system enumeration.

---

# 5. Command and Control Infrastructure

PowerShell logs revealed connections to attacker-controlled infrastructure used for hosting malicious payloads.

Identified domains:

```
files.bpackaging.xyz
cdn.bpackaging.xyz
```

Evidence:

- [08_C2_domains](Screenshots/08_C2_domains.png)

---

# 6. Enumeration Tool Download

The attacker downloaded the **Seatbelt** enumeration tool from the malicious infrastructure.

Seatbelt is a widely known Windows enumeration tool used by attackers to collect system information.

Evidence:

- [09_enumeration_tool](Screenshots/09_enumeration_tool.png)

---

# 7. Sensitive File Access

The attacker used the downloaded tool `sq3.exe` to access a local database belonging to Microsoft Sticky Notes.

File accessed:

```
C:\Users\j.westcott\AppData\Local\Packages\Microsoft.MicrosoftStickyNotes_8wekyb3d8bbwe\LocalState\plum.sqlite
```

Evidence:

- [10_accesses_sq3.exe](Screenshots/10_accesses_sq3.exe.png)

---

# 8. Software Identification

The accessed database was confirmed to belong to **Microsoft Sticky Notes**, which stores user notes locally in a SQLite database.

Evidence:

- [11_Q3_software](Screenshots/11_Q3_software.png)

Users often store sensitive information in Sticky Notes, making it a valuable target for attackers.

---

# 9. Credential Database Discovery

Further investigation revealed that a KeePass credential database was also accessed during the attack.

Evidence:

- [12_extracted_file](Screenshots/12_extracted_file.png)
- [13_KeePass](Screenshots/13_KeePass.png)

KeePass databases often store highly sensitive information such as passwords and financial data.

---

# 10. DNS Data Exfiltration

Network traffic analysis revealed that the attacker exfiltrated data using **DNS tunneling**.

Encoded data fragments were embedded within DNS query names.

Evidence:

- [14_hex_encoding](Screenshots/14_hex_encoding.png)
- [15_exfiltration_ns_lookup](Screenshots/15_exfiltration_ns_lookup.png)

---

# 11. Payload Hosting Infrastructure

Network traffic analysis revealed that the attacker hosted payloads using a Python HTTP server.

Evidence:

- [16_software_host_file_payload_py](Screenshots/16_software_host_file_payload_py.png)

---

# 12. HTTP Command and Control

The compromised system transmitted command results back to the attacker using HTTP POST requests.

Wireshark filter used:

```bash
http.request.method == "POST"
```

Evidence:

- [17_HTTP_post_C2](Screenshots/17_HTTP_post_C2.png)

---

# 13. DNS Exfiltration Protocol

Further analysis confirmed that DNS was used as the protocol for data exfiltration.

Evidence:

- [18_dns_exfiltration_protocol](Screenshots/18_dns_exfiltration_protocol.png)

---

# 14. Recovering the Exfiltrated Data

Encoded DNS fragments were extracted and reconstructed to recover the stolen data.

Evidence:

- [19_Finding_Password_encoded](Screenshots/19_Finding_Password_encoded.png)
- [20_Decoding_Password](Screenshots/20_Decoding_Password.png)

---

# 15. Sensitive Data Discovery

After reconstructing the stolen KeePass database, sensitive information was recovered.

Evidence:

- [21_finding_credit_card_nuber_part1](Screenshots/21_finding_credit_card_nuber_part1.png)
- [22_finding_credit_card_nuber_part2](Screenshots/22_finding_credit_card_nuber_part2.png)

The database contained a stored credit card number, confirming successful data exfiltration.

---

# Attack Chain Summary

```
Phishing Email
      ↓
Password-Protected Attachment
      ↓
Malicious LNK Execution
      ↓
PowerShell Payload Execution
      ↓
Tool Download (Seatbelt)
      ↓
Sensitive File Access
      ↓
KeePass Credential Database Theft
      ↓
DNS Tunneling Exfiltration
```

---

# Key Findings

| Category | Finding |
|--------|--------|
Initial Access | Phishing email with malicious attachment |
Execution | Malicious LNK launching PowerShell |
Enumeration Tool | Seatbelt |
Sensitive File | Sticky Notes SQLite database |
Credential Database | KeePass `.kdbx` |
Exfiltration Method | DNS tunneling |
C2 Infrastructure | `files.bpackaging.xyz`, `cdn.bpackaging.xyz` |

---

# Conclusion

The investigation successfully reconstructed the attacker’s activity from initial phishing email delivery through system compromise and data exfiltration.

The attacker leveraged common post-exploitation techniques including PowerShell payload execution, system enumeration, and DNS tunneling to exfiltrate sensitive data.

This investigation highlights the importance of monitoring:

- Suspicious PowerShell activity
- DNS traffic anomalies
- Unexpected outbound network connections
- Access to sensitive credential databases

Proper monitoring and detection controls can significantly reduce the impact of similar attacks in real-world environments.
