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

**NOTE:**

OSSTMM prescribes the Security Test Audit Report (STAR) format for deliverables, enforcing consistency and enabling cross-team comparability.

Its scientific rigor is both its greatest strength and its primary barrier. The OSSTMM framework makes results auditable and comparable, which is rare in penetration testing. However, the learning curve is steep, full implementations can be time-consuming, and experienced OSSTMM practitioners are harder to find than those trained in OWASP or NIST methodologies. OSSTMM is best suited for organizations that need repeatable, auditable security measurements and are willing to invest in mastering the methodology.

---

## OWASP WSTG

Assume you are testing a web application for an online retailer. 

With dozens of potential attack surfaces in a single web application, it is easy to either test the same things twice or miss entire vulnerability classes altogether.

The Open Web Application Security Project (OWASP) addresses this problem with the [Web Security Testing Guide (WSTG)](https://owasp.org/www-project-web-security-testing-guide/). 

While OWASP is most widely known for the [OWASP Top Ten](https://owasp.org/www-project-top-ten/) list of critical web vulnerabilities, the WSTG goes far deeper: it is a **comprehensive, community-driven framework** that organizes web application testing into over **90 discrete test cases grouped across twelve categories.**

Those twelve categories span the full attack surface of a modern web application. They include:
- information gathering,
- configuration and deployment management,
- identity management,
- authentication,
- authorization,
- session management,
- input validation,
- error handling,
- cryptography,
- business logic,
- client-side testing,
- API testing.

Each category contains numbered test cases (e.g., WSTG-INPV-01 for reflected cross-site scripting) with step-by-step guidance on what to test and how to test it.

The WSTG adopts a risk-based approach: **vulnerabilities are prioritized based on their exploitability and impact,** not simply cataloged. This approach means the framework helps testers focus their efforts where they matter most.

### Phases: Security Across the SDLC

What sets the WSTG apart from many penetration testing frameworks is that it does not treat security as a single event. Instead, it aligns testing across five phases of the **Software Development Life Cycle (SDLC),** embedding security from initial planning through post-launch maintenance.

Let's see how this works for our online retailer, ShopSecure Inc., which is building a new customer portal.

**Phase 1: Before development begins.** 

Security requirements and regulatory obligations are established upfront. For ShopSecure, this means defining that the portal must comply with PCI DSS (since it processes payments) and establishing measurable criteria, such as the maximum acceptable time for patching vulnerabilities.

**Phase 2: During definition and design.*** 

The application architecture is reviewed for security flaws before any code is written. ShopSecure's team creates threat models for the payment flow, identifying that the checkout API will be a high-value target and designing rate-limiting and input validation controls from the start.

**Phase 3: During development.** 

Code is vetted through walkthroughs and reviews. ShopSecure's developers review the authentication module against WSTG test cases for credential handling (WSTG-ATHN), identifying a flaw in which password reset tokens do not expire.

**Phase 4: During deployment.** 

Security controls are verified in the production environment. ShopSecure's team runs a penetration test against the staged application, verifying that default credentials have been changed, that TLS is properly configured, and that no debug endpoints are exposed.

**Phase 5: During maintenance and operations.** 

Security is maintained post-launch through periodic health checks, especially after updates. When ShopSecure pushes a new product recommendation feature three months later, the relevant WSTG test cases are re-executed to ensure the update has not introduced new vulnerabilities.

**NOTE:**

The WSTG's greatest strength is its exhaustive, practical coverage. With over 90 test cases, each containing specific procedures and expected results, it gives testers a concrete roadmap rather than abstract principles. It also benefits from continuous updates from a global community of security professionals, keeping it current with emerging vulnerability classes and modern architectures such as SPAs and microservices.

On the downside, a full implementation of every test case can be impractical for resource-constrained teams. Some tests require specialized expertise in areas like cryptographic analysis or business logic testing. There is also a risk of falling into a checklist mentality, where testers mechanically execute test cases without stepping back to assess the application's overall risk posture. 

The best practitioners use the WSTG as a foundation while applying critical thinking beyond the checklist.


---

##  NIST SP 800-115

**scenario:**

You work as a security analyst for a government agency or a large enterprise that contracts with the federal government. Your organization needs a security assessment, but the results must satisfy auditors, comply with federal guidelines, and be defensible in the event of oversight inquiries. A penetration testing report full of informal commentary and subjective risk ratings won't cut it. You need a methodology that federal regulators recognize and trust.

The [NIST Special Publication 800-115](https://csrc.nist.gov/pubs/sp/800/115/final), titled Technical Guide to Information Security Testing and Assessment apd Published by the National Institute of Standards and Technology (NIST), provides a foundational framework for systematically evaluating the security posture of information systems. 

Its principles are broadly applicable and widely adopted in the private sector, especially by organizations that value structured, repeatable processes.

NIST SP 800-115 is not just a penetration testing framework, it is broader and covers the full spectrum of security testing and assessment techniques, from document reviews and log analysis to vulnerability scanning and full penetration tests. 

It treats penetration testing as one technique among several, all of which serve the goal of validating whether security controls work as intended.

Three core objectives driving the framework: 
1. **Identify vulnerabilities** in systems, networks, and applications.
2. **Validate security controls** by testing whether they perform as expected under adversarial conditions.
3. **Assess exploitability** by simulating real-world attack scenarios to determine whether a threat actor can actually leverage identified weaknesses.

### Phases: A Walkthrough

NIST SP 800-115 structures testing into three phases. 

Consider the scenario: where your team has been engaged to assess the security of GovNet, a mid-size federal agency's internal network spanning 500 hosts, 3 data centers, and a public-facing citizen services portal.

**Phase 1: Planning**

Before any testing begins, the objectives, scope, and rules of engagement are formally defined and documented. 

For GovNet, this means specifying which network segments are in scope (the citizen portal and its supporting backend) and which are excluded (the classified enclave). It also means establishing communication protocols: who gets notified if a critical vulnerability is found mid-test, what hours testing is permitted, and what constitutes an emergency stop condition. The planning phase produces a formal test plan that all stakeholders sign off on.

**Phase 2: Execution.** 

This is where active testing happens. 

NIST SP 800-115 groups execution activities into four technique categories:

1. **Review techniques:** Examining documentation, policies, system configurations, and rule sets. At GovNet, this includes reviewing firewall rules and access control lists for misconfigurations.
2. **Target identification and analysis:** Discovering and fingerprinting live hosts, open ports, and running services. At GovNet, you scan the citizen portal's infrastructure and identify 12 internet-facing services.
3. **Target vulnerability validation:** Confirming that identified weaknesses are real and exploitable, not false positives. At GovNet, a scan flags a potential SQL injection on the portal's search function; you manually validate it by crafting a test query that returns database version information.
4. **Penetration testing:** Simulating adversarial attacks to test the depth of exploitation possible. At GovNet, you chain the confirmed SQL injection with a privilege escalation on the database server to demonstrate that an external attacker could access internal citizen records.

Notice how NIST SP 800-115 treats these as a progression from passive to active, from low-impact to high-impact. Not every engagement needs to reach the penetration testing stage; sometimes, a review and vulnerability validation are sufficient for the assessment's objectives.

**Phase 3: Post-Testing.**

The focus shifts to analyzing results, prioritizing risks, and delivering actionable remediation strategies. For GovNet, this means categorizing findings by severity (the SQL injection chain is critical; a missing HTTP security header is low), mapping each finding to the specific control it bypasses, and providing concrete remediation steps. NIST SP 800-115 emphasizes that findings should be actionable: telling a client "you have a SQL injection" is not enough; explaining which parameter is vulnerable, what data is at risk, and how to remediate it is the standard.

**NOTE:**

NIST SP 800-115's strengths lie in its flexibility and institutional credibility. It adapts to diverse environments, from traditional data centers to cloud infrastructure and IoT deployments, because it defines technique categories rather than prescribing specific tools. Its association with NIST gives it immediate recognition in government, defense, and regulated industries. It also promotes standardization, making it easier for organizations with multiple testing teams to maintain consistent quality.

On the downside, it does not enforce audit frequencies or penalties. Unlike PCI DSS, which mandates annual penetration tests, NIST SP 800-115 is guidance, not regulation. This can limit its adoption as a standalone driver in highly regulated sectors. It also requires skilled personnel; the framework assumes testers can execute the full range of techniques from document review to exploitation, which demands a broad skill set.



---

## 




---
## Conclusion

We have: 
- described the purpose and structure of the major penetration testing frameworks.
- Compared frameworks based on their scope, methodology, and intended use cases.
- Selected an appropriate framework for a given engagement scenario.
- Explained how MITRE ATT&CK complements traditional penetration testing methodologies.




