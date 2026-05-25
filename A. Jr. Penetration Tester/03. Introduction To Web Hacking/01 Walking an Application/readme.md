# Walking an Application

This Room is not Free, Rather it's a Premium one.

Thus I have documented more details of my Learning in a private Repo.

In the room, I reviewed a web application for security issues using only my Web Browsers Developer tools.

I basically learnt hacking with just a browser and no other tools nor scripts.

## My TakeHomes from the Room:

Manually reviewing a web application for security issues is key because more often than not, automated security tools and scripts will miss many potential vulnerabilities and useful information.

**Some in-built browser tools:**

- View Source - view the human-readable source code of a website.
- Inspector - inspect page elements and make changes to view usually blocked content.
- Debugger - Inspect and control the flow of a page's JavaScript
- Network - See all the network requests a page makes.

As a penetration tester, your role when reviewing a website or web application is to discover features that could potentially be vulnerable and attempt to exploit them to assess whether or not they are. These features are usually parts of the website that require some interactivity with the user.

Any component that accepts input, processes data, manages identity, or controls access is a prime target during web application penetration testing. The tester is less focused on static content and more on dynamic, interactive, and state-changing features

When doing a Web pentest, an excellent place to start is just with your browser exploring the website and noting down the individual pages/areas/features with a summary for each one.

An example site review for the a website would look something like this:

<img width="829" height="603" alt="image" src="https://github.com/user-attachments/assets/cadeac99-6485-4322-aa21-9f7fea115975" />

**A. Viewing the page source:**
- can often give us clues into whether a framework is in use and, if so, which framework and even what version. Knowing the framework and version can be a powerful find as there may be public vulnerabilities in the framework, and the website might not be using the most up to date version.
- Comments can sometime hold secrets left by developers erraniously.
-  In page source, there can be hidden links to a pages that may hold some private area used by the business for storing company/staff/customer information

**B. Developer Tools:**
- Included in every Modern Browser
- A tool kit used to aid web developers in debugging web applications and gives them an understanding of a website to see what is going on.
- As a pentester, we can leverage these tools to provide us with a much better understanding of the web application.
- Key Features:
  - **Inspector:**  provides the penetration Tester with a live representation of what is currently on the website.
  - **Debugger:** intended for debugging JavaScript code but it gives the penetration tester the option of digging deep into the JavaScript code. A feature of debugger called breakpoints are points in the code that we can force the browser to stop processing the JavaScript and pause the current execution.
  - **Network:** The network tab on the developer tools can be used to keep track of every external request a webpage makes. If you click on the Network tab and then refresh the page, you'll see all the files the page is requesting.  AJAX is a method for sending and receiving network data in a web application background without interfering by changing the current web page.
 
**NOTE**:  Many times when viewing javascript files, you'll notice that everything is on one line, which is because it has been minimised, which means all formatting ( tabs, spacing and newlines ) have been removed to make the file smaller. This file is no exception to this, and it has also been obfusticated, which makes it purposely difficult to read, so it can't be copied as easily by other developers.




