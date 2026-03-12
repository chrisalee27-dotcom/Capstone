# Boogeyman DFIR Investigation

## Overview

This investigation analyzes a simulated security incident involving a phishing email that led to system compromise, attacker enumeration activity, and data exfiltration. The objective was to analyze provided forensic artifacts and reconstruct the attacker’s actions.

Artifacts analyzed during this investigation included:

- Email artifact (`dump.eml`)
- Network capture (`capture.pcapng`)
- Windows Event Logs (`powershell.evtx`)
- Parsed PowerShell logs (`powershell.json`)

Through analysis of these artifacts, the attacker’s activity was reconstructed, including:

- Initial phishing delivery
- PowerShell execution
- Tool download and system enumeration
- Access to sensitive user data
- Data exfiltration using DNS tunneling

---

# 1. Phishing Email Analysis

The investigation began by analyzing the suspicious email contained in `dump.eml`.

The email originated from a suspicious domain and contained a password-protected attachment designed to bypass email security filters.

### Email Sender

![Email Sender](Screenshots/01_email_sender.png)

### Email Recipient

![Email Recipient](Screenshots/02_email_recipient.png)

### DKIM Signature Analysis

The email header revealed that the DKIM signature originated from a third-party domain.

![DKIM Signature](Screenshots/03_DKIM_Signature_3rd_Party.png)

---

# 2. Password-Protected Attachment

The phishing email contained a password-protected attachment designed to evade automated scanning systems.

The password provided in the email allowed extraction of the malicious archive.

![Password Protected Attachment](Screenshots/04_Password_Encoded_Attachment.png)

---

# 3. Malicious LNK File

The extracted attachment contained a malicious `.lnk` file. Using `lnkparse`, the shortcut file was analyzed to determine the command executed by the attacker.

![LNK File Analysis](Screenshots/05_lnkparse_Invoice.png)

The analysis revealed that the shortcut executed a PowerShell command to download additional tools from attacker-controlled infrastructure.

---

# 4. PowerShell Activity Analysis

PowerShell logs were parsed using `jq` to analyze executed commands.

Example command used:

```bash
cat powershell.json | jq -r '.ScriptBlockText'
```

The logs revealed commands used to download enumeration tools and execute system reconnaissance.

![PowerShell Logs](Screenshots/07_cat_powershell_json_jq.png)

---

# 5. Command and Control Infrastructure

Analysis of the PowerShell logs revealed connections to attacker-controlled domains used for hosting malicious payloads and C2 communication.

![C2 Domains](Screenshots/08_C2_domains.png)

The attacker used:

```
files.bpackaging.xyz
cdn.bpackaging.xyz
```

---

# 6. Enumeration Tool Download

The attacker downloaded an enumeration tool used to gather system information.

![Enumeration Tool](Screenshots/09_enumeration_tool.png)

The enumeration tool identified during the investigation was **Seatbelt**, a well-known Windows security assessment tool.

---

# 7. Accessing Sensitive Files

Further analysis revealed the attacker accessed a local database used by Microsoft Sticky Notes.

![Accessing Sensitive File](Screenshots/10_accesses_sq3.exe.png)

The accessed file path was:

```
C:\Users\j.westcott\AppData\Local\Packages\Microsoft.MicrosoftStickyNotes_8wekyb3d8bbwe\LocalState\plum.sqlite
```

---

# 8. Software Identification

The SQLite database accessed by the attacker belongs to Microsoft Sticky Notes.

![Sticky Notes Database](Screenshots/11_Q3_software.png)

---

# 9. Extracted Sensitive File

Analysis later revealed that the attacker accessed a KeePass database file containing sensitive credentials.

![Extracted File](Screenshots/12_extracted_file.png)

---

# 10. KeePass Credential Database

The stolen file was identified as a KeePass credential database.

![KeePass Database](Screenshots/13_KeePass.png)

KeePass databases typically store sensitive credentials including passwords and financial information.

---

# 11. DNS Data Exfiltration

Network traffic analysis revealed that the attacker exfiltrated data using DNS tunneling.

Encoded data fragments were embedded within DNS query names.

![DNS Encoding](Screenshots/14_hex_encoding.png)

---

# 12. DNS Lookup During Exfiltration

DNS queries were observed containing encoded fragments of the stolen data.

![DNS Lookup](Screenshots/15_exfiltration_ns_lookup.png)

---

# 13. Payload Hosting Infrastructure

Network traffic analysis revealed that the attacker hosted malicious payloads using a Python HTTP server.

![Payload Server](Screenshots/16_software_host_file_payload_py.png)

---

# 14. HTTP Command and Control

The compromised system transmitted command results back to the attacker via HTTP POST requests.

Wireshark filtering:

```bash
http.request.method == "POST"
```

![HTTP POST C2](Screenshots/17_HTTP_post_C2.png)

---

# 15. DNS Exfiltration Protocol

Further network analysis confirmed that the attacker used DNS to exfiltrate the stolen data.

![DNS Exfiltration](Screenshots/18_dns_exfiltration_protocol.png)

---

# 16. Recovering the Exfiltrated Data

The DNS query fragments were extracted from the network capture and reconstructed.

![Encoded Password Discovery](Screenshots/19_Finding_Password_encoded.png)

The encoded password was then decoded.

![Password Decoding](Screenshots/20_Decoding_Password.png)

---

# 17. Sensitive Data Discovery

After reconstructing and opening the KeePass database, sensitive information was recovered.

![Credit Card Discovery](Screenshots/21_finding_credit_card_nuber_part1.png)

Additional sensitive information was found inside the credential database.

![Credit Card Discovery Continued](Screenshots/22_finding_credit_card_nuber_part2.png)

---

# Attack Chain Summary

```
Phishing Email
      ↓
Password Protected Attachment
      ↓
Malicious LNK Execution
      ↓
PowerShell Payload Execution
      ↓
Tool Download (Seatbelt)
      ↓
Sensitive File Access
      ↓
KeePass Credential Theft
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

# Lessons Learned

This investigation highlights several common attacker techniques:

- Use of password-protected attachments to evade email security scanning
- Execution of malicious PowerShell commands via shortcut files
- Use of system enumeration tools during post-exploitation
- DNS tunneling used for stealthy data exfiltration
- Targeting credential databases containing sensitive user data

Organizations should monitor for:

- Suspicious PowerShell execution
- DNS queries containing encoded data
- Unexpected outbound connections
- Access to sensitive credential stores

---

# Conclusion

This investigation successfully reconstructed the attacker’s actions from initial compromise through data exfiltration. The attacker leveraged common post-exploitation techniques and used DNS tunneling to stealthily exfiltrate sensitive information.

The analysis demonstrates the importance of monitoring PowerShell activity, network traffic patterns, and DNS anomalies to detect similar attacks in real-world environments.
