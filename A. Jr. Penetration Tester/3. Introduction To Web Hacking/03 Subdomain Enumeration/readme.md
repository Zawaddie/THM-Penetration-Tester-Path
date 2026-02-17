## Subdomain Enumeration

This THM Room is not Free, Rather it's a Premium one.

In the room, I was able to learn various ways of discovering subdomains to help expand the attack surface of a target.

**My TakeHomes from the Room:**

Subdomain enumeration is the process of finding valid subdomains for a domain so as to expand our attack surface to try and discover more potential points of vulnerability.

Three different subdomain enumeration methods: 
- Brute Force
- OSINT (Open-Source Intelligence)
- Virtual Host.

**OSINT: Secure Sockets Layer/Transport Layer Security Certificates:**

When an SSL/TLS certificate is created for a domain by a CA (Certificate Authority), CA's take part in what's called "Certificate Transparency (CT) logs". These are publicly accessible logs of every SSL/TLS certificate created for a domain name. The purpose of Certificate Transparency logs is to stop malicious and accidentally made certificates from being used. We can use this service to our advantage to discover subdomains belonging to a domain, sites like https://crt.sh offer a searchable database of certificates that shows current and historical results.

**OSINT: Search engines:**

Search engines contain trillions of links to more than a billion websites, which can be an excellent resource for finding new subdomains. Using advanced search methods on websites like Google, such as the **site:** filter, can narrow the search results. For example, **site:*.domain.com -site:www.domain.com** would only contain results leading to the domain name domain.com but exclude any links to www.domain.com; therefore, it shows us only subdomain names belonging to domain.com.

**OSINT: sublist3r:**

To speed up the process of OSINT subdomain discovery, we can automate the above methods with the help of tools like [Sublist3r](https://www.kali.org/tools/sublist3r/)

```
./sublist3r.py -d acmeitsupport.thm
```
<img width="655" height="325" alt="image" src="https://github.com/user-attachments/assets/fd099937-b002-4528-83f7-eb152e0eee89" />


**DNS Bruteforce**

Bruteforce DNS enumeration is the method of trying tens, hundreds, thousands or even millions of different possible subdomains from a pre-defined list of commonly used subdomains. Because this method requires many requests, we automate it with tools to make the process quicker. For instance, we can use a tool called **dnsrecon** to perform this

```
 dnsrecon -t brt -d acmeitsupport.thm
```

<img width="552" height="115" alt="image" src="https://github.com/user-attachments/assets/13d3b5cd-e538-47d3-ae0e-25c641a669ac" />

**Virtual Hosts**

Some subdomains such as development versions of a web application or administration portals aren't always hosted in publically accessible DNS results. Instead, the DNS record could be kept on a private DNS server or recorded on the developer's machines in their /etc/hosts file (or c:\windows\system32\drivers\etc\hosts file for Windows users), which maps domain names to IP addresses. 

Because web servers can host multiple websites from one server when a website is requested from a client, the server knows which website the client wants from the Host header. We can utilize this host header by making changes to it and monitoring the response to see if we've discovered a new website.

Like with DNS Bruteforce, we can automate this process by using a wordlist of commonly used subdomains.

```
ffuf -w /usr/share/wordlists/SecLists/Discovery/DNS/namelist.txt -H "Host: FUZZ.acmeitsupport.thm" -u http://TARGET_IP
```

```
ffuf -w /usr/share/wordlists/SecLists/Discovery/DNS/namelist.txt -H "Host: FUZZ.acmeitsupport.thm" -u http://TARGET_IP -fs {size}
```




