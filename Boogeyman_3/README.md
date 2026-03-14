# Boogeyman 3 – SOC Investigation

## Scenario

The Boogeyman threat actor returns with a more advanced attack chain. In this investigation, we analyze telemetry from a compromised host to identify how the attacker executed malware, established persistence, and attempted lateral movement within the network.

Using SIEM logs and endpoint telemetry, we reconstruct the attacker’s actions and determine the techniques used to maintain access and pivot through the environment.

This investigation was performed using **Elastic SIEM logs from Winlogbeat**, focusing on process creation events, command-line activity, network connections, and attacker behavior.

---

# Tools Used

* Elastic SIEM (Kibana / Winlogbeat logs)
* Windows Event Logs
* Process Command Line Analysis
* Network Connection Analysis
* Threat Hunting Methodology

---

# Attack Chain Summary

| Stage                | Description                                         |
| -------------------- | --------------------------------------------------- |
| Initial Execution    | Stage 1 malware executed on the compromised machine |
| File Implantation    | Malware copied a malicious file to another location |
| Execution            | Implanted file executed using Windows binaries      |
| Persistence          | Scheduled task created to maintain access           |
| Command & Control    | Host attempted outbound C2 connection               |
| Privilege Escalation | Attacker used a UAC bypass technique                |
| Credential Access    | Attacker attempted credential dumping               |
| Lateral Movement     | Attacker pivoted to another machine                 |
| Ransomware Delivery  | Malicious download link used to deploy ransomware   |

---

# Investigation Walkthrough

---

# 1. Parent Process – Stage 1 Payload

The investigation begins by identifying the process responsible for executing the initial Stage 1 payload.

![Stage1 Payload Parent Process](Screenshots/01_Parent_Process_ID_Stage1_Payload.png)

---

# 2. Command Line Used to Copy Malware

The Stage 1 payload attempts to implant a malicious file to another location on disk using Windows utilities.

![Command Line Copy Location](Screenshots/02_Command_Line_Value_Copy_Location.png)

---

# 3. Full Command Line – Implanted Code Execution

After copying the file, the attacker executes the implanted payload.

![Implanted Code Execution](Screenshots/03_Full_Command_Line_Implanted_Code_Stage1.png)

---

# 4. Scheduled Task Persistence

The attacker creates a **scheduled task** to maintain persistence on the compromised machine.

![Scheduled Task](Screenshots/04_Scheduled_Task_Name.png)

---

# 5. C2 Connection (IP and Port)

Following execution of the implanted file, the compromised system attempts to establish a **Command and Control (C2) connection**.

![C2 Connection](Screenshots/05_IP_PORT_C2_Connection.png)

---

# 6. UAC Bypass Process

The attacker identifies the compromised account has administrator privileges and attempts a **UAC bypass technique**.

![UAC Bypass Process](Screenshots/06_UAC_Bypass_Process.png)

---

# 7. GitHub Link Used by Attacker

During the investigation, we identify a GitHub link used by the attacker to retrieve additional payloads.

![GitHub Link](Screenshots/07_GitHub_Link.png)

---

# 8. Username Hash

Credential artifacts reveal hashed account information associated with attacker activity.

![Username Hash](Screenshots/08_Username_Hash.png)

---

# 9. Remote Share Access

Logs indicate the attacker accessed a **remote share** as part of lateral movement.

![Remote Share](Screenshots/09_Remote_Share_File.png)

---

# 10. Attacker Created Credentials

Evidence shows a **new username and password** created by the attacker.

![New Credentials](Screenshots/10_Attacker_New_Username_Password.png)

---

# 11. Target Machine Hostname

Analysis reveals the hostname of the machine targeted during lateral movement.

![Target Hostname](Screenshots/11_Hostname_Attackers_Target_Machine.png)

---

# 12. Parent Command Line – Lateral Movement

Command-line logs reveal how the attacker initiated lateral movement.

![Lateral Movement Command](Screenshots/12_Process_Parent_Command_Lateral_Movement.png)

---

# 13. Username Hash – Second Machine

Credential artifacts were also discovered on a second compromised machine.

![Second Machine Username Hash](Screenshots/13_Username_Hash_Second_Machine.png)

---

# 14. Account Dumped by Attacker

Evidence indicates the attacker attempted to dump account credentials.

![Account Dumped](Screenshots/14_Account_Attacker_Dumped.png)

---

# 15. Ransomware Download Link

Finally, the investigation reveals the link used to download ransomware.

![Ransomware Download](Screenshots/15_Link_Used_Download_Ransomware.png)

---

# Key Takeaways

This investigation demonstrates several common attacker techniques used during a multi-stage compromise:

* Malware execution using legitimate Windows binaries
* File implantation and execution
* Scheduled task persistence
* Command and Control communication
* Privilege escalation via UAC bypass
* Credential dumping
* Lateral movement between hosts
* Ransomware deployment

Understanding these behaviors allows SOC analysts to detect similar attack patterns and respond quickly to emerging threats.

---

# Related Investigations

Boogeyman Investigation
Boogeyman 2 Investigation

Full SOC Investigation Repository:

https://github.com/chrisalee27-dotcom/SOC-Level-1-Capstone


