# Protocols and Servers 1

The Room Introduces common protocols such as HTTP, FTP, POP3, SMTP and IMAP, along with related insecurities

---

## Telnet

An application-layer protocol used to connect to a virtual terminal of another computer. Using Telnet, a user can log into a remote machine and access its terminal (console) to run programs, start batch processes, and perform system administration tasks remotely.

The Telnet protocol is relatively simple. When a user connects, they are asked for a username and password. Upon correct authentication, the user gains access to the remote system's terminal. However, all communication between the Telnet client and the Telnet server is unencrypted, making it an easy target for attackers.

Telnet was widely used for remote administration in the early days of networking. However, it has been almost entirely replaced by SSH (Secure Shell) for interactive remote access. You are unlikely to find Telnet enabled on modern, properly configured systems. However, you may still encounter it in:

- Legacy systems and older network equipment (routers, switches, industrial controllers)
- Embedded devices and IoT equipment with limited resources
- Internal networks where security was never prioritised
- Misconfigured systems where Telnet was enabled but never disabled

During penetration tests, finding an open Telnet port (23) is often a significant finding because it indicates either a legacy system or a security misconfiguration.

**Telnet Client as a Testing Tool**

While Telnet servers are rare, the Telnet client remains useful as a simple tool for connecting to any TCP port and manually interacting with text-based protocols. For example, you can use telnet target 80 to connect to a web server and type HTTP commands manually. 

A Telnet server listens for incoming connections on port 23 using the Telnet protocol. 


Although Telnet provides access to the remote system's terminal quickly, it is not a reliable protocol for remote administration because all data is sent in cleartext. 

---

## Hypertext Transfer Protocol (HTTP)
 The  protocol used to transfer web pages. Your web browser connects to the web server and uses HTTP to request HTML pages, images, and other files. It also submits forms and uploads various files. Any time you browse the World Wide Web (WWW), you are using the HTTP protocol.

HTTP sends and receives data as cleartext (not encrypted). This means anyone with access to the network traffic can read the content being transferred, including sensitive information like login credentials and personal data.

HTTPS (HTTP Secure) wraps HTTP inside TLS encryption. Modern browsers mark plain HTTP sites as "Not Secure", and some features (like geolocation and camera access) are blocked entirely on non-HTTPS sites. 

However, understanding how HTTP works is still essential because:
- The HTTP commands and structure are identical whether using HTTP or HTTPS.
- You will encounter HTTP during internal penetration tests and on legacy systems.
- Understanding the protocol helps you identify and exploit web vulnerabilities.
- Tools like Burp Suite decrypt HTTPS traffic for analysis, showing you raw HTTP.
For this demonstration, plain HTTP is used so you can see exactly what is being transmitted.

Manually Sending HTTP Requests
Because HTTP is a cleartext protocol, you can use a simple tool such as Telnet (or Netcat) to communicate with a web server and act as a "web browser". The key difference is that you need to input the HTTP-related commands instead of the web browser doing that for you.

It is possible to request a page from a web server and discover the web server version. The Telnet client is used because Telnet is a simple protocol that uses cleartext for communication. 

The steps are as follows:

1. Connect to port 80 using ```telnet 10.48.184.32 80.```
2. Type ```GET /index.html HTTP/1.1``` to retrieve the page index.html, or ```GET / HTTP/1.1``` to retrieve the default page.
3. Provide a value for the host header, such as ```host: telnet,``` and press the Enter/Return key twice.

In the console output below, the requested page is recovered along with information not usually displayed by the web browser. If the requested page is not found, the server returns error 404.

<img width="581" height="339" alt="image" src="https://github.com/user-attachments/assets/bd1cef09-fc0b-4f16-b4fe-fd29a5a267be" />

The key input from the user is two lines: ```GET /index.html HTTP/1.1``` followed by ```host: telnet.```

Information Revealed in HTTP Headers

 The Server: nginx/1.18.0 (Ubuntu) header reveals both the web server software and version, as well as the operating system. This information is valuable during reconnaissance because:

- Specific versions may have known vulnerabilities you can research.
- The OS information helps tailor further attacks.
- Even knowing the web server software narrows down potential attack vectors.

Security-conscious administrators often configure their servers to suppress or obscure this information. During a penetration test, finding detailed version information in headers is worth noting.

### Web Servers and Clients

An HTTP server (web server) and an HTTP client (web browser) are required to use the HTTP protocol. The web server "serves" a specific set of files to the requesting web browser.

Popular choices for HTTP servers include:

- **Nginx** has become the most widely used web server on the internet, known for its performance and efficiency in handling concurrent connections. It is free and open-source.
- **Apache** remains extremely popular and powers a large portion of websites. It is highly configurable with a vast ecosystem of modules. It is also free and open-source.
- **Internet Information Services (IIS)** is Microsoft's web server, commonly found in Windows enterprise environments. It requires a Windows Server licence.

Other notable web servers include **LiteSpeed**, **Caddy** (which has automatic HTTPS built in), and **Node.js** for JavaScript-based applications.

The most popular web browsers today are:

- Chrome by Google 
- Safari by Apple 
- Edge by Microsoft 
- Firefox by Mozilla excellent for penetration testing and security research because of its extensive developer tools and add-on ecosystem.

HTTP Protocol Versions
- HTTP/1.1, which has been the workhorse of the web for decades. However, newer versions exist.
- HTTP/2 introduced multiplexing (multiple requests over a single connection), header compression, and server push. It is binary rather than text-based, making it harder to manually interact with using Telnet.
- HTTP/3 uses QUIC (built on UDP) instead of TCP, offering improved performance, especially on unreliable networks. It is increasingly common on major websites.

For learning purposes and manual testing, HTTP/1.1 remains the most accessible because of its human-readable text format.

After connecting using Telnet to ```10.48.184.32 80``` and retrieve the file flag.thm:

<img width="440" height="282" alt="image" src="https://github.com/user-attachments/assets/5a46f668-4288-4515-a37d-8884fda84052" />



