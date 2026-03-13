# Boogeyman 2 – SOC Level 1 Capstone

## Scenario

In this investigation, we analyze a compromised workstation memory dump to determine how an attacker gained access, executed malware, established command and control (C2), and implemented persistence. The analysis focuses on identifying the phishing email, malicious document, execution chain, and persistence mechanism using memory forensics techniques.

Artifacts were analyzed using **Volatility 3** to reconstruct the attack chain and identify indicators of compromise.

---

# Investigation Walkthrough

---

## 1. Identify the Phishing Email Sender

The initial step is identifying the sender of the phishing email responsible for delivering the malicious attachment.

Screenshot:

[01_Phishing_Sender](Screenshots/01_Phishing_Sender.png)

---

## 2. Identify the Phishing Email Recipient

Next, we determine the targeted employee who received the malicious email.

Screenshot:

[02_Phishing_Recipient](Screenshots/02_Phishing_Recipient.png)

---

## 3. Identify the Malicious Document

The phishing email contained a Microsoft Word document attachment designed to execute malicious code when opened.

Screenshot:

[03_Malicious_Document](Screenshots/03_Malicious_Document.png)

---

## 4. Calculate the MD5 Hash of the Attachment

To uniquely identify the malicious document, the MD5 hash was calculated for threat intelligence correlation.

Screenshot:

[04_MD5_hash](Screenshots/04_MD5_hash.png)

---

## 5. Identify the Stage 2 Download URL

The malicious macro embedded within the document downloads a second-stage payload from a remote server.

Screenshot:

[05_Stage2_URL](Screenshots/05_Stage2_URL.png)

---

## 6. Identify the Stage 2 Payload Process

After execution, the payload launches a process responsible for continuing the attack chain.

Screenshot:

[06_Stage2_Payload_Process](Screenshots/06_Stage2_Payload_Process.png)

---

## 7. Identify the Full File Path of the Payload

Using memory analysis, the exact location of the malicious binary was recovered.

Screenshot:

[07_Full_File_Path](Screenshots/07_Full_File_Path.png)

---

## 8. Identify the Payload Process ID (PID)

The malicious payload process was identified along with its process identifier (PID).

Screenshot:

[08_Stage2_Payload_PID](Screenshots/08_Stage2_Payload_PID.png)

---

## 9. Identify the Parent Process ID (PPID)

Understanding the parent process helps reconstruct the execution chain and determine how the payload was launched.

Screenshot:

[09_Stage2_Payload_PPID](Screenshots/09_Stage2_Payload_PPID.png)

---

## 10. Identify the Stage 2 Download URL

The memory dump also reveals the remote URL used by the malware to retrieve the binary payload.

Screenshot:

[10_Stage2_Payload_URL](Screenshots/10_Stage2_Payload_URL.png)

---

## 11. Identify the PID of the Malicious C2 Connection

Using Volatility’s network analysis plugins, we identified the process responsible for establishing the command and control connection.

Screenshot:

[11_PID_Malicious_C2_Connection](Screenshots/11_PID_Malicious_C2_Connection.png)

---

## 12. Identify the Full File Path of the C2 Malware

The full file path of the malicious binary responsible for the C2 communication was extracted from memory artifacts.

Screenshot:

[12_Full_Path_C2_Connection](Screenshots/12_Full_Path_C2_Connection.png)

---

## 13. Identify the C2 IP Address and Port

Network artifacts reveal the remote command-and-control server used by the attacker.

Screenshot:

[13_IP_Port_C2_Connection](Screenshots/13_IP_Port_C2_Connection.png)

---

## 14. Identify the Full Path of the Malicious Email Attachment

The attachment opened by the victim was located in the Outlook temporary cache directory.

Screenshot:

[14_Full_File_Path_Mal_Email](Screenshots/14_Full_File_Path_Mal_Email.png)

---

## 15. Identify the Persistence Mechanism

After establishing the C2 connection, the attacker created a scheduled task to maintain persistence. The command used to create the scheduled task was recovered from memory strings.

Screenshot:

[15_Scheduled_Task_Command](Screenshots/15_Scheduled_Task_Command.png)

---

# Attack Chain Summary

The investigation revealed the following attack progression:

1. A phishing email was sent to the victim employee.
2. The email contained a malicious Word document attachment.
3. The document executed a macro which downloaded a second-stage payload.
4. The payload executed a malicious binary (`updater.exe`).
5. The malware established a command-and-control connection to an external server.
6. The attacker implemented persistence using a scheduled task that executed an encoded PowerShell payload stored in the registry.

---

# Tools Used

* **Volatility 3**
* **strings**
* **Linux command line utilities**
* **Memory forensics techniques**

---

# Key Indicators of Compromise

| Artifact         | Value                                                 |
| ---------------- | ----------------------------------------------------- |
| Malware Process  | updater.exe                                           |
| C2 Server        | 128.199.95.189                                        |
| C2 Port          | 8080                                                  |
| Persistence      | Scheduled Task (Updater)                              |
| Registry Payload | HKCU:\Software\Microsoft\Windows\CurrentVersion\debug |

---

# Skills Demonstrated

* Memory forensics
* Malware process identification
* Network artifact analysis
* Persistence mechanism discovery
* Incident response workflow
* Attack chain reconstruction

---

# Repository Structure

```
Boogeyman_2
│
├── README.md
└── Screenshots
    ├── 01_Phishing_Sender.png
    ├── 02_Phishing_Recipient.png
    ├── 03_Malicious_Document.png
    ├── 04_MD5_hash.png
    ├── 05_Stage2_URL.png
    ├── 06_Stage2_Payload_Process.png
    ├── 07_Full_File_Path.png
    ├── 08_Stage2_Payload_PID.png
    ├── 09_Stage2_Payload_PPID.png
    ├── 10_Stage2_Payload_URL.png
    ├── 11_PID_Malicious_C2_Connection.png
    ├── 12_Full_Path_C2_Connection.png
    ├── 13_IP_Port_C2_Connection.png
    ├── 14_Full_File_Path_Mal_Email.png
    └── 15_Scheduled_Task_Command.png
```

---

# Conclusion

Through memory forensic analysis, the full attack chain was reconstructed from phishing email delivery through persistence establishment. The investigation demonstrates how attackers leverage phishing, macro execution, staged payloads, and scheduled task persistence to maintain access to compromised systems.

This exercise highlights the importance of memory analysis in identifying attacker activity that may not be visible through traditional disk-based forensic analysis.

