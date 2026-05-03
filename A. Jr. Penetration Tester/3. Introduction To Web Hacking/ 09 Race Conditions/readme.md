# Race Conditions

The room taught me about race conditions and how they affect web application security.

---

When testing the security of an online shopping web application. Many questions pop up. Can we reuse a single $10 gift card to pay for a $100 item? Can we apply the same discount to our shopping cart multiple times? The answer is maybe! If the system is susceptible to a race condition vulnerability, we can do all this and more.

**A race condition** is a situation in computer programs where the timing of events influences the behaviour and outcome of the program. It typically happens when a variable gets accessed and modified by multiple threads. Due to a lack of proper lock mechanisms and synchronization between the different threads, an attacker might abuse the system and apply a discount multiple times or make money transactions beyond their balance.

---
## Overview

**A program** is a set of instructions to achieve a specific task. You need to execute the program to accomplish what you want. Unless you execute it, it won’t do anything and remains a set of static instructions.

**A process/job** is a program in execution. In some literature, you might come across the term job. Both terms refer to the same thing, although the term process has superseded the term job. Unlike a program, which is static, a process is a dynamic entity. 

key aspects of a process:
- **Program**: The executable code related to the process
- **Memory**: Temporary data storage
- **State**: A process usually hops between different states. After it is in the New state, i.e., just created, it moves to the Ready state, i.e., ready to run once given CPU time. Once the CPU allocates time for it, it goes to the Running state. Furthermore, it can be in the Waiting state pending I/O or event completion. Once it exits, it moves to the Terminated state.
State diagram showing the 5 states of a process: New, Ready, Running, Waiting, and Terminated

<img width="879" height="417" alt="image" src="https://github.com/user-attachments/assets/6b9f68cf-66b4-41a6-a080-05c46c8233ae" /> 


**A thread** is a lightweight unit of execution. It shares various memory parts and instructions with the process.

In many cases, we need to replicate the same process repeatedly. Think of a web server serving thousands of users the same page (or a personalized page). We can adopt one of two main approaches:

- **Serial**: One process is running; it serves one user after the other sequentially. New users are enqueued.
- **Parallel**: One process is running; it creates a thread to serve every new user. New users are only enqueued after the maximum number of running threads is reached.

---

## Race Conditions

Considering the following Python code with two threads simulating a task completion by 10% increments

```Python

import threading

x = 0  # Shared variable

def increase_by_10():
    global x
    for i in range(1, 11):
        x += 1
        print(f"Thread {threading.current_thread().name}: {i}0% complete, x = {x}")

# Create two threads
thread1 = threading.Thread(target=increase_by_10, name="Thread-1")
thread2 = threading.Thread(target=increase_by_10, name="Thread-2")

# Start the threads
thread1.start()
thread2.start()

# Wait for both threads to finish
thread1.join()
thread2.join()

print("Both threads have finished completely.")

```

These two threads start together; they do nothing except print a value on the screen. Consequently, one would expect them to finish simultaneously, or at least the result to be consistent. However, in the program above, there is no guarantee which thread will finish first and how early it will be. 

First execution output:

```python

python t3_race_to_100.py
...
Thread Thread-1: 40% complete, x = 10
Thread Thread-2: 70% complete, x = 11
Thread Thread-1: 50% complete, x = 12
Thread Thread-2: 80% complete, x = 13
Thread Thread-1: 60% complete, x = 14
Thread Thread-1: 70% complete, x = 16
Thread Thread-2: 90% complete, x = 15
Thread Thread-2: 100% complete, x = 17
Thread Thread-1: 80% complete, x = 18
Thread Thread-1: 90% complete, x = 19
Thread Thread-1: 100% complete, x = 20
Both threads have finished completely.

```

Second execution output:

```python

python t3_race_to_100.py 
...
Thread Thread-1: 70% complete, x = 10
Thread Thread-2: 40% complete, x = 11
Thread Thread-1: 80% complete, x = 12
Thread Thread-2: 50% complete, x = 13
Thread Thread-1: 90% complete, x = 14
Thread Thread-2: 60% complete, x = 15
Thread Thread-1: 100% complete, x = 16
Thread Thread-2: 70% complete, x = 17
Thread Thread-2: 80% complete, x = 18
Thread Thread-2: 90% complete, x = 19
Thread Thread-2: 100% complete, x = 20
Both threads have finished completely.

```
Running this program multiple times will lead to different results. In the first attempt, Thread-2 reached 100 first; however, in the second attempt, Thread-2 reached 100 second. We have no control over the output. If the security of our application relies on one thread finishing before the other, then we need to set mechanisms in place to ensure proper protection. 

