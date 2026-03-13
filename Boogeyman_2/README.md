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

After execution, the payload launches a process responsible for continuing the attack cha

