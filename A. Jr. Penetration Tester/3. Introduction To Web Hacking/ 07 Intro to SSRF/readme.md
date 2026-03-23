# SSRF : Server Side Request Forgery

<img width="1257" height="79" alt="image" src="https://github.com/user-attachments/assets/efa7f474-3bde-4862-b451-a75a9bdee18b" />


In this Room, I was able to learn what an SSRF is, what kind of impact they can have, how you can discover SSRF vulnerabilities, how to circumvent input rules and to Exploit Server-side Request Forgery(SSRF) vulnerabilities to allow access to internal server resources.

---

## What is an SSRF?

SSRF (Server-Side Request Forgery) is a vulnerability that allows a malicious user to cause the webserver to make an additional or edited HTTP request to the resource of the attacker's choosing.


**Types of SSRF vulnerability:**

- Regular SSRF where data is returned to the attacker's screen.
- Blind SSRF vulnerability where an SSRF occurs, but no information is returned to the attacker's screen.

**What's the impact?**<br>
A successful SSRF attack can result in any of the following: 

- Access to unauthorised areas.
- Access to customer/organisational data.
- Ability to Scale to internal networks.
- Reveal authentication tokens/credentials.

---
## SSRF Examples:

1. The attacker can have complete control over the page requested by the webserver.
The Expected Request is what the ```website.thm``` server is expecting to receive, with the section in red being the URL that the website will fetch for the information.<br>
The attacker can modify the area in red to an URL of their choice.

<img width="904" height="410" alt="image" src="https://github.com/user-attachments/assets/6759d1ce-44fb-413f-8dcc-c109dea76e25" />

3. The attacker can still reach the ```/api/user``` page with only having control over the path by utilising directory traversal. When ```website.thm``` receives ```../``` this is a message to move up a directory which removes the ```/stock``` portion of the request and turns the final request into ```/api/user```.

<img width="795" height="388" alt="image" src="https://github.com/user-attachments/assets/e0558a3b-1df0-40d0-935c-88ee9ce67c9e" />

3. The attacker can control the server's subdomain to which the request is made. <br> Take note of the payload ending in ``&x=`` being used to stop the remaining path from being appended to the end of the attacker's URL and instead turns it into a parameter ```(?x=)```on the query string.

<img width="652" height="394" alt="image" src="https://github.com/user-attachments/assets/c717db1a-0dff-4114-b287-b3fde4d85d70" />


4. Apart from the original request, the attacker can instead force the webserver to request a server of the attacker's choice. By doing so, he can capture request headers that are sent to the attacker's specified domain. These headers could contain authentication credentials or API keys sent by ```website.thm``` (that would normally authenticate to ```api.website.thm```)

<img width="645" height="394" alt="image" src="https://github.com/user-attachments/assets/bb903c9d-102f-49a1-8098-ef085bc17269" />

If the server is requesting ```https://website.thm/item/2?server=api```, we can change the request to ```https://website.thm/item/2?server=server.website.thm/flag?id=9&x=``` in order to force the webserver to return data from https://server.website.thm/flag?id=9.

<img width="649" height="337" alt="image" src="https://github.com/user-attachments/assets/569654e4-900a-477f-89fa-5ea20c70bcdb" />

---

## Finding SSRF

Potential SSRF vulnerabilities can be spotted in web applications in many different ways. 

For instance, there are four common places to look:

Some of these are easier to exploit than others, thus a lot of trial and error will be required to find a working payload.

1. When a full URL is used in a parameter in the address bar:

<img width="676" height="56" alt="image" src="https://github.com/user-attachments/assets/a0174ad8-b7ca-49aa-9fff-2a843c7d64f8" />

2. A hidden field in a form:

<img width="553" height="133" alt="image" src="https://github.com/user-attachments/assets/6b2872a6-e1f2-4f3f-b671-f9824ebdec44" />

3. A partial URL such as just the hostname:

<img width="414" height="55" alt="image" src="https://github.com/user-attachments/assets/21cf6cc0-b7c4-4fd8-8f79-e71f5231d05d" />

4. Or perhaps only the path of the URL:
<img width="559" height="64" alt="image" src="https://github.com/user-attachments/assets/51ac6cf0-5fd4-409d-b1a5-923f8fabaaf2" />



When working with a blind SSRF where no output is reflected back, we need to use an external HTTP logging tool to monitor requests such as ```requestbin.com```, our own HTTP server or Burp Suite's Collaborator client.

---
## Defeating Common SSRF defenses

More security-savvy developers aware of the risks of SSRF vulnerabilities may implement checks in their applications to make sure the requested resource meets specific rules. 

There are usually two approaches to this, either **a deny list** or **an allow list**.

1. **Deny List**

Where all requests are accepted apart from resources specified in a list or matching a particular pattern. 

A Web Application may employ a deny list to protect sensitive endpoints, IP addresses or domains from being accessed by the public while still allowing access to other locations. 

A specific endpoint to restrict access is the localhost, which may contain server performance data or further sensitive information, so domain names such as ```localhost``` and ```127.0.0.1``` would appear on a deny list. 

