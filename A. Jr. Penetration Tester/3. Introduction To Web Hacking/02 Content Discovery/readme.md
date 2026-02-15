# Content Discovery

This is a THM  premium Room.

Through the room, I have learnt the various ways of Discovering hidden or private content on a web Browser that could lead to new vulnerabilities.

## My TakeHomes from the room

in regards to a web application, content could be, for example, pages or portals intended for staff usage, older versions of the website, backup files, configuration files, administration panels, etc.

There are three main ways of discovering content on a website:
- **Manual Detection:**
  - **Robots.txt file:** a document that tells search engines which pages they are and aren't allowed to show on their search engine results or ban specific search engines from crawling the website altogether. It can be common practice to restrict certain website areas so they aren't displayed in search engine results. These pages may be areas such as administration portals or files meant for the website's customers. This file gives us a great list of locations on the website that the owners don't want us to discover as penetration testers.
  - **Favicon:** Sometimes when frameworks are used to build a website, a favicon that is part of the installation gets leftover, and if the website developer doesn't replace this with a custom one, this can give us a clue on what framework is in use. OWASP host a database of common framework icons that you can use to check against the targets favicon [OWASP_favicon_database](https://wiki.owasp.org/index.php/OWASP_favicon_database). Once we know the framework stack, we can use external resources to discover more about it
  - **Sitemap.xml:** Unlike the robots.txt file, which restricts what search engine crawlers can look at, the sitemap.xml file gives a list of every file the website owner wishes to be listed on a search engine. These can sometimes contain areas of the website that are a bit more difficult to navigate to or even list some old webpages that the current site no longer uses but are still working behind the scenes.
  - **HTTP Headers:** When we make requests to the web server, the server returns various HTTP headers. These headers can sometimes contain useful information such as the webserver software and possibly the programming/scripting language in use.
    ```
    curl http://<Ip address> -v
    ```

  - **Framework Stack:** Once the framework of a website is established, you can then locate the framework's website. From there, learn more about the software and other information, possibly leading to more content we can discover.
- **Automated Detection:** using tools instead of amnual search. There are many different content discovery tools available, all with their features and flaws.
  - Example of Automation Tools:
    - **Using ffuf:**

      ```
      user@machine$ ffuf -w /usr/share/wordlists/SecLists/Discovery/Web-Content/common.txt -u http://<IP address>/FUZZ
      ```
      
    - **Using dirb**:

      ```
      user@machine$ dirb http://<IP address>/ /usr/share/wordlists/SecLists/Discovery/Web-Content/common.txt
      ```
      
    - **Using Gobuster:**

      ```
      user@machine$ gobuster dir --url http://<IP address>/ -w /usr/share/wordl
      ```
      
- **OSINT (Open-Source Intelligence):**
  - **Google Hacking / Dorking**: More information about google hacking can be found [here.](https://en.wikipedia.org/wiki/Google_hacking)
  - **Wappalyzer:** [wappalyzer](https://www.wappalyzer.com/) is an online tool and browser extension that helps identify what technologies a website uses, such as frameworks, Content Management Systems (CMS), payment processors and much more, and it can even find version numbers as well.
  - **Wayback Machine:** The [Wayback Machine](https://archive.org/web/) is a historical archive of websites that dates back to the late 90s. You can search a domain name, and it will show you all the times the service scraped the web page and saved the contents. This service can help uncover old pages that may still be active on the current website.
  - **GitHub**: You can use GitHub's search feature to look for company names or website names to try and locate repositories belonging to your target. Once discovered, you may have access to source code, passwords or other content that you hadn't yet found.
  - **AWS S3 Buckets:** S3 Buckets allows people to save files and even static website content in the cloud accessible over HTTP and HTTPS. The owner of the files can set access permissions to either make files public, private and even writable. Sometimes these access permissions are incorrectly set and inadvertently allow access to files that shouldn't be available to the public. The format of the S3 buckets is http(s)://{name}.s3.amazonaws.com where {name} is decided by the owner. S3 buckets can be discovered in many ways, such as finding the URLs in the website's page source, GitHub repositories, or even automating the process. One common automation method is by using the company name followed by common terms such as {name}-assets, {name}-www, {name}-public, {name}-private, etc.



