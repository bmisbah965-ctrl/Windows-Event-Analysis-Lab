# SOC Analyst L1 Home Lab

## Windows Authentication & Account Activity Investigation

### 1. Project Overview

**Project Name:** Windows Security Event Monitoring & Investigation

**Role:** SOC Analyst L1

**Environment:**

* Windows 10 Virtual Machine
* VirtualBox
* Windows Event Viewer
* Sysmon
* Sysmon Configuration: https://github.com/olafhartong/sysmon-modular/blob/master/sysmonconfig.xml
* Host OS: Windows

### 2. Project Objective

The objective of this project is to build a basic SOC Analyst L1 home lab for monitoring and investigating Windows security events.

The investigation focuses on four important Windows Security Event IDs:

| Event ID | Event                | Investigation Purpose                       |
| -------- | -------------------- | ------------------------------------------- |
| 4624     | Successful Logon     | Identify successful authentication activity |
| 4625     | Failed Logon         | Investigate failed authentication attempts  |
| 4740     | Account Locked Out   | Investigate account lockout activity        |
| 4720     | User Account Created | Investigate creation of new user accounts   |

The project demonstrates how a SOC Analyst can use Windows Event Viewer and security logs to identify, investigate, and document authentication-related activity.

---

# 3. Lab Setup

### Virtual Machine

**Operating System:** Windows 10

**Virtualization Platform:** VirtualBox

**VM Resources:**

* RAM: 4 GB
* CPU: 6 cores
* Storage: 50 GB

### Security Tools

* Windows Event Viewer
* Sysmon
* sysmon-config-master

---

# 4. Investigation Methodology

For each event, the following investigation process was followed:

```text
Generate Activity
       ↓
Event Generated
       ↓
Locate Event in Event Viewer
       ↓
Examine Event Details
       ↓
Identify Relevant Fields
       ↓
Correlate Related Events
       ↓
Determine Whether Activity is Normal or Suspicious
       ↓
Document Findings
```

---

# Investigation 1 — Event ID 4624

## Successful Logon

### Objective

Identify and analyze successful authentication events on the Windows system.

### Test Activity

**Date/Time:** 2026-08-09 4:34:57 PM

**Account Used:** SYSTEM

**Method:** Service Logon

### Event Details

**Event ID:** 4624

**Time:** 2026-08-09 4:34:57 PM

**Username:** SYSTEM

**Domain:** WORKGROUP

**Logon Type:** 5

**Source Network Address:** -

**Workstation Name:** -

**Authentication Package:** Negotiate

### Findings

The event shows a successful logon for the computer account SYSTEM at 2026-08-09 4:34:57 PM.

The Logon Type 5 indicates that this was a Service Logon, rather than an interactive user login. The authentication package used was Negotiate.

No source network address or workstation name was recorded (-), which is consistent with this being a service logon rather than a remote network login.

The activity appears to be expected/benign because the account is the local computer account and the event represents service-related authentication within the Windows system.

No related failed logon attempts were identified before this event during the review period.

### Verdict

Expected

### Evidence

<img width="635" height="436" alt="4624-successful-logon" src="https://github.com/user-attachments/assets/45921647-ef4c-4483-858d-68a448ebb071" />


# Investigation 2 — Event ID 4625

## Failed Logon

### Objective

Identify and investigate unsuccessful authentication attempts.

### Test Activity

**Date/Time:** 2026-08-09 4:35:12 AM

**Account Used:** Test User

**Method:** Interactive

**Number of Failed Attempts:** 3

### Event Details

**Event ID:** 4625

**Time:** 2026-08-09 4:35:12 AM

**Target Username:** Test User

**Failure Reason:** Unknown user name or bad password

**Logon Type:** 2

**Source Network Address:** 127.0.0.1

**Workstation Name:** DESKTOP-40N5DHB

### Findings

The account Test User was targeted by 3 failed logon attempts occurring close together in time. The attempts used Logon Type 2 (Interactive), indicating that the authentication attempts were made through an interactive logon session on the local system.

The Source Network Address was 127.0.0.1, which is IPv4 loopback address, indicating that the activity originated from the same system rather than an external network address.

A 4624 successful logon event occurred afterward, providing additional context for the authentication sequence.

The repeated failed logons followed by a successful logon resembled a possible brute-force pattern. However, these attempts were intentionally generated as part of the controlled home lab, so the activity was considered expected lab activity rather than a real brute-force attack.

### Verdict

Expected Lab Activity

### Evidence

<img width="631" height="439" alt="4625-failed-logon" src="https://github.com/user-attachments/assets/219336c4-d02f-436a-ba49-4a25bcc8a041" />

---

# Investigation 3 — Event ID 4740

## Account Lockout

### Objective

Investigate an account lockout and determine what activity occurred before the account was locked.

### Test Activity

**Date/Time:** 2026-08-09 1:17:07 AM       

**Test Account:** Test User

**Lockout Threshold:** 3 attempts

### Event Details

**Event ID:** 4740

**Time:** 2026-08-09 1:17:07 AM   

**Target Account:** Test User

**Caller Computer Name:** DESKTOP-40N5DHB$

