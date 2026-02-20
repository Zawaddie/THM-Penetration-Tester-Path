# File Inclusion

This THM Room is not Free, Rather it's a Premium one.

This room introduced me to File Inclusion Vulnerabilities, including Local File Inclusion (LFI), Remote File Inclusion (RFI), and directory traversal.

## My Key TakeHomes From The Room

- Essential knowledge to exploit file inclusion vulnerabilities, including
  - Local File Inclusion (LFI),
  - Remote File Inclusion (RFI), and
  - Directory traversal.

- Understanding of the risk of these vulnerabilities if they're found and the required remediation.
- In some scenarios, web applications are written to request access to files on a given system via parameters.
- Parameters are query strings attached to the URL that could be used to retrieve data or perform actions based on user input

<img width="717" height="342" alt="image" src="https://github.com/user-attachments/assets/de145a95-dab4-40e3-9160-9dd6f7c80ef9" />

<img width="1059" height="501" alt="image" src="https://github.com/user-attachments/assets/edc30620-3b89-4b09-909f-b36a4e887a0f" />


- File inclusion vulnerabilities are commonly found and exploited in various programming languages for web applicationsthat are poorly written and implemented.
  
- Input validation, in which the user inputs are not sanitized or validated, and the user controls them can allow the user can pass any input to the function, causing the vulnerability.
 
- By default, attackers leverage file inclusion vulnerabilities to leak data, such as code, credentials or other important files related to the webApp or OS.
-  If the attacker can write files to the server by any other means, file inclusion might be used to gain remote command execution (RCE).


---

- **Path Transversal/Directory Transversal**

A web security vulnerability allows an attacker to read OS resources like local files on the server running an application. 

Attackers exploits this vulnerability by manipulating and abusing the web application's URL to locate and access files or directories stored outside the application's root directory.

Path traversal vulnerabilities occur when the user's input is passed to a function such as **file_get_contents**  in PHP (used to read the contents of a file) attributed to poor input validation or filtering. 

A web application stores files in **/var/www/app** and here is the path of a user requesting the contents of userCV.pdf from a defined path **/var/www/app/CVs**'

<img width="1343" height="560" alt="image" src="https://github.com/user-attachments/assets/8cf9786d-f46e-47a2-95a6-57ecdadffa5c" />

**dot-dot-slash attack**
A Path traversal attacks that take advantage of moving the directory one step up using the double dots ../.

By this, the attacker can test out the URL parameter by adding payloads to see how the web application behaves.  

For instance, if the attacker finds the entry point, which in this case **get.php?file=,** then the he may send something like, [http://webapp.thm/get.php?file=../../../../etc/passwd]()

For this case, if there isn't input validation, instead of accessing the PDF files at /var/www/app/CVs location, the web application retrieves files from other directories and presents the /etc/passwd to the attacker. Each .. entry moves one directory until it reaches the root directory /. Then it changes the directory to /etc, and from there, it read the passwd file.


<img width="713" height="598" alt="image" src="https://github.com/user-attachments/assets/98668fd9-a6ad-468b-99bc-2af0640a794e" />

<img width="1255" height="600" alt="image" src="https://github.com/user-attachments/assets/5a388a9b-8f7b-4b1d-aae4-624281260f8d" />



Similarly, if the web application runs on a Windows server, the same concept applies as with Linux OS, where we climb up directories until it reaches the root directory, as we provide Windows paths. 

For example, to read the **boot.ini** file located in **c:\boot.ini,** then the attacker can try the following:

http://webapp.thm/get.php?file=../../../../boot.ini or

http://webapp.thm/get.php?file=../../../../windows/win.ini


As a remediation, developers will add filters to limit access to only certain files or directories. 

Some common OS files a penetration tester can use when testing:.

 
---

- **Local File Inclusion (LFI)**
  
LFI attacks against web applications are often due to a developers' lack of security awareness. 

With PHP, using functions such as *include, require, include_once,* and *require_once* often contribute to vulnerable web applications.

LFI vulnerabilities not only occur on PHP, they also occur when using other languages such as ASP, JSP, or even in Node.js but  the LFI  exploits follow the same concepts as path traversal.

**various LFI scenarios and how to exploit them.**:

#1. Suppose the web application provides two languages, and the user can select between the EN and AR

<?PHP 
	include($_GET["lang"]);
?>
 
The PHP code above uses a GET request via the URL parameter lang to include the file of the page. The call can be done by sending the following HTTP request as follows: http://webapp.thm/index.php?lang=EN.php to load the English page or http://webapp.thm/index.php?lang=AR.php to load the Arabic page, where EN.php and AR.php files exist in the same directory.

Theoretically, we can access and display any readable file on the server from the code above if there isn't any input validation. Let's say we want to read the /etc/passwd file, which contains sensitive information about the users of the Linux operating system, we can try the following: http://webapp.thm/get.php?file=/etc/passwd

In this case, it works because there isn't a directory specified in the include function and no input validation.

Now apply what we discussed and try to read /etc/passwd file. Also, answer question #1 below.

#2. Next, In the following code, the developer decided to specify the directory inside the function.

 

<?PHP 
	include("languages/". $_GET['lang']); 
?>
In the above code, the developer decided to use the include function to call PHP pages in the languages directory only via lang parameters.

If there is no input validation, the attacker can manipulate the URL by replacing the lang input with other OS-sensitive files such as /etc/passwd.

Again the payload looks similar to the path traversal, but the include function allows us to include any called files into the current page. The following will be the exploit:

http://webapp.thm/index.php?lang=../../../../etc/passwd

Now apply what we discussed, try to read files within the server, and figure out the directory specified in the include function and answer question #2 below.



---


