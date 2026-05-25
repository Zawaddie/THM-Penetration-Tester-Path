# Offensive Security Intro

## Task 1: What is Offensive Security?

Offensive Security simulates a hacker's actions to find vulnerabilities in a system by breaking into computer systems, exploiting software bugs, and finding loopholes in applications to gain unauthorized access. 

The goal is to understand hacker tactics and enhance our system defences because o outsmart a hacker, you need to think like one."

---

## Task 2: Hacking a First Machine – FakeBank 
**Objectives:**
- Discover hidden pages and exploit an exposed bank transfer function
- Use **Gobuster** to enumerate hidden directories on the FakeBank website and exploit an exposed admin-style functionality to perform an unauthorized bank transfer.

**Tools & Environment:**

* **Gobuster** – Directory brute-forcing tool
* **Terminal (Command Line)**
* **Web Browser**
* **Target URL:** `http://fakebank.thm`
* **Wordlist:** `wordlist.txt`


**Background**

Many web applications expose sensitive endpoints due to misconfiguration or human error. 
Admin portals, debug pages, or internal tools may be left publicly accessible, allowing attackers to discover and abuse them.

**Attack Walkthrough***

**Step 1: Open the Terminal**

Launch the terminal on the TryHackMe machine to interact with the system using command-line tools.

**=> Directory Enumeration with Gobuster**

Run Gobuster to brute-force hidden directories and pages on the FakeBank website:

```bash
gobuster -u http://fakebank.thm -w wordlist.txt dir
```

* `-u` → Target URL
* `-w` → Wordlist used for brute-forcing
* `dir` → Directory enumeration mode


**=> Gobuster Output**

```text
=====================================================
Gobuster v2.0.1
=====================================================
[+] Mode         : dir
[+] Url/Domain   : http://fakebank.thm/
[+] Threads      : 10
[+] Wordlist     : wordlist.txt
[+] Status codes : 200,204,301,302,307,403
=====================================================
/images        (Status: 301)
/bank-transfer (Status: 200)
=====================================================
```

**=>Key Finding:**

* `/bank-transfer` → Accessible page returning **HTTP 200**


**Step 3: Exploitation: Unauthorized Bank Transfer**

The `/bank-transfer` endpoint exposes a functionality that allows money transfers **without authentication**.

1. We navigate to:

   ```
   http://fakebank.thm/bank-transfer
   ```
2. Transfer **$2000**:

   * **From Account:** 2276
   * **To Account:** 8881
3. Submit the transaction.
4. Refresh our account page to confirm the updated balance.

**Result**

* Successfully transferred funds without authentication
* Demonstrates **Broken Access Control** and **Sensitive Function Exposure**

**Security Impact**

This vulnerability allows:

* Unauthorized fund transfers
* Account manipulation
* Financial fraud

**Key Takeaways**

* Directory enumeration is a critical first step in web attacks
* Sensitive endpoints must be protected with authentication & authorization
* Simple misconfigurations can lead to serious financial impact

---

## Task 3: Careers in Cyber Security.

- Ethical hackers (security consultants) 
- Defenders (security analysts fighting cybercrime)

 A few offensive security roles:

* **Penetration Tester** - Responsible for testing technology products for finding exploitable security vulnerabilities.

* **Red Teamer** - Plays the role of an adversary, attacking an organization and providing feedback from an enemy's perspective.

* **Security Engineer** - Design, monitor, and maintain security controls, networks, and systems to help prevent cyberattacks.


