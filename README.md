# 🛡️ SOC Investigation: Finance Workstation Compromise — Process Hollowing

> A hands-on SOC investigation into a suspected Finance workstation compromise involving process hollowing, memory-resident code injection, and suspicious outbound network communication.

---

## 📌 Incident Overview

This investigation was conducted following an alert for a:

**Generic Suspicious Network Connection — Finance Department Workstation**

Although no malicious files were detected on disk, memory analysis revealed suspicious activity within a Windows process.

The investigation identified a compromised:

- **Process:** `svchost.exe`
- **PID:** `3880`
- **Parent Process:** `notepad.exe`
- **Parent PID:** `2550`
- **External IP:** `185.112.55.20`
- **Port:** `443`
- **Protocol:** TCPv4
- **MITRE ATT&CK Technique:** T1055.012 — Process Hollowing

The investigation was based on **Volatility memory dump analysis**.

---

## 🎯 Investigation Objective

The main objectives of this investigation were to:

- Identify the suspicious process responsible for the network connection.
- Analyze the process hierarchy and parent-child relationship.
- Investigate suspicious memory regions.
- Identify evidence of code injection.
- Investigate suspicious outbound network communication.
- Classify the observed attack technique.
- Identify indicators of compromise (IOCs).
- Recommend appropriate incident response actions.

---

## 🔎 Investigation Findings

### 1. Compromised Process Identification

The investigation identified:

text
Process Name: svchost.exe
PID: 3880

The process exhibited abnormal behavior and suspicious memory characteristics.

Normally, svchost.exe should be spawned by services.exe. The identified process instead had an abnormal parent process.

<img width="1440" height="900" alt="1(compromise)" src="https://github.com/user-attachments/assets/944b4c68-13c6-421c-9b29-d232a20a8e30" />


## 2. Parent-Child Process Analysis
The process relationship identified was:
``` notepad.exe (PID 2550)
        |
        └── svchost.exe (PID 3880) ```

This relationship is highly suspicious because a legitimate svchost.exe instance is expected to be launched by services.exe.

## The abnormal:
``` notepad.exe → svchost.exe ```
relationship indicated possible process masquerading or code injection from a user application.


<img width="1440" height="900" alt="2(parent)" src="https://github.com/user-attachments/assets/dbd51fb6-5bb4-4877-9b83-e7bf6af8e05a" />



