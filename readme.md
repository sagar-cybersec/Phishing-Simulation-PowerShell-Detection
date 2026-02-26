## 📌 Project Overview

This project simulates a phishing campaign using GoPhish and detects malicious PowerShell execution using Wazuh SIEM. We will be luring our users by sending a payroll phishing email & at the same time delivering
a malicious(Not actual malware) payload into the same device to demonstrate detection and alert triage capabilities.


The objective of this lab is to:

- Simulate a phishing scenario
- Trigger PowerShell execution on victim machine
- Detect activity using custom Wazuh rules
- Perform SOC L1 triage using 5W + 1H methodology

---

## 🖥️ Lab Architecture

### 🔹 Attacker Machine
- Kali Linux
- GoPhish installed
- IP: `192.168.0.50`

### 🔹 Victim Machine
- Windows 11 Pro
- Wazuh Agent Installed
- IP: `192.168.0.20`

### 🔹 SOC Analyst Machine
- Windows 11 Pro
- Remote accees into Wazuh Server using SSH 
- IP: `192.168.0.10`

### 🔹 Wazuh Server
- IP: `192.168.0.30`

### 🔹 SIEM
- Wazuh Manager
- Custom detection rules configured

---

## 🎯 Attack Simulation

### 1. Phishing Email

- Phishing email was sent using GoPhish tool in Kali Linux. I created a landing page, email template, added users, and created a campaign to launch a phishing simulation.
- Python HTTP server was hosted on Kali machine mimicing attackers device and malicious file is being stored in the same device to deliver payload.

---

### 2️. Malicious Landing Page

- Fake payroll login portal is being created to bait the users.
- User clicks embedded link/button and gets redirected to an employee payroll portal.
- Once user enters their username and password. Clicking on signin gets connected to server in kali and request .hta file to be downloaded in user's/victim's machine.
- PowerShell payload gets executed when user open that file.
- Users login credentials get stored in attacker's device which can be exploited by attacker to perform further actions based on their goal.

> ⚠️ Note: The payload used in this lab is non-malicious and strictly for educational purposes.

---

## 🚨 Detection Engineering

### 🔹 Default Wazuh Rule Triggered

- Rule ID: `67027`
- Description: Windows PowerShell execution detected

---

### 🔹 Custom Rule Creation

Location: /var/ossec/etc/rules/local_rules.xml

Custom rule:

```xml
<group name="windows,powershell_detection">

  <rule id="100510" level="13">
    <if_sid>67027</if_sid>
    <match>ExecutionPolicy Bypass</match>
    <description>Suspicious PowerShell Execution - ExecutionPolicy Bypass</description>
  </rule>

<rule id="100511" level="15">
  <if_sid>67027</if_sid>
  <match>-EncodedCommand</match>
  <description>High Risk PowerShell Execution - EncodedCommand Used</description>
</rule>

</group>
```

---

## 🔎 SOC L1 Alert Triage (5W + 1H)

### Who
User Sagar has been impacted.

### What
PowerShell executed following phishing link interaction.

### When
Feb 24, 2026 @ 23:07:55.258 EST

### Where
Win11-Client01 with the IP address - 192.168.0.20

### Why
User received an email mimicing a payroll related email but user interacted with phishing email link providing their employee login credentials and running a malicious .hta file.

### How
Phishing email received → Login Credentials submitted → Malicious URL clicked → PowerShell executed.

---

## 🧠 Key Learning Outcomes

- Learned how to create custom Wazuh rules which enhances detection engineering
- PowerShell execution monitoring based on same custom rule
- Detection tuning
- SOC L1 triage methodology