Consider the following two examples to better understand the bugs’ gravity when we leave things to chance.

**Causes**

As we saw in the last program, two threads were changing the same variable. Whenever the thread was given CPU time, it rushed to increase x by 1. Consequently, these two threads were “racing” to increment the same variable. This program shows a straightforward example happening on a single host.

Generally speaking, **a common cause of race conditions lies in shared resources.** For example, when multiple threads concurrently access and modify the same shared data. Examples of shared data are a database record and an in-memory data structure. 

There are many subtle causes, but we will mention three common ones:

1. **Parallel Execution:** Web servers may execute multiple requests in parallel to handle concurrent user interactions. If these requests access and modify shared resources or application states without proper synchronization, it can lead to race conditions and unexpected behaviour.
2. **Database Operations:** Concurrent database operations, such as read-modify-write sequences, can introduce race conditions. For example, two users attempting to update the same record simultaneously may result in inconsistent data or conflicts. The solution lies in enforcing proper locking mechanisms and transaction isolation.
3. **Third-Party Libraries and Services:** Nowadays, web applications often integrate with third-party libraries, APIs, and other services. If these external components are not designed to handle concurrent access properly, race conditions may occur when multiple requests or operations interact with them simultaneously.

---

## Web Applications Architecture

The web application architecturehelps in understanding how race conditions are possible.

### **Client-Server Model**

Web applications follow a client-server model:

**Client:** The program or application that initiates the request for a service.

**Server** The program or system that provides these services in response to incoming requests. 

Generally speaking, the client-server model runs over a network. The client sends its request over the network, and the server receives it and processes it before sending back the required resource.

### Typical Web Application

A web application follows a multi-tier architecture. Such architecture separates the application logic into different layers or tiers. The most common design uses three tiers:

1. **Presentation tier:** In web applications, this tier consists of the web browser on the client side. The web browser renders the HTML, CSS, and JavaScript code.
2. **Application tier:** This tier contains the web application’s business logic and functionality. It receives client requests, processes them, and interacts with the data tier. It is implemented using server-side programming languages such as Node.js and PHP, among many others.
3. **Data tier:** This tier is responsible for storing and manipulating the application data. Typical database operations include creating, updating, deleting, and searching existing records. It is usually achieved using a database management system (DBMS); examples of DBMS include MySQL and PostgreSQL.

<img width="902" height="560" alt="image" src="https://github.com/user-attachments/assets/ffac5d37-589d-4fb8-8dd2-5c458c5314a9" />



### **States**

Some examples from business logic before diving deeper:

1. Validating and conducting money transfer
2. Validating coupon codes and applying discounts

**Validating and Conducting Money Transfer**

Consider the example of transferring money to a friend or your other account. The program will progress as:

1. The user clicks on the “Confirm Transfer” button
2. The application queries the database to confirm that the account balance can cover the transfer amount
3. The database responds to the query
   - If the amount is within the account limits, the application conducts the transaction
   - If the amount is beyond the account limits, the application shows an error message


<img width="484" height="564" alt="image" src="https://github.com/user-attachments/assets/2e07efd6-68ff-4880-a3ad-c2a2a1965585" />


In an ideal scenario, the code above leads to two program states:
1. Amount not sent
2. Amount sent

<img width="904" height="320" alt="image" src="https://github.com/user-attachments/assets/e3d825b4-fda0-4180-a61d-e3dd6bab0eb9" />


**Validating coupon codes and applying discounts**

considering the example of applying a discount coupon. The user goes to their shopping cart and adds a coupon to get a discount. The steps might be as:

1. The user enters a coupon code
2. The application queries the database to determine whether the coupon code is valid and whether any constraints exist
3. The database responds with validity and constraints
   - The discount is applied if the code is valid and there are no constraints on applying it for this user.
   - An error message is displayed if the code is invalid or there are constraints on applying it for this user.

<img width="456" height="555" alt="image" src="https://github.com/user-attachments/assets/30678a78-8889-4631-b0e3-5f60782e6b2f" />

The above code leads to a few program states:
1. Coupon not applied
2. Coupon applied