Note that Attackers can bypass a Deny List by **using alternative localhost references such as ```0```, ```0.0.0.0```, ```0000```, ```127.1```, ```127.*.*.*```, ```2130706433```, ```017700000001``` or subdomains that have a DNS record which resolves to the IP Address ```127.0.0.1``` such as ```127.0.0.1.nip.io```.***



Also, in a cloud environment, it would be beneficial to block access to the IP address ```169.254.169.254```, which contains metadata for the deployed cloud server, including possibly sensitive information. 

An attacker can bypass this by registering a subdomain on their own domain with a DNS record that points to the IP Address ```169.254.169.254```.



2. **Allow List**

Where all requests get denied unless they appear on a list or match a particular pattern, such as a rule that an URL used in a parameter must begin with https://website.thm. 

An attacker could quickly circumvent this rule by creating a subdomain on an attacker's domain name, such as https://website.thm.attackers-domain.thm. The application logic would now allow this input and let an attacker control the internal HTTP request.


### Open Redirect

The above bypasses may not work. If so, **Open Redirect** is the trick can use.

An **open redirect** is an endpoint on the server where the website visitor gets automatically redirected to another website address. Take, for example, the link https://website.thm/link?url=https://tryhackme.com. This endpoint was created to record the number of times visitors have clicked on this link for advertising/marketing purposes. But imagine there was a potential SSRF vulnerability with stringent rules which only allowed URLs beginning with https://website.thm/. An attacker could utilise the above feature to redirect the internal HTTP request to a domain of the attacker's choice.

Questions to answer:
1. What method can be used to bypass strict rules? **Open Redirect** 

2. What IP address may contain sensitive data in a cloud environment? **169.254.169.254**

3. What type of list is used to permit only certain input? **Allow List**

4. What type of list is used to stop certain input? **Deny List**

---

## SSRF Practical

**Fictional Scenario:**

We've come across two new endpoints during a content discovery exercise against the Acme IT Support website: 
- The first one is ```/private```, which gives us an error message explaining that the contents cannot be viewed from our IP address. 
- The second is a new version of the customer account page at ```/customers/new-account-page``` with a new feature allowing customers to choose an avatar for their account.


STEPS to obtain the Flag:
1. We bBegin by clicking the Start Machine button to launch the Acme IT Support website.
2. Once running, we visit it at the URL https://LAB_WEB_URL.p.thmlabs.com.
3. Then, we create a customer account and sign in.

<img width="1065" height="399" alt="image" src="https://github.com/user-attachments/assets/2e9ca206-be8f-4f71-90d2-8398db6db059" />

5. Once signed in, we visit https://LAB_WEB_URL.p.thmlabs.com/customers/new-account-page to view the new avatar selection feature.

<img width="763" height="583" alt="image" src="https://github.com/user-attachments/assets/fb3544b2-f22f-4496-88c4-a0a3dadb6bd1" />

7. By viewing the page source of the avatar form, we see the avatar form field value contains the path to the image. 

<img width="923" height="568" alt="image" src="https://github.com/user-attachments/assets/9a343e2d-12f5-49fb-9c90-002d4f5bc31f" />

8. We can then try making the request again but changing the avatar value to private in hopes that the server will access the resource and get past the IP address block by first by right-clicking on one of the radio buttons on the avatar form and selecting Inspect:

<img width="1365" height="639" alt="image" src="https://github.com/user-attachments/assets/a18d9aa3-5bf2-4af4-a1ca-96c01db38e79" />

9. We then edit the value of the radio button to 'private'

<img width="560" height="58" alt="image" src="https://github.com/user-attachments/assets/cdec879b-805c-47ee-bb7c-2238a3e0635e" />

10. When we select the avatar we edited and then click the Update Avatar button.
    Unfortunately, it looks like the web application has a deny list in place and has blocked access to the ```/private``` endpoint.

<img width="1358" height="600" alt="image" src="https://github.com/user-attachments/assets/983befff-409d-4e83-b337-f334e52fbea8" />

As we can see from the error message, the path cannot start with ```/private```. 

11. We can use a directory traversal trick to bypass this rule and reach our desired endpoint. We do this by setting the avatar value to ```x/../private```.

<img width="1365" height="587" alt="image" src="https://github.com/user-attachments/assets/7b065e65-3a1b-42c6-9c2f-42cec5860ff5" />

Following this trick, we note that have now bypassed the rule, and the user updated the avatar.

<img width="1365" height="680" alt="image" src="https://github.com/user-attachments/assets/11bc7984-63f9-45a9-9089-e1fb2d0dd783" />



This trick works because when the web server receives the request for ```x/../private```, it knows that the ```../``` string means to move up a directory that now translates the request to just ```/private```.

<img width="1082" height="571" alt="image" src="https://github.com/user-attachments/assets/e6e4166e-1713-4981-8a6f-b7cd026f86cc" />


Finally, Viewing the page source of the avatar form, we see the currently set avatar now contains the contents from the ```/private``` directory in base64 encoding, decode this content and it will reveal the flag for this task.

<img width="1365" height="608" alt="image" src="https://github.com/user-attachments/assets/35c7a68c-04d5-46b4-a088-e3cb9876433b" />





