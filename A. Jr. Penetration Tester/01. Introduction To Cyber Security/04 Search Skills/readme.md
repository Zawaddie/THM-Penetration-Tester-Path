# Search Skills

Learnt how to efficiently search the Internet and use specialised services and technical docs for information

---
## Introduction

Whether you're hunting down an exploit, trying to understand how a tool works, tracking a threat actor, knowing where to search is just as important as knowing what to search for.

Using the internet and it's resources effectively is a critical skill in cyber security.

---

## Shodan

Shodan is often described as a search engine for the Internet of Things (IoT), but that undersells it. Shodan continuously scans the internet, searching for networking equipment, industrial control systems, traffic cameras, and virtually anything else with a public network connection to see what's running and where.

<img width="1362" height="726" alt="image" src="https://github.com/user-attachments/assets/6f1b510f-745c-4a56-8913-e583a84861d4" />

For example, searching apache 2.4.1 will return a list of servers advertising that version in their HTTP headers, broken down by country, organisation, and port. During a penetration test or vulnerability assessment, that kind of visibility is extremely useful, particularly when paired with a known CVE affecting that version.

<img width="1346" height="650" alt="image" src="https://github.com/user-attachments/assets/90dc3afb-010f-4a4a-92ba-271d9b8f79cd" />


Shodan also supports its own query filters, which let you narrow results significantly:

<img width="1252" height="349" alt="image" src="https://github.com/user-attachments/assets/3b43e0aa-dc9c-43ba-a25d-efee8ec08010" />

<img width="669" height="635" alt="image" src="https://github.com/user-attachments/assets/b237ed12-554d-4b93-8c3d-cbd1ed353877" />


---
## VirusTotal

VirusTotal collates results from over 70 antivirus engines and website scanners into a single interface. 

When you Submit a file, a URL, a domain, or a file hash, virusTotal will tell you whether any of those engines have flagged it as malicious or not.

<img width="1114" height="626" alt="image" src="https://github.com/user-attachments/assets/da780f23-5c2c-4319-ac87-46ebc5947090" />

VirusTotal is a popular resource in the blue teaming community for obtaining a general consensus on suspicious files and links, as well as for gathering intelligence on new threats on the move. However it is not foolproof.

<img width="659" height="608" alt="image" src="https://github.com/user-attachments/assets/ce842493-4729-43c8-ae8c-b6db9059fde1" />

---

## Vulnerability Databases

The Common Vulnerabilities and Exposures (CVE) programme is the closest thing the industry has to a universal dictionary of known vulnerabilities.

<img width="1364" height="688" alt="image" src="https://github.com/user-attachments/assets/3f6316c7-793b-4f74-9ecb-e1a948ab944d" />

<img width="990" height="632" alt="image" src="https://github.com/user-attachments/assets/f626ac51-43c1-45bb-a5d1-e58ad87a69db" />


Each confirmed vulnerability is assigned a unique identifier in the format ```CVE-YEAR-NUMBER```, such as ```CVE-2025-55182```. If the vulnerability is impactful enough, it may even get a moniker. 

You may have heard of vulnerabilities such as **Heartbleed, React2Shell, and Log4Shell.**

These vulnerabilities are given a score (CVSS) based on a variety of factors, such as:

- **Impact** - What damage can this vulnerability lead to?
- **Complexity** - Is the vulnerability easy to exploit or not? 
- **Availability** - How likely is it that someone can exploit this?

Organisations use scoring like this to prioritise their level of risk. Addressing the highest scoring first.
These identifiers function as a reference point among vendors, researchers, security tools, and documentation, ensuring that everyone discussing a vulnerability refers to the same issue. 

Websites like ExploitDB compile this information alongside "Proof of Concepts" (PoCs), which are scripts capable of demonstrating the vulnerability.

<img width="1346" height="301" alt="image" src="https://github.com/user-attachments/assets/ae50e915-ac7d-4896-b64c-658e1ad794e4" />


---



## Technical Documentation (MAN)

Product and Tool Documentation

Each major security tool or platform provides its own documentation, which is the most reliable and up-to-date than any third-party tutorials.

When you're troubleshooting unexpected behaviour or trying to understand how to use a tool in a certain way, the official documentation should always be your first stop - not your last.

Linux MAN pages gives a documentation  of linux command Line tools.

---

## GitHub

GitHub can be a great resource for staying updated on the latest threats and vulnerabilities. Researchers often publish proof-of-concept (PoC) code, exploitation tools, and detailed technical reports there, which are usually faster than official channels. 

Searching for a CVE identifier (e.g., CVE-2026-1337) directly on GitHub often reveals repositories containing PoC code, scanner scripts, or detailed analyses of the vulnerability.

That said, not all PoCs are equally reliable. Some are incomplete, some are intentionally flawed, and occasionally a "PoC" repository is malicious itself. Always verify what you're about to execute.

---