### Event Correlation

```text
Time                Event ID        Activity
------------------------------------------------
1:17:05 AM            4625            Failed logon
1:17:05 AM            4625            Failed logon
1:17:06 AM            4625            Failed logon
1:17:07 AM            4740            Account locked
```

### Findings

The account Test User was locked out at 1:17:07 AM, generating Event ID 4740.

The Caller Computer Name recorded in the event was DESKTOP-40N5DHB$.

Three 4625 Failed Logon events were observed immediately before the account lockout. The failed attempts occurred close together in time, indicating repeated authentication failures preceding the lockout.

The activity was intentionally generated as part of the controlled home lab, so the account lockout was considered expected lab activity.

### Verdict

☐ Expected Lab Activity

### Evidence

<img width="615" height="433" alt="4740-user-account-locked" src="https://github.com/user-attachments/assets/69fdadd9-88b7-4e63-8409-1b7132bd1b57" />


# Investigation 4 — Event ID 4720

## User Account Created

### Objective

Investigate the creation of a new Windows user account.

### Test Activity

**Date/Time:** 2026-08-09 1:10:01 AM

**Account Created:** Test User

**Account Creator:** Admin

### Event Details

**Event ID:** 4720

**Time:** 2026-08-09 1:10:01 AM

**New Account Name:** Test User

**Account Domain:** DESKTOP-40N5DHB

**Creator Account:** Admin

### Findings

A new user account named Test User was created by the Admin account at 2026-08-09 1:10:01 AM.

The account creation was an expected activity performed during the controlled lab environment. The Admin account was authorized to create the new user account.

No other suspicious activity was identified around the time of the account creation.

### Verdict

☐ Authorized 

### Evidence

<img width="635" height="429" alt="4720-user-created" src="https://github.com/user-attachments/assets/0fb5c046-44bf-4994-9e2a-d4c705d4d03b" />


# 5. Event Correlation

The events were reviewed together to identify relationships between authentication and account activity.

Correlation Finding 1 — Failed Logons → Account Lockout

Multiple 4625 Failed Logon events were observed close together in time, followed by a 4740 Account Lockout event.

4625 — Failed Logon
       ↓
4625 — Failed Logon
       ↓
4625 — Failed Logon
       ↓
4740 — Account Lockout

This sequence demonstrates repeated authentication failures followed by an account lockout. Since the activity was intentionally generated in the controlled home lab, it was considered expected lab activity.

### Correlation Findings

A 4720 User Account Created event was investigated in relation to subsequent authentication activity. The purpose was to determine whether the newly created account was later used to authenticate to the system.

No confirmed correlation between the 4720 event and a 4624 Successful Logon event for the Test User account was established during this investigation.

Overall, the investigation demonstrated the importance of correlating multiple Windows Security events instead of analyzing each event in isolation.

---

# 6. Indicators and Important Evidence

| Indicator      | Value   |
| -------------- | ------- |
| Username       | Test User |
| Source IP      | 127.0.0.1 |
| Hostname       | DESKTOP-40N5DHB |
| Timestamp      | 2026-08-09 1:17:07 AM |
| Event ID       | 4625, 4740 |
| Logon Type     | 2 — Interactive|
| Failure Reason | Unknown user name or bad password |
| Lockout Threshold | 3 attempts |

---

# 7. MITRE ATT&CK Mapping

No specific MITRE ATT&CK technique was assigned because the activity was generated as part of a controlled lab scenario.

---

# 8. Investigation Summary

### 4624 — Successful Logon

**Result:** A successful Service Logon (Logon Type 5) was observed for the SYSTEM account. No related failed logon activity was identified during the review period. The activity was considered expected/benign.

### 4625 — Failed Logon

**Result:** Three failed Interactive Logon (Logon Type 2) attempts targeting the Test User account were observed from the local system (127.0.0.1). A successful 4624 event occurred afterward. The activity was intentionally generated in the lab and was considered expected lab activity.

### 4740 — Account Lockout

**Result:** Three failed logon attempts occurred close together before the Test User account was locked out. The attempts matched the configured three-attempt lockout threshold. The lockout was considered expected lab activity.

### 4720 — User Account Creation

**Result:** A new user account named Test User was created by the Admin account. The account creation was intentionally performed in the controlled lab and was considered authorized/expected activity.

---

# 9. Lessons Learned

Through this lab, I learned how to:

* Monitor Windows Security logs.
* Identify successful and failed authentication events.
* Investigate account lockouts.
* Investigate newly created user accounts.
* Correlate multiple Windows Event IDs.
* Identify potentially suspicious authentication patterns.
* Use Event Viewer as a basic security monitoring tool.
* Document security investigations.

---

# 10. Conclusion

This project provided hands-on experience with Windows security event monitoring and basic SOC investigation techniques.

The main focus was not only identifying individual events but also understanding the context surrounding those events and correlating related activity.

The lab demonstrates a basic SOC Analyst L1 workflow:

**Detect → Investigate → Correlate → Determine → Document**

---

# 11. Disclaimer

All activities in this project were performed in an isolated home lab using a controlled Windows virtual machine. No unauthorized systems or accounts were targeted.

