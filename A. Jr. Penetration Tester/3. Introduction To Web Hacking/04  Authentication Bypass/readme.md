# Authentication Bypass

This THM Room is not Free, Rather it's a Premium one.

In the room, I was able to learn how to defeat logins and other authentication mechanisms to allow access to unpermitted areas.

## My TakeHomes from the room

There are different ways in which website authentication methods can be bypassed, defeated or broken. These vulnerabilities can be some of the most critical as it often ends in leaks of customers personal.
- User Enumeration
- Brute Force Using Ffuf
- Taking advantage of logic Flaws
- Cookie Tampering

---

- **Username Enumeration**

Website error messages are great resources for collating this information to build our list of valid usernames when trying to find authentication vulnerabilities.

for instance, trying to sign up with the username admin and then getting the below error message.

<img width="648" height="508" alt="image" src="https://github.com/user-attachments/assets/c3b3a447-8525-430b-95b0-04b04a57a204" />

 The [ffuf tool](https://github.com/ffuf/ffuf) uses a list of commonly used usernames to check against for any matches.

For instance: 
```
ffuf -w /usr/share/wordlists/SecLists/Usernames/Names/names.txt -X POST -d "username=FUZZ&email=x&password=x&cpassword=x" -H "Content-Type: application/x-www-form-urlencoded" -u http://TARGET_IP_ADDRESS/customers/signup -mr "username already exists" | awk '{print $1}' >> valid_usernames.txt

```

- The above command performs the following:
  - **-w** points to the list of usernames that we’re going to check exists
  - **-X** specifies the request method, this will be a GET request by default, but it is a POST request in this example
  - **-d** specifies the data to send
  - **FUZZ** keyword signifies where the contents from our wordlist will be inserted
  - **email=x:** The email field is set to a placeholder value "x". This is to satisfy the form requirements that typically need an email address for account creation.
  - **password=x&cpassword=x:** Both the password and confirm password fields are set to "x". Similar to the email, this is to meet the form's validation rules that require passwords for account creation.
  - **H** for adding additional headers to the request. Here, we’re setting the Content-Type so the web server knows we are sending form data
  - **u** specifies the URL we are making the request to
  - **mr** the text on the page we are looking for to validate we’ve found a valid username
  - **awk** is used to clean the data by only providing the username and throwing out status, size, word, and line information
  - usernames are appended to **valid_usernames.txt file**

   <img width="683" height="539" alt="image" src="https://github.com/user-attachments/assets/cbf97e8b-a452-49ad-99f9-4e5bb1f65f42" />

---

- **Brute Force**

Using the valid_usernames.txt file we generated in the previous task, we can now use this to attempt a brute force attack on the login page (http://10.66.142.45/customers/login).

Command to bruteForce with Ffuf

```
ffuf -w valid_usernames.txt:W1,/usr/share/wordlists/SecLists/Passwords/Common-Credentials/10-million-password-list-top-100.txt:W2 -X POST -d "username=W1&password=W2" -H "Content-Type: application/x-www-form-urlencoded" -u http://10.66.142.45/customers/login -fc 200
```

- The above command performed the following:
  - **-w** Specifies two wordlists; W1 for usernames and W2 for passwords.
  - **-X** Sets the HTTP method to POST.
  - **-d** Sets the POST data, replacing W1 with usernames and W2 with passwords.
  - **-H** Sets the Content-Type header for form data.
  - **-u** Specifies the target URL.
  - **-fc** By filtering out status code 200, we focus on responses indicating a change in status that might suggest successful authentication attempts.

 <img width="682" height="504" alt="image" src="https://github.com/user-attachments/assets/be263964-9f2b-425a-9044-081d473c6aa6" />


---
 
- **Logic Flaw**

Sometimes authentication processes contain logic flaws. A logic flaw is when the typical logical path of an application is either bypassed, circumvented or manipulated by a hacker. 

Logic flaws can exist in any area of a website.

<img width="581" height="247" alt="image" src="https://github.com/user-attachments/assets/5bb969ab-39b4-43ea-9e26-dfc54314bafe" />

 Logic flaw practical example:

**Target:** [http://10.66.142.45/customers/reset]()

**sample valid mail:** [robert@acmeitsupport.thm]()

<img width="646" height="302" alt="image" src="https://github.com/user-attachments/assets/842b96e2-4ce5-4eb6-9b07-ba86b55e1049" />


<img width="647" height="199" alt="image" src="https://github.com/user-attachments/assets/a23df7be-8203-45d3-aa94-7a72432f75f0" />

- Inspecting the page while reseting the password, We note:

  - the email address is sent in the query string request as a GET field.
 
<img width="658" height="433" alt="image" src="https://github.com/user-attachments/assets/fdf184e6-4688-4fed-87e4-ae4dda03e404" />


  - username ‘robert’ was submitted as a POST field and a confirmation message popped up that a password reset email will be sent.

  <img width="646" height="444" alt="image" src="https://github.com/user-attachments/assets/239a9316-076e-42ca-a89d-dfc16f6fc0ad" />
  
  - The password reset logic is: You have to know both the email and username and then the password link is sent to the email address of the account owner.

The system uses the provided email address to trigger the password reset workflow. Knowing this, the logic flaw can be exploited by manipulating the email in the POST data to redirect the reset email. This suggests the application trusts user input from both GET and POST parameters, leading to potential security vulnerabilities.

We thus can use **Curl requests** to simulate the form submission and illustrate the reset password function.

** Curl Requests 1**

```
curl 'http://10.66.142.45/customers/reset?email=robert%40acmeitsupport.thm' -H 'Content-Type: application/x-www-form-urlencoded' -d 'username=robert
```

- In the Curl command:
  - We use the -H flag to add an additional header to the request.
  - We are setting the Content-Type to application/x-www-form-urlencoded, which lets the web server know we are sending form data so it properly understands our request.
    
In the application, the user account is retrieved using the query string, but later on, in the application logic, the password reset email is sent using the data found in the PHP variable $_REQUEST: an array that contains data received from the query string and POST data and used to access both GET and POST data, and that POST data is prioritized over GET data, a characteristic that attackers can exploit.

If the same key name is used for both the query string and POST data, the application logic for this variable favours POST data fields rather than the query string, so if we add another parameter to the POST form, we can control where the password reset email gets delivered.

By doing this, we can manipulate POST data to override the email address from the query string, thus redirecting the password reset email to address of our choice. 


For Next step, we thus create an account on the Acme IT support customer section, doing so gives you a unique email address that can be used to create support tickets. The email address is in the format of [{username}@customer.acmeitsupport.thm]()

I used: 

```
zazazaza@customer.acmeitsupport.thm
```

Now rerunning Curl Request 2 but with [zazazaza@customer.acmeitsupport.thm]() in the email field I get a ticket created on my account that I have created which contains a link to log me in as Robert. 

Using Robert's account, I can then view their support tickets and reveal a flag.



** Curl Requests 2**

```
curl 'http://10.66.142.45/customers/reset?email=robert@acmeitsupport.thm' -H 'Content-Type: application/x-www-form-urlencoded' -d 'username=robert&email=zazazaza@customer.acmeitsupport.thm'
```



 ---
 
- **Cookie Tampering**

Examining and editing the cookies set by the web server during an online session can have multiple outcomes, such as:
- unauthenticated access,
- access to another user's account, or
- elevated privileges.

Cookies can either be in:
- Plain Text: Human reddable
- Hashed : Hashes are irrevasibble but can be cracked using the [Crackstation tool](https://crackstation.net/)
- encoded:


***Plain Text**

```
user@tryhackme$ curl http://10.66.142.45/cookie-test

curl -H "Cookie: logged_in=true; admin=false" http://10.66.142.45/cookie-test

curl -H "Cookie: logged_in=true; admin=true" http://10.66.142.45/cookie-test

```



<img width="677" height="127" alt="image" src="https://github.com/user-attachments/assets/324ed8b6-1331-49bf-bfb8-daa2fa12fc6f" />
