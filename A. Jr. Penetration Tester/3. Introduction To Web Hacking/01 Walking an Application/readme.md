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

**Viewing the page source:**
- can often give us clues into whether a framework is in use and, if so, which framework and even what version. Knowing the framework and version can be a powerful find as there may be public vulnerabilities in the framework, and the website might not be using the most up to date version.
- Comments can sometime hold secrets left by developers erraniously.
-  In page source, there can be hidden links to a pages that may hold some private area used by the business for storing company/staff/customer information
