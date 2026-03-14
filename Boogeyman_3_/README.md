
# Boogeyman 3 – SOC Investigation

## Scenario

The Boogeyman threat actor returns with a more advanced attack chain.
In this investigation, we analyze telemetry from a compromised host to identify how the attacker executed malware, established persistence, and attempted lateral movement within the network.

Using SIEM logs and endpoint telemetry, we reconstruct the attacker’s actions and determine the techniques used to maintain access and pivot through the environment.

This investigation was performed using **Elastic SIEM logs from Winlogbeat**, focusing on process creation events, command-line activity, network connections, and attacker behavior.

---

# Tools Used

* Elastic SIEM (Kibana / Winlogbeat logs)
* Windows Event Logs
* Process and Command Line Analysis
* Threat Hunting Techniques

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
| Credential Access    | Attacker attempted to dump account credentials      |
| Lateral Movement     | Attacker attempted movement to a second host        |
| Ransomware Delivery  | Malicious download link used to deploy ransomware   |

---

# Investigation Walkthrough

---

# 1. Parent Process – Stage 1 Payload

The investigation begins by identifying the process responsible for executing the initial Stage 1 payload. By examining process creation logs within Elastic, we can identify the parent process responsible for launching the malware.

![Stage1 Payload Parent Process](01_Parent_Process_ID_Stage1_Payload.png)

---

# 2. Command Line Used to Copy Malware

The Stage 1 payload attempts to implant a file to another location on disk using Windows utilities. Command-line telemetry reveals how the attacker copied the malicious file.

![Command Line Copy Location](02_Command_Line_Value_Copy_Location.png)

---

# 3. Full Command Line – Implanted Code Execution

After copying the file, the attacker executes the implanted payload. Analyzing command-line arguments allows us to identify the method used to execute the malicious code.

![Implanted Code Execution](03_Full_Command_Line_Implanted_Code_Stage1.png)

---

# 4. Scheduled Task Persistence

The attacker creates a **scheduled task** to maintain persistence on the compromised machine. Scheduled tasks are commonly used by malware to ensure execution after system reboot.

![Scheduled Task](04_Scheduled_Task_Name.png)

---

# 5. C2 Connection (IP and Port)

Following execution of the implanted file, the compromised system attempts to establish a **Command and Control (C2) connection** to an external server.

Network telemetry within the SIEM reveals the destination IP and port used by the malware.

![C2 Connection](05_IP_PORT_C2_Connection.png)

---

# 6. UAC Bypass Process

The attacker discovers that the compromised user account has **local administrator privileges** and attempts to escalate privileges using a **UAC bypass technique**.

Process logs show the executable used to perform the bypass.

![UAC Bypass Process](06_UAC_Bypass_Process.png)

---

# 7. GitHub Link Used by Attacker

During the investigation, we identify a GitHub link used by the attacker to retrieve additional payloads or scripts used during the attack.

![GitHub Link](07_GitHub_Link.png)

---

# 8. Username Hash

Analysis of credential-related artifacts reveals hashed account information used during the attack.

![Username Hash](08_Username_Hash.png)

---

# 9. Remote Share Access

The attacker attempts to access a remote network share as part of lateral movement.

![Remote Share File](09_Remote_Share_File.png)

---

# 10. Attacker Created Credentials

Logs show evidence of a newly created username and password associated with the attacker’s activity.

![New Username Password](10_Attacker_New_Username_Password.png)

---

# 11. Target Machine Hostname

Investigation of network activity reveals the hostname of the system targeted during lateral movement.

![Target Hostname](11_Hostname_Attackers_Target_Machine.png)

---

# 12. Parent Command Line – Lateral Movement

Further analysis shows the command-line arguments used by the attacker to initiate lateral movement between hosts.

![Parent Command Lateral Movement](12_Process_Parent_Command_Lateral_Movement.png)

---

# 13. Username Hash (Second Machine)

Credential artifacts were also observed on a second compromised machine.

![Second Machine Username Hash](13_Username_Hash_Second_Machine.png)

---

# 14. Account Dumped by Attacker

Evidence indicates the attacker attempted to dump account information during the attack.

![Account Dumped](14_Account_Attacker_Dumped.png)

---

# 15. Ransomware Download Link

Finally, the investigation reveals the link used to download ransomware onto the compromised host.

![Ransomware Download](15_Link_Used_Download_Ransomware.png)

---

# Key Takeaways

This investigation demonstrates several common attacker techniques used during a multi-stage compromise:

* Malware execution using legitimate Windows binaries
* File implantation and execution
* Scheduled task persistence
* Command and Control communication
* Privilege escalation via UAC bypass
* Credential dumping
* Lateral movement across hosts
* Ransomware deployment

Understanding these behaviors allows SOC analysts to detect similar attack patterns and respond quickly to emerging threats.

---

# Related Investigations

* Boogeyman Investigation
* Boogeyman 2 Investigation

Full SOC Investigation Repository:

https://github.com/chrisalee27-dotcom/SOC-Level-1-Capstone
