# Cyber Kill Chain

In this [room](https://tryhackme.com/room/cyberkillchain) we explore the Cyber Kill Chain by Lockheed Martin.

---
##  Introduction

A Cybersecurirty Framework:
- Inspired by the military kill chains,
-  Introduced by Lockheed Martin in 2011.
-  Created to help organisations defend against cyber attacks by understanding how they are conducted.

The Cyber Kill Chain divides an attack into seven stages:

1. **Reconnaissance:** In the first stage, the attacker gathers information about the target
2. **Weaponisation:** Once proper reconnaissance is conducted, the attacker creates a deliverable payload or modifies an existing one based on the target system’s vulnerabilities
3. **Delivery:** Once ready, the attacker sends the weaponised payload to the target
4. **Exploitation:** Once executed, the payload exploits a vulnerability in the target’s system
5. **Installation:** The exploitation enables the attacker to install a backdoor or malware to maintain persistence in the target’s environment
6. **Command & Control (C2):** Using the installed backdoor, the attacker can control the compromised system
7. **Actions on Objectives:** Reaching this far, the attacker can now carry out further actions such as data exfiltration or other systems’ exploitation

When an organisation learns about each stage, it has a better chance of breaking the chain and interrupting an attack while it is in progress.


---
## Reconnaissance

The act of gathering information about the target’s vulnerabilities and weaknesses to discover potential entry points.

Reconnaissance can be divided into two types: 
1. **Passive:** attacker performs their activities without making any “noise,” for example, using open-source intelligence (OSINT).
   Few examples of passive reconnaissance:
   - The WHOIS database can reveal contact information, registration dates, and domain owners unless hidden using a privacy service add-on.
   - Querying the DNS database can reveal the DNS servers and public servers’ IP addresses.
   - Website reconnaissance by crawling and scraping,
   - social media reconnaissance
   - Google Dorking, i.e., using search engines to reveal sensitive information and confidential files.
  
2. **Active:** the attacker cannot remain completely quiet and invisible; it requires some form of interaction with the target organisation, such as using social engineering against the target’s personnel or scanning a target system for vulnerabilities.
   Example of Active Reconnaisance:
   -  Network port scanning, which identifies live hosts and running services.
   -  More intrusive examples include vulnerability scanning to identify weaknesses in the target’s public services.
   -  Physical reconnaissance is typical, where the attacker might visit the target’s premises to identify entry points, study the security measures, and observe personnel behaviour.



### Countermeasures Against Reconnaissance: Examples
Defence tactics include minimising public information exposure. This countermeasure is achieved by limiting the information on websites, social media accounts, and DNS records. 

Furthermore, the WHOIS records should not reveal any names or addresses that are best left private; many registrars offer a privacy service, usually for an added cost.

Monitoring and analysing network traffic and logs is a must to detect vulnerabilities and network port scans. The security team must monitor the network traffic actively to detect scanning traffic. Checking the service logs is also necessary to uncover reconnaissance attempts.

---
## 
