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

**Account Used:** DESKTOP-N5DHB$

**Method:** Service Logon

### Event Details

**Event ID:** 4624

**Time:** 2026-08-09 4:34:57 PM

**Username:** DESKTOP-N5DHB$

**Domain:** WORKGROUP

**Logon Type:** 5

**Source Network Address:** -

**Workstation Name:** -

**Authentication Package:** Negotiate

### Findings

The event shows a successful logon for the computer account DESKTOP-N5DHB$ at 2026-08-09 4:34:57 PM.

The Logon Type 5 indicates that this was a Service Logon, rather than an interactive user login. The authentication package used was Negotiate.

No source network address or workstation name was recorded (-), which is consistent with this being a service logon rather than a remote network login.

The activity appears to be expected/benign because the account is the local computer account and the event represents service-related authentication within the Windows system.

No related failed logon attempts were identified before this event during the review period.

### Verdict

Expected

### Evidence

[Screenshot of Event ID 4624]

---

# Investigation 2 — Event ID 4625

## Failed Logon

### Objective

Identify and investigate unsuccessful authentication attempts.

### Test Activity

**Date/Time:** [Date and time]

**Account Used:** [Username]

**Number of Failed Attempts:** [Number]

### Event Details

**Event ID:** 4625

**Time:** [Timestamp]

**Target Username:** [Username]

**Failure Reason:** [Reason]

**Logon Type:** [Type]

**Source Network Address:** [IP address]

**Workstation Name:** [Name]

### Investigation

Answer:

1. Which account was targeted?
2. How many failed attempts occurred?
3. Were the attempts close together in time?
4. What was the source IP?
5. What Logon Type was used?
6. Did a successful 4624 event occur afterward?
7. Could the activity represent a brute-force attempt?

### Findings

[Write your findings here.]

### Verdict

☐ Normal User Error
☐ Suspicious
☐ Possible Brute Force
☐ Requires Further Investigation

### Evidence

[Screenshot of Event ID 4625]

---

# Investigation 3 — Event ID 4740

## Account Lockout

### Objective

Investigate an account lockout and determine what activity occurred before the account was locked.

### Test Activity

**Date/Time:** [Date and time]

**Test Account:** [Username]

**Lockout Threshold:** [e.g., 3 attempts]

### Event Details

**Event ID:** 4740

**Time:** [Timestamp]

**Target Account:** [Username]

**Caller Computer Name:** [Computer name]

### Investigation

Search for related **4625 Failed Logon** events before the 4740 event.

### Event Correlation

```text
Time                Event ID        Activity
------------------------------------------------
[Time]              4625            Failed logon
[Time]              4625            Failed logon
[Time]              4625            Failed logon
[Time]              4740            Account locked
```

### Investigation Questions

1. Which account was locked?
2. When did the lockout occur?
3. Which computer was associated with the lockout?
4. How many failed logons occurred before the lockout?
5. Were the failed attempts close together?
6. Was the lockout expected?

### Findings

[Write your findings here.]

### Verdict

☐ Expected Lab Activity
☐ Suspicious
☐ Possible Brute Force
☐ Requires Further Investigation

### Evidence

[Screenshot of Event ID 4740 and related 4625 events]

---

# Investigation 4 — Event ID 4720

## User Account Created

### Objective

Investigate the creation of a new Windows user account.

### Test Activity

**Date/Time:** [Date and time]

**Account Created:** [Username]

**Account Creator:** [Username]

### Event Details

**Event ID:** 4720

**Time:** [Timestamp]

**New Account Name:** [Username]

**Account Domain:** [Domain]

**Creator Account:** [Username]

### Investigation

Answer:

1. Which account was created?
2. Who created the account?
3. When was it created?
4. Was the account creation expected?
5. Was the creator account authorized to create users?
6. Were there other suspicious events around the same time?

### Findings

[Write your findings here.]

### Verdict

☐ Authorized / Expected
☐ Suspicious
☐ Requires Further Investigation

### Evidence

[Screenshot of Event ID 4720]

---

# 5. Event Correlation

A SOC Analyst should not always investigate events individually.

For example:

```text
4625 — Failed Logon
       ↓
4625 — Failed Logon
       ↓
4625 — Failed Logon
       ↓
4740 — Account Lockout
```

This sequence may indicate repeated authentication failures resulting in an account lockout.

Similarly:

```text
4720 — New Account Created
       ↓
4624 — Successful Logon
```

This sequence should be investigated to determine whether the newly created account was subsequently used.

### Correlation Findings

[Describe any relationships you discovered between the events.]

---

# 6. Indicators and Important Evidence

| Indicator      | Value   |
| -------------- | ------- |
| Username       | [Value] |
| Source IP      | [Value] |
| Hostname       | [Value] |
| Timestamp      | [Value] |
| Event ID       | [Value] |
| Logon Type     | [Value] |
| Failure Reason | [Value] |

---

# 7. MITRE ATT&CK Mapping

If applicable, map suspicious activity to the relevant MITRE ATT&CK technique.

**Technique:** [Technique name]

**Technique ID:** [Txxxx]

**Reason:** [Explain why the activity maps to this technique.]

If no applicable technique was identified:

> No specific MITRE ATT&CK technique was assigned because the activity was generated as part of a controlled lab scenario.

---

# 8. Investigation Summary

### 4624 — Successful Logon

**Result:** [Summary]

### 4625 — Failed Logon

**Result:** [Summary]

### 4740 — Account Lockout

**Result:** [Summary]

### 4720 — User Account Creation

**Result:** [Summary]

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

# 11. Screenshots

Include screenshots showing:

1. Windows VM
2. Event Viewer
3. Sysmon installation/configuration
4. Event ID 4624
5. Event ID 4625
6. Event ID 4740
7. Event ID 4720
8. Related/correlated events

---

# 12. Tools Used

* Windows 10
* VirtualBox
* Windows Event Viewer
* Sysmon
* Command Prompt
* PowerShell [if used]
* GitHub [for documentation]

---

# 13. Disclaimer

All activities in this project were performed in an isolated home lab using a controlled Windows virtual machine. No unauthorized systems or accounts were targeted.

