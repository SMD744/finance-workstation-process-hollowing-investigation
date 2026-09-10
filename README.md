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

## Evidence 

<img width="1440" height="900" alt="1(compromise)" src="https://github.com/user-attachments/assets/944b4c68-13c6-421c-9b29-d232a20a8e30" />


## 2. Parent-Child Process Analysis
The process relationship identified was:

```
notepad.exe (PID 2550)
        |
        └── svchost.exe (PID 3880)
```

This relationship is highly suspicious because a legitimate svchost.exe instance is expected to be launched by services.exe.

## The abnormal:
``` 
notepad.exe → svchost.exe
```
relationship indicated possible process masquerading or code injection from a user application.

## Evidence 

<img width="1440" height="900" alt="2(parent)" src="https://github.com/user-attachments/assets/dbd51fb6-5bb4-4877-9b83-e7bf6af8e05a" />

--

## 3. Memory Injection Analysis
Here we are using memory analysis, suspicious artifacts were identified within:
```
svchost.exe (PID 3880)
```
The investigation identified:
| Indicator         | Finding                  |
| ----------------- | ------------------------ |
| Memory Address    | `0x400000`               |
| Memory Protection | `PAGE_EXECUTE_READWRITE` |
| Magic Bytes       | `4D 5A (MZ)`             |
| Process           | `svchost.exe`            |
| PID               | `3880`                   |

The presence of PAGE_EXECUTE_READWRITE memory combined with MZ magic bytes indicated executable code present directly within process memory.

The MZ signature is associated with Windows Portable Executable files.

This provided evidence of executable code being injected into the process memory.

<img width="1440" height="900" alt="3" src="https://github.com/user-attachments/assets/fa72c8ed-0da4-4586-bcba-ccec04767754" />


## 4. Suspicious Network Communication
The compromised process maintained an established outbound TCP connection:

```
Destination IP: 185.112.55.20
Port: 443
Protocol: TCPv4
State: ESTABLISHED

```

The connection was considered suspicious because the compromised process was communicating externally over port 443 while containing other indicators of malicious activity.

Port 443 can also be used by attackers to blend malicious communications with normal HTTPS traffic.

The connection was considered suspicious because the compromised process was communicating externally over port 443 while containing other indicators of malicious activity.

Port 443 can also be used by attackers to blend malicious communications with normal HTTPS traffic.

<img width="1440" height="900" alt="4pic" src="https://github.com/user-attachments/assets/9bbc7dab-774a-4e11-b1c3-bf664fe902b8" />


## 🧩 Attack Classification
Based on the combined evidence, the activity was classified as:
## Process Hollowing — T1055.012

The classification was supported by three major findings:

```
Abnormal Process Relationship
        +
Injected Executable Code in Memory
        +
Active Outbound Network Communication
        =
Process Hollowing
```

MITRE ATT&CK

Technique: T1055.012 — Process Hollowing

Tactics:

Defense Evasion
Execution

Process hollowing allows malicious activity to execute within the context of a legitimate-looking process, making detection more difficult.

<img width="1297" height="915" alt="5 1" src="https://github.com/user-attachments/assets/7c145969-02b9-458a-a61c-e94ad7ad4608" />
<img width="1297" height="915" alt="5 2" src="https://github.com/user-attachments/assets/e5d3858a-2f70-44e5-8118-f8306360476b" />






