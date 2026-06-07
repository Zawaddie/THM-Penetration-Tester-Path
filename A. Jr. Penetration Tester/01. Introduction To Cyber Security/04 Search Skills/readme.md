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

Shodan also supports its own query filters, which let you narrow results significantly:

<img width="1252" height="349" alt="image" src="https://github.com/user-attachments/assets/3b43e0aa-dc9c-43ba-a25d-efee8ec08010" />
