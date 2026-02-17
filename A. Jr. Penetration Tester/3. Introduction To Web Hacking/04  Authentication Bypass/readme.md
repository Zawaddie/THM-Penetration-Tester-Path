# Authentication Bypass

This THM Room is not Free, Rather it's a Premium one.

In the room, I was able to learn how to defeat logins and other authentication mechanisms to allow access to unpermitted areas.

## My TakeHomes from the room

There are different ways in which website authentication methods can be bypassed, defeated or broken. These vulnerabilities can be some of the most critical as it often ends in leaks of customers personal.

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

- **Brute Force**

  
- **Logic Flow**

  
- **Cookie Tampering**