<img width="1200" height="358" alt="image" src="https://github.com/user-attachments/assets/7e9b5804-2967-4beb-bd3e-0a701f4bb3cc" />


**Two States? Think Again**

Let’s continue our analysis of applying a discount coupon. Ideally, we expect two states: Coupon not applied and Coupon applied. However, this is too simplistic to depict real sophisticated scenarios. We can add an intermediary state: Checking coupon applicability.


<img width="1292" height="326" alt="image" src="https://github.com/user-attachments/assets/43ffbf27-7425-42b2-a446-9871be6876ce" />

Depending on how the application is developed, we can expect more states. For example, **Checking coupon applicability** might involve two states: **Checking coupon validity** and **Checking coupon constraints**. A coupon might be valid, but existing constraints prevent it from being applied. Similarly, Coupon applied might be divided into two states, one of which is **Recalculating total**.

<img width="963" height="527" alt="image" src="https://github.com/user-attachments/assets/79b372a5-de91-4c55-a20c-01af4f7dae76" />


**Why is this important for race conditions?**

In the state diagram above, we can see that we pass through multiple states before the coupon is marked as applied. Let’s draw the states on a time axis, as shown below.

<img width="1287" height="336" alt="image" src="https://github.com/user-attachments/assets/067ec7fb-449c-4bd0-b9db-fc31f0385624" />
Timeline showing the different states that the application goes through as it applies a discount coupon.

There is a time window between the instant we try to add a coupon and the instant where the coupon is marked as applied and cannot be applied again. As long as the coupon is not marked as applied, most likely, no controls prevent it from being accepted repeatedly. We might be able to apply it multiple times during this time window.

This situation is similar when considering the states for the program making a money transfer. Although ideally speaking, it would be two states, considering the business logic, we can easily update the diagram to include three states. The reason is that we expect some time spent checking the account balance and limits; although this amount of time might be brief, it is not zero. If we dig deeper, we can uncover more “hidden” states.

<img width="1229" height="332" alt="image" src="https://github.com/user-attachments/assets/a6a1c6e2-1937-4a19-ae47-a016dafda2e5" />
A state diagram for program responsible for money transfers; it shows three states.

However, even if the web application is vulnerable, we still have one challenge to overcome: timing. Even in vulnerable applications, this “window of opportunity” is relatively short; therefore, exploiting it necessitates that our requests reach the server simultaneously. In practice, we aim to get our repeated requests to reach the server only milliseconds apart.

How can we get our duplicated requests to reach the server within this short window? We need a tool such as Burp Suite.

---

## Exploiting Race Conditions

TARGET: http://MACHINE_IP:8080. A web application belonging to a mobile operator and allows phone credit transfer.

These are the credentials for two users:
1. User1: 07799991337  Password: pass1234
2. User2: 07113371111  Password: pass1234

**In this demo, we will check if the system is susceptible to a race condition vulnerability and try to exploit it by transferring more credit than we have in our account.**

First, we need to explore and study how the target web application receives HTTP requests and how it responds to them. 
1. Using Burp Suite Proxy, we click Open browser under the Intercept tab. (If you get an error message about enabling the browser’s sandbox, you must manually change the settings. In this case, click Settings on the top right of your Burp Suite window, click on the Burp’s browser under Tools, and check Allow Burp’s browser to run without a sandbox.)
2. Using the bundled browser, we can browse the target site and study how it processes our HTTP requests, notably the ```POST HTTP requests``` and related responses. The HTTP history tab logs every ```HTTP request``` and the respective response.
3. We log in to either of the accounts and click the Pay & Recharge button.
4. We then make a credit transfer: we click the Transfer button and enter the mobile number of the other account along with the amount you want to transfer. You can try to transfer an amount that exceeds your current balance and a small amount, such as $1, to see how the system responds in each case.


### Burp Suite: Repeater
In the image below, we can see:
- A POST request
- The details show the target phone number and a transfer amount of $1.5
- In the response, we can infer that the transaction is successful

<img width="1116" height="586" alt="image" src="https://github.com/user-attachments/assets/25dc4de0-9036-49c4-b67a-3489f98cbc98" />

Burp Sutie Proxy HTTP history showing an HTTP POST request and response.

Now that we have seen how the system reacts to valid and invalid requests, let’s see if we can exploit a race condition. 
1. Right-click on the POST request you want to duplicate and choose Send to Repeater.

