# Penetration Testing Frameworks
Exploring the landscape of penetration testing frameworks.

---

## Introduction

Imagine we have our first penetration testing engagement. The client is a mid-size e-commerce company, and they want you to evaluate the security of their web application, internal network, and employee security awareness. 

Without a structured approach, a penetration test quickly becomes a disorganized collection of random checks and we might miss critical attack surfaces, skip important documentation, or deliver findings that the client cannot act on. 

**A penetration testing framework** is a structured methodology that guides security professionals through every stage of an engagement, from initial planning and scoping to exploitation, reporting, and remediation validation. 

Relying on a structured methodology provides several benefits:
- It ensures **thoroughness** so that critical areas are not overlooked.
- It promotes **consistency** so that different testers on the same team produce comparable results.
- It supports **compliance** by aligning the assessment with regulatory requirements.
- It also **improves communication** because clients, auditors, and stakeholders can understand and trust a process grounded in a recognized standard.

Some penetration testing frameworks: 

1. **[Open Source Security Testing Methodology Manual (OSSTMM)](https://www.isecom.org/OSSTMM.3.pdf)), a scientific, metrics-driven approach to security testing
2. **[OWASP Web Security Testing Guide (WSTG)](https://owasp.org/www-project-web-security-testing-guide/), the go-to framework for web application assessments
3. [NIST Special Publication 800-115](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-115.pdf), the U.S. government's technical guide to security testing and assessment
4. [Penetration Testing Execution Standard (PTES)](http://www.pentest-standard.org/index.php/Main_Page), a practical, phase-driven standard that mirrors how real engagements are conducted
5. [Information Systems Security Assessment Framework (ISSAF)](https://untrustednetwork.net/files/issaf0.2.1.pdf), a historically influential methodology with a detailed nine-step assessment model.

Worthy noting also is the:
- [MITRE ATT&CK](https://attack.mitre.org/) a complementary knowledge base that maps adversary tactics and techniques.
- [The WASC Threat Classification](http://projects.webappsec.org/w/page/13246978/Threat%20Classification),
- [CSA Cloud Controls Matrix](https://cloudsecurityalliance.org/research/cloud-controls-matrix),
- [OWASP Mobile Application Security Testing Guide(MASTG)]](https://mas.owasp.org/MASTG/)
- [PCI DSS Penetration Testing Guidelines](https://listings.pcisecuritystandards.org/documents/Penetration-Testing-Guidance-v1_1.pdf),
- [CBEST Framework](https://www.bankofengland.co.uk/financial-stability/operational-resilience-of-the-financial-sector/cbest-threat-intelligence-led-assessments-implementation-guide)


---
## Open Source Security Testing Methodology Manual (OSSTMM)

### Overview

We can't manage what we can't measure. 

Developed by the Institute for Security and Open Methodologies (ISECOM).

The OSSTMM applies the scientific method to security testing. Its defining characteristic is metrics over opinions: rather than delivering subjective risk judgments, OSSTMM produces quantifiable, verifiable, and repeatable results.

OSSTMM organizes testing around five security channels, reflecting its philosophy that security is not just a network problem:

1. **Human Security (HUMSEC):** Social engineering and human-factor vulnerabilities.
2. **Physical Security (PHYSSEC):** Physical access controls, from badge readers to tailgating.
3. **Wireless Communications (SPECSEC):** Wi-Fi, Bluetooth, RFID, and other electromagnetic signals.
4. **Telecommunications (COMSEC):** Phone systems, VoIP, fax, and modem infrastructure.
5. **Data Networks (DATASEC):** Network services, firewalls, and application-layer protocols.

An organization might have flawless firewall rules, but if an attacker can tailgate into the server room (PHYSSEC) or social-engineer a credential reset (HUMSEC), those network controls become irrelevant. The five channels ensure nothing is overlooked.

At the heart of OSSTMM's quantitative approach are Risk Assessment Values (RAVs). 

A **RAV** measures the balance between the total attack surface (exposure) and the controls protecting it.
- A positive RAV indicates residual risk;
- A RAV near zero suggests controls are well-matched to exposure.

This numeric output means that two testers assessing the same target should arrive at comparable results, much as two engineers measuring the same beam should calculate similar stress tolerances.

### Phases: 
The OSSTMM testing cycle has four phases.

Scenario: our team is assessing the external network of FinVault Corp, a financial services company, scoped to **10.0.113.0/24** and their customer portal at **portal.finvault-corp.thm.**

**Phase 1: Induction** 

Covers **enumeration and verification.** We map what exists and confirm it is real. At FinVault, we query DNS, review certificate transparency logs, and discover subdomains like **vpn.finvault-corp.thm** and **mail.finvault-corp.thm.** We then verify each asset is live and responsive. The output is a confirmed inventory of the target environment.

**Phase 2: Interaction** 

Covers **qualification and quantification.** We  actively probe the verified assets and assess their relevance. At FinVault, you connect to each service, fingerprint its technology, and quantify the exposure: 12 externally reachable services across 8 hosts, 4 of which accept unauthenticated connections. These findings feed directly into the attack surface calculation.

**Phase 3: Inquiry**

Covers **privilege escalation and verification escalation.** We test whether the measured exposure can be converted into unauthorized access. At FinVault, we discover an IDOR vulnerability in the customer portal that allows one authenticated user to read another customer's account statements. Verification escalation confirms the scope: read access to 12,000 accounts, no write access.

**Phase 4: Intervention** 

Covers **quarantine, audit, and enticement.** We address findings and examine the broader control environment. At FinVault, the vulnerable endpoint is restricted. At the same time, a patch is developed (quarantine), the wider access control model is examined for similar flaws (audit), and a canary token is deployed to test internal detection capabilities (enticement).

Note:
OSSTMM prescribes the Security Test Audit Report (STAR) format for deliverables, enforcing consistency and enabling cross-team comparability.

Its scientific rigor is both its greatest strength and its primary barrier. The OSSTMM framework makes results auditable and comparable, which is rare in penetration testing. However, the learning curve is steep, full implementations can be time-consuming, and experienced OSSTMM practitioners are harder to find than those trained in OWASP or NIST methodologies. OSSTMM is best suited for organizations that need repeatable, auditable security measurements and are willing to invest in mastering the methodology.

---





---
## Conclusion

We have: 
- described the purpose and structure of the major penetration testing frameworks.
- Compared frameworks based on their scope, methodology, and intended use cases.
- Selected an appropriate framework for a given engagement scenario.
- Explained how MITRE ATT&CK complements traditional penetration testing methodologies.




