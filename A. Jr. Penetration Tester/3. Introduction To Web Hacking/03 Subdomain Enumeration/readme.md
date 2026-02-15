## Subdomain Enumeration

This THM Room is not Free, Rather it's a Premium one.

In the room, I was able to learn various ways of discovering subdomains to help expand the attack surface of a target.

**My TakeHomes from the Room:**

Subdomain enumeration is the process of finding valid subdomains for a domain so as to expand our attack surface to try and discover more potential points of vulnerability.

Three different subdomain enumeration methods: 
- Brute Force,
- OSINT (Open-Source Intelligence)
- Virtual Host.

**OSINT- Secure Sockets Layer/Transport Layer Security Certificates:**

When an SSL/TLS certificate is created for a domain by a CA (Certificate Authority), CA's take part in what's called "Certificate Transparency (CT) logs". These are publicly accessible logs of every SSL/TLS certificate created for a domain name. The purpose of Certificate Transparency logs is to stop malicious and accidentally made certificates from being used. We can use this service to our advantage to discover subdomains belonging to a domain, sites like https://crt.sh offer a searchable database of certificates that shows current and historical results.
