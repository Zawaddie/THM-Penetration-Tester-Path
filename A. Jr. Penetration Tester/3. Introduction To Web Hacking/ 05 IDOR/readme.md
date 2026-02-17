# IDOR : Insecure Indirect Object Reference

This THM Room is not Free, Rather it's a Premium one.

In the room, I was able to learn how to find IDOR vulnerabilities in a Web application so as to gain access to data that I shouldn't have.

## My key TakeHomes from the Room:

- IDOR (Insecure Direct Object Reference)  is a type of access control vulnerability.

- It can occur when a web server receives user-supplied input to retrieve objects (files, data, documents), too much trust has been placed on the input data, and it is not validated on the server-side to confirm the requested object belongs to the user requesting it.


---

**IDOR Example**

Imagine you've just signed up for an online service, and you want to change your profile information. 

URL for the page revealing your information:

[http://online-service.thm/profile?user_id=1305]() 

Changing the user_id value to 1000, you can now see another user's information

[http://online-service.thm/profile?user_id=1000]() 


---

**Finding IDORs in Encoded IDs**

When passing data from page to page either by post data, query strings, or cookies, web developers will often first take the raw data and encode it. 

Encoding ensures that the receiving web server will be able to understand the contents. It changes binary data into an ASCII string commonly using the a-z, A-Z, 0-9 and = character for padding. The most common encoding technique on the web is base64 encoding 


<img width="1054" height="80" alt="image" src="https://github.com/user-attachments/assets/335cb96f-a36e-4afd-a34e-b184770d0614" />


---

**Finding IDORs in Hashed IDs**

Hashed IDs are a little bit more complicated to deal with than encoded ones, but they may follow a predictable pattern, such as being the hashed version of the integer value. For example, the Id number 123 would become 202cb962ac59075b964b07152d234b70 if md5 hashing were in use.

---

**Finding IDORs inUnpredictable IDs**

If the Id cannot be detected using the above methods, an excellent method of IDOR detection is to create two accounts and swap the Id numbers between them. If you can view the other users' content using their Id number while still being logged in with a different account (or not logged in at all), you've found a valid IDOR vulnerability.

---

To Note:

The vulnerable endpoint you're targeting may not always be something you see in the address bar. It could be the content your browser loads in via an AJAX request or something that you find referenced in a JavaScript file. 

Sometimes endpoints could have an unreferenced parameter that may have been of some use during development and got pushed to production. 

For example, you may notice a call to **/user/details** displaying your user information (authenticated through your session). But through an attack known as parameter mining, you discover a parameter called user_id that you can use to display other users' information, for example, /user/details?user_id=123.

---

I create an account and log in.

Inspecteing the page, I notice I'm given user Id 50

<img width="1360" height="673" alt="image" src="https://github.com/user-attachments/assets/2b68a331-5f24-4fdd-95f8-49a0fa950078" />

Changing the user ID to 1 or 3, am able to view other users information.

<img width="804" height="130" alt="image" src="https://github.com/user-attachments/assets/f7b0785f-5378-4f15-83f4-2a1728e6dc15" />

<img width="785" height="105" alt="image" src="https://github.com/user-attachments/assets/0ed071de-6209-4f65-ad3c-aeb0e44f28b3" />


**Found this useful**

<img width="635" height="529" alt="image" src="https://github.com/user-attachments/assets/1e4b0017-ac04-412b-8403-02111e7ddf6e" />
