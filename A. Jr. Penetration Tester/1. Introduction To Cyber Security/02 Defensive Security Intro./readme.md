# Defensive Security Intro
This room introduce me todefensive security and related topics: **Threat Intelligence, SOC, DFIR, Malware Analysis, and SIEM.**

---

## Task 1: Introduction

 Blue teams are part of the defensive security landscape and it is concerned with two main tasks:
* Preventing intrusions from occurring
* Detecting intrusions when they occur and responding properly

**Related Tasks:**
- User cyber security awareness
- Documenting and managing assets
- Updating and patching systems: 
- Setting up preventative security devices like firewalls and IPS/IDS
- Setting up logging and monitoring devices Like SIEMs and EDRs

---

## Task 2: Areas of Defensive Security

This task introduced to me two core areas of **defensive security**:

* **Security Operations Center (SOC)** – including **Threat Intelligence**
* **Digital Forensics and Incident Response (DFIR)** – including **Malware Analysis**

### Security Operations Center (SOC)

A team of cybersecurity professionals responsible for continuously monitoring networks and systems to detect, analyze, and respond to security threats.

**Key Areas of Focus**

* **Vulnerabilities**
  Identifying and addressing system weaknesses through patching or mitigation.
  While remediation may not always be handled by the SOC, detection and reporting are critical.

* **Policy Violations**
  Enforcing organizational security policies, such as preventing sensitive data from being uploaded to unauthorized cloud services.

* **Unauthorized Activity**
  Detecting compromised credentials and unauthorized access attempts before attackers can escalate privileges or cause damage.

* **Network Intrusions**
  Identifying malicious activity resulting from phishing, exploited services, or compromised endpoints.

One of the most important SOC functions is **Threat Intelligence**.
It involves collecting and analyzing information about **actual and potential adversaries** to enable a **threat-informed defense**.

A **threat** is any action capable of disrupting or damaging systems. 
Different organizations face different adversaries, such as:

* Nation-state attackers (political motives)
* Ransomware groups (financial motives)
* Cyber-espionage actors
* Insider threats

**Threat Intelligence Lifecycle:**

1. **Collection**
   Data is gathered from:

   * Internal sources (logs, alerts, telemetry)
   * External sources (open-source intelligence, forums, feeds)

2. **Processing**
   Raw data is cleaned, normalized, and structured for analysis.

3. **Analysis**
   Analysts identify:

   * Threat actors
   * Tactics, Techniques, and Procedures (TTPs)
   * Intent and potential impact

4. **Action**
   Intelligence is used to:

   * Predict attacker behavior
   * Improve detection
   * Prepare mitigation and response strategies

### Digital Forensics and Incident Response (DFIR)

* Digital Forensics
* Incident Response
* Malware Analysis

DFIR focuses on investigating security incidents and responding effectively to minimize damage and restore operations


A. **Digital Forensics**

Applies scientific methods to investigate cyber incidents and establish facts.

In defensive security, digital forensics is used to analyze attacks such as Data breaches, Cyber espionage and Intellectual property theft

**Key Forensic Areas:**

* **File System Analysis**
  Examining forensic disk images to recover: Installed software, Created and deleted files and Partially overwritten data

* **System Memory (RAM)**
  Capturing memory images to analyze malware that runs only in memory.

* **System Logs**
  Reviewing event logs to reconstruct attacker activity, even when traces are partially erased.

* **Network Logs**
  Inspecting network traffic to identify malicious communication and intrusion patterns.


B. **Incident Response**

An **incident** can range from a minor policy violation to a full-scale cyber attack such as:

* Website defacement
* Data breaches
* Denial of service
* System compromise

Incident response provides a structured methodology to **reduce impact and recover quickly**.

**Incident Response Phases**

1. **Preparation**

   * Trained response teams
   * Preventive controls and procedures

2. **Detection and Analysis**

   * Identify incidents
   * Assess scope and severity

3. **Containment, Eradication, and Recovery**

   * Stop the spread of the attack
   * Remove the threat
   * Restore affected systems

4. **Post-Incident Activity**

   * Produce reports
   * Document lessons learned
   * Improve future defenses


C. **Malware Analysis**

**Malware Analysis Techniques**

* **Static Analysis**
  Examining malware without executing it. Requires knowledge of assembly language and binary structures.

* **Dynamic Analysis**
  Running malware in a controlled environment to observe behavior such as file changes, network connections, and process creation.


---

## Task 3: Practical Example of Defensive Security

The practical bit on this Room involved a simulation of a SOC analyst responsible for protecting a bank using a SIEM tool which gathers security-related information and events from various sources and presents them in one dashboard and creating an alert when it finds something suspicious.

Not all alerts are malicious, however. It is up to the analyst to use their expertise in cyber security to investigate which ones are harmful.

Following the step-by-step instructions provided within the simulation to navigate through the events helped locate the "flag."

**NOTE:** There are many open-source databases out there, like **AbuseIPDB, and Cisco Talos Intelligence,** where we can perform a reputation and location check for the IP address. Most security analysts use these tools to aid them with alert investigations. 
We can also make the Internet safer by reporting the malicious IPs, for example, on AbuseIPDB.

<img width="685" height="343" alt="image" src="https://github.com/user-attachments/assets/5ffddb11-9910-41fd-83dd-59c0e2375908" />