<img width="1114" height="587" alt="image" src="https://github.com/user-attachments/assets/97140bf9-e1bb-456b-bf36-ba913dd959fd" />


Burp Suite Proxy HTTP history showing an HTTP POST request being sent to Repeater.

2. In the Repeater tab, as shown in the numbered screenshots below:
   1.  Click on the + icon next to the received request tab and select Create tab group
   2.  Assign a group name, and include the tab of the request you just sent to the importer before clicking Create
   3.  Right-click on the request tab and choose Duplicate tab (If this option is not available in your version, you can press CTRL+R multiple times instead)
   4.  As a starting point, we will duplicate it 20 times
   5.  Next to the Send button, the arrow pointed downwards will bring a menu to decide how you want to send the duplicated requests

<img width="1129" height="589" alt="image" src="https://github.com/user-attachments/assets/f6da004f-6f36-4747-8030-41eacd629754" />

Creating a tab group in Burp Suite Repeater.

<img width="1034" height="599" alt="image" src="https://github.com/user-attachments/assets/99ab1347-4118-4cab-abb4-7fd7a9a84885" />

Duplicating a tab 20 times within a tab group in Burp Suite Repeater.

3. Next, we will exploit the target application by sending the duplicated request. Using the built-in options in Burp Suite Repeater, the drop-down arrow offers the following choices:
   - Send group in sequence (single connection)
   - Send group in sequence (separate connections)
   - Send group in parallel

### **Sending Request Group in Sequence**

Sending the group in sequence provides two options:
   - Send group in sequence (single connection)
   - Send group in sequence (separate connections)

**Send Group in Sequence over a Single Connection**

This option establishes a single connection to the server and sends all the requests in the group’s tabs before closing the connection. This can be useful for testing for potential client-side desync vulnerabilities.

**Send Group in Sequence over Separate Connections**

As the name suggests, this option establishes a TCP connection, sends a request from the group, and closes the TCP connection before repeating the process for the subsequent request.

We tested this option to attack the web application. The screenshot below shows 21 TCP connections for the different POST requests in the group we sent.
- The first group (labelled 1) comprises five successful requests. We could confirm that they were successful by checking the respective responses. Furthermore, we noticed that each took around 3 seconds, as indicated by the duration (labelled 3).
- The second group (labelled 2) shows sixteen denied requests. The duration was around four milliseconds. It is interesting to check the Relative Start time as well.

<img width="1359" height="371" alt="image" src="https://github.com/user-attachments/assets/0e3a471f-81d6-42d6-8fe0-ed8be7f3e816" />


Wireshark showing 21 different POST requests; five are successful and the remaining ones are not successful.

The screenshot below shows the whole TCP connection for a request. We can confirm that the POST request was sent in a single packet.


Wireshark showing the TCP connection related to a POST request.

### **Send Request Group in Parallel**

Choosing to send the group’s requests in parallel would trigger the Repeater to send all the requests in the group at once. In this case, we notice the following, as shown in the screenshot below:

- In the Relative Start column, we notice that all 21 packets were sent within a window of 0.5 milliseconds (labelled 1).
- All 21 requests were successful; they resulted in a successful credit transfer. Each request took around 3.2 seconds to complete (labelled 2).

<img width="1321" height="355" alt="image" src="https://github.com/user-attachments/assets/0d5f4671-6661-4572-9074-4effafa2f1eb" />
Wireshark showing 21 requests sent in parallel and at the same time.

By paying close attention to the screenshot above, we notice that each request led to 12 packets; however, in the previous attempt (send in sequence), we see that each request required only 10 packets. Why did this happen?

According to [Sending Grouped HTTP Requests](https://portswigger.net/burp/documentation/desktop/tools/repeater/send-group) documentation, when sending in parallel, Repeater implements different techniques to synchronize the requests’ arrival at the target, i.e., they arrive within a short time frame. The synchronization technique depends on the HTTP protocol being used:
- In the case of ```HTTP/2+,``` the Repeater tries to send the whole group in a single packet. In other words, a single TCP packet would carry multiple requests.
- In the case of ```HTTP/1,``` the Repeater resorts to last-byte synchronization. This trick is achieved by withholding the last byte from each request. Only once all packets are sent without the last-byte are the last-byte of all the requests sent. The screenshot below shows our POST request sent over two packets.




Wireshark showing the TCP connection related to a POST request when using last-byte synchronization.



---










