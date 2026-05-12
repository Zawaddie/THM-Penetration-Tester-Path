# Command Injection

In the room, I was able to learn about a vulnerability that allowed me to execute commands through a vulnerable app. 
I also learnt about its remediations.

---
## What it is

**Command injection** is the abuse of an application's behaviour to execute commands on the operating system, using the same privileges that the application on a device is running with. For example, achieving command injection on a web server running as a user named joe will execute commands under this joe user - and therefore obtain any permissions that joe has.

Also often known as **“Remote Code Execution” (RCE)** because of the ability to remotely execute code within an application. These vulnerabilities are often the most lucrative to an attacker because it means that the attacker can directly interact with the vulnerable system. For example, an attacker may read system or user files, data, and things of that nature.

For example, being able to abuse an application to perform the command whoami to list what user account the application is running will be an example of command injection.

Command injection was one of the top ten vulnerabilities reported by Contrast Security’s AppSec intelligence report in 2019. [Contrast Security AppSec., 2019](https://www.contrastsecurity.com/security-influencers/insights-appsec-intelligence-report). Moreover, the OWASP framework constantly proposes vulnerabilities of this nature as one of the top ten vulnerabilities of a web application [OWASP framework](https://owasp.org/www-project-top-ten/).

---

## Discovering command injention

This vulnerability exists because applications often use functions in programming languages such as PHP, Python and NodeJS to pass data to and to make system calls on the machine’s operating system. For example, taking input from a field and searching for an entry into a file.

In this code snippet, the application takes data that a user enters in an input field named $title to search a directory for a song title. 
<img width="1271" height="430" alt="image" src="https://github.com/user-attachments/assets/643c33be-bcee-4037-ae45-4d88a004b022" />

1. The application stores MP3 files in a directory contained on the operating system.

2. The user inputs the song title they wish to search for. The application stores this input into the $title variable.

3. The data within this $title variable is passed to the command grep to search a text file named songtitle.txt for the entry of whatever the user wishes to search for.

4. The output of this search of songtitle.txt will determine whether the application informs the user that the song exists or not.

Now, this sort of information would typically be stored in a database; however, this is just an example of where an application takes input from a user to interact with the application’s operating system.

An attacker could abuse this application by injecting their own commands for the application to execute. Rather than using grep to search for an entry in songtitle.txt, they could ask the application to read data from a more sensitive file.

Abusing applications in this way can be possible no matter the programming language the application uses. As long as the application processes and executes it, it can result in command injection. 

For example, this code snippet below is an application written in Python.

<img width="823" height="282" alt="image" src="https://github.com/user-attachments/assets/c890254b-af60-4d78-a25f-4e98b7a4006b" />

Steps of how this Python application works as well.

1. The "flask" package is used to set up a web server
2. A function that uses the "subprocess" package to execute a command on the device
3. We use a route in the webserver that will execute whatever is provided. For example, to execute whoami, we'd need to visit http://flaskapp.thm/whoami

---












