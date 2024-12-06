---
description: 'source: https://medium.com/@nimmughal799/recon-like-a-boss-9a0065648d10'
---

# 🔎 Recon Like a Boss

**Reconnaissance, or recon** for short, is the process of gathering information about a target with the goal of identifying vulnerabilities and potential attack vectors.

Effective recon is an essential part of any successful web security assessment or penetration testing engagement. However, many beginners find the process daunting and overwhelming, with a seemingly endless array of tools and techniques to choose from.

In this article, we’ll take a step-by-step approach to web recon, starting with the basics and building up to more advanced techniques.

## Agenda <a href="#id-4c7c" id="id-4c7c"></a>

* Increase Your Attack Area\
  • Determine Technologies used by Website.\
  • Amazon Web Service (AWS) Recon & Hacking\
  • Github Recon\
  • Content Discovery

## Increase Your Attack Area <a href="#id-1b8c" id="id-1b8c"></a>

<figure><img src="https://miro.medium.com/v2/resize:fit:275/1*FKn2kdozY5XH-nCmEytS_Q.jpeg" alt="" height="183" width="275"><figcaption></figcaption></figure>

### Recon- Go Back in Time <a href="#id-337c" id="id-337c"></a>

Wayback Machine to view old files like robots.txt\
and URLs

<figure><img src="https://miro.medium.com/v2/resize:fit:640/1*JlmXilQ-R1evogebQy6MAw.jpeg" alt="" height="400" width="640"><figcaption></figcaption></figure>

### Tools are out to automate this <a href="#id-2171" id="id-2171"></a>

**waybackurls.py**

Download:

[https://gist.github.com/mhmdia/adf6bff70142e5091792841d4b372050](https://gist.github.com/mhmdiaa/adf6bff70142e5091792841d4b372050)

**waybackrobots.py**

Download:

[https://gist.github.com/mhmdiaa/2742c5e147d49a804b408bfed3d32d07](https://gist.github.com/mhmdiaa/2742c5e147d49a804b408bfed3d32d07)

## Sub-domains Discovery <a href="#id-6294" id="id-6294"></a>

* Brute force on main domain
* Some scripts to automate this task\
  &#xNAN;**– Knockpy:-**\
  [https://github.com/guelfoweb/knock](https://github.com/guelfoweb/knock)

**Usage:** ./knockpy target.com

<figure><img src="https://miro.medium.com/v2/resize:fit:292/1*dHR4WN5KmQ8B_Fga6Vh3Uw.jpeg" alt="" height="173" width="292"><figcaption></figcaption></figure>

**– Sublist3r:-**\
[https://github.com/aboul3la/Sublist3r](https://github.com/aboul3la/Sublist3r)

**Usage:** python sublist3r.py -d target.com

* Find sub-domains with specific open ports

**Usage:** python sublist3r.py -d target.com -p 80,443

<figure><img src="https://miro.medium.com/v2/resize:fit:382/1*nnpB1ioCzIr4QaqAze1h3Q.png" alt="" height="132" width="382"><figcaption></figcaption></figure>

**– SubBrute**\
[https://github.com/TheRook/subbrute](https://github.com/TheRook/subbrute)

**Usage:** ./subbrute.py google.com

* You can give list of domains like this\
  **Usage:** ./subbrute.py -t list.txt

<figure><img src="https://miro.medium.com/v2/resize:fit:298/1*ZWxx-EaRTJuExXBAVH8LgQ.jpeg" alt="" height="169" width="298"><figcaption></figcaption></figure>

**Google Dork**\
site:target.com –site [www.target.com](http://www.target.com/)

**Online Resource:**

– [https://dnsdumpster.com/](https://dnsdumpster.com/)\
– [https://searchdns.netcraft.com/](https://searchdns.netcraft.com/)\
– [https://www.virustotal.com](https://www.virustotal.com/) (Go to search and\
type target.com)\
– [https://crt.sh/?q=%25paypal.com](https://crt.sh/?q=%25paypal.com) (Use “%target.com”. )

## Don’t Stop Here! <a href="#f333" id="f333"></a>

## Find Sub-domains of Sub-domain <a href="#fa7c" id="fa7c"></a>

* Some website have 5th and 6th level sub-domain

**Tool: altdns** ([https://github.com/infosec-au/altdns](https://github.com/infosec-au/altdns))\
**Input :** sub-domain list\
**Usage:** ./altdns.py -i subdomains.txt -o\
data\_output -w words.txt -r -s output.txt

**Tool:** **SubBrute**\
**Usage:**\
./subbrute.py target.com > sudomains.txt\
**Then**\
./subbrute.py –t subdomains.txt

**Now We Have**\
WaybackURls\
+\
Subdomains\
+\
Subdomains of Subdomains

## Sub-domain Validation <a href="#id-4f53" id="id-4f53"></a>

**Tool: EyeWitness** ([https://github.com/ChrisTruncer/EyeWitness](https://github.com/ChrisTruncer/EyeWitness))

\- Provide list of sub-domains and it will give you\
report with screenshots of sub-domain\
**Usage:** ./EyeWitness.py -f subdomains.txt

**Tool: Grab Them All** (Mozilla addon)

## Other sites on the same domain <a href="#id-8e87" id="id-8e87"></a>

[www.yougetsignal.com](http://www.yougetsignal.com/)

<figure><img src="https://miro.medium.com/v2/resize:fit:580/1*5LJcRKberYdTJFjvbF0tOA.png" alt="" height="362" width="580"><figcaption></figcaption></figure>

### Now We Have <a href="#id-2409" id="id-2409"></a>

WaybackURls\
+\
Subdomains\
+\
Subdomains of Subdomains\
+\
Other Sites on the same Domain

## Target IP Range <a href="#f990" id="f990"></a>

**Url:** [https://whois.arin.net](https://whois.arin.net/)\
• Search by Target IP

<figure><img src="https://miro.medium.com/v2/resize:fit:341/1*OEuVv2xyCa2Me-9RL7eOXA.png" alt="" height="148" width="341"><figcaption></figcaption></figure>

* Yahoo! owns a massive block of IP addresses\
  • From 98.136.0.0–98.139.255.255\
  • Which is 260,000 unique IP addresses

### Got Huge IP Range <a href="#id-685d" id="id-685d"></a>

<figure><img src="https://miro.medium.com/v2/resize:fit:273/1*sEoIwwYnzX1nSDTWqk07vA.jpeg" alt="" height="184" width="273"><figcaption></figcaption></figure>

### Real Case Study <a href="#id-8c4a" id="id-8c4a"></a>

Patrik Fehrenbach (@ITSecurityguard)\
Wrote a Bash script to download phpinfo.php\
file (if found) from Yahoo! IP range\
(98.136.0.0–98.139.255.255)

### Bash Script <a href="#id-7fef" id="id-7fef"></a>

```
#!/bin/bash
for ipa in 98.13{6..9}.{0..255}.{0..255}; do
wget -t 1 -T 5 http://${ipa}/phpinfo.php; done&
```

## Takeaways <a href="#id-90de" id="id-90de"></a>

• When hacking, consider a company’s entire\
infrastructure. I know that Patrik has employed\
similar techniques to find some more.\
(Eg. Many people keep Backup.rar)

• Additionally, you’ll notice there was 260,000\
potential addresses here, which would have been\
impossible to scan manually.

• When performing this type of testing,\
automation is hugely important.

## Find New Endpoints from JS Files <a href="#id-6fd3" id="id-6fd3"></a>

* Tools used

**1- Burp Suite:** Proxy\
**2- Zscanner:** A tool designed to scrape a list of\
URLs. This tool will also scrape .js urls found\
on each page\
**3- JS-Scan:** A tool designed to scrape a list of .js\
files and extract urls

### How to use these tools together?? <a href="#eddb" id="eddb"></a>

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*93HnV7YJpH-izvCTq5ECcw.jpeg" alt="" height="467" width="700"><figcaption></figcaption></figure>

## 1- Burpsuite <a href="#bbcb" id="bbcb"></a>

* Run Spider tool on your target in Burp Suite\
  • Once the spider has finished right click on the host\
  and click “Copy Urls in this host“

<figure><img src="https://miro.medium.com/v2/resize:fit:636/1*NQcFk48iJdKzCAmo4xJnXQ.png" alt="" height="522" width="636"><figcaption></figcaption></figure>

## 2- Zscanner <a href="#id-1436" id="id-1436"></a>

* Once copied, paste them into urls.txt\
  • Put urls.txt file in the root of Zscanner\
  Eg. c/xampp/htdocs/zscanner/urls.txt\
  • Now open zscanner in browser

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*kXilJuCwbqjK4GVQqsmSLw.png" alt="" height="199" width="700"><figcaption></figcaption></figure>

* Click on “Begin Scanner”\
  • 4 files are outputted in the /outputs/ folder:\
  JS-output.txt, GET-output.txt, POSTHost-\
  output.txt, POSTData-output.txt\
  • Copy JS-output.txt file and put it in the root of\
  JS-Scan root folder\
  Eg. c/xampp/js-scan/JS-output.txt

## 3- JS-Scan <a href="#id-5f85" id="id-5f85"></a>

* Open JS-Scan in browser

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*btYvCGDbHsBvtuwRi0Ai0g.png" alt="" height="270" width="700"><figcaption></figcaption></figure>

* Click on Run Scanner and you will see\
  something similar to this. That’s it.

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*wEaRdA19mtX3LgUTlKCpmQ.png" alt="" height="352" width="700"><figcaption></figcaption></figure>

### Takeaways <a href="#id-5454" id="id-5454"></a>

• Endpoints extracted from JS files are more\
vulnerable then Endpoints defined in\
WebPages.\
• Automated Scanners generally don’t scan\
Endpoints defined in JS files.\
• Developers & Testers don’t care about them.

## Technologies Used by Web <a href="#id-147a" id="id-147a"></a>

• **Wappalyzer** (Mozilla Addon)

<figure><img src="https://miro.medium.com/v2/resize:fit:241/1*UOPQOUQzR2Jx25ClE_8Q2A.png" alt="" height="209" width="241"><figcaption></figcaption></figure>

## Amazon Web Services (AWS or S3 Buckets) <a href="#id-35f4" id="id-35f4"></a>

* AWS Simple Storage Service (often shortened\
  to S3) is used by companies that don’t want to\
  build and maintain their own storage\
  repositories\
  • By using Amazon Simple Storage Service, they\
  can store objects and files on a virtual server\
  instead of on physical racks
* After the user has created their bucket, they\
  can start storing their source code,\
  certificates, passwords, content, databases\
  and other data.

### What if target is vulnerable? <a href="#id-512c" id="id-512c"></a>

* You can get full access to S3 bucket\
  • You can download, upload and overwrite files

### How to find S3 Buckets? <a href="#id-689d" id="id-689d"></a>

* **Google Dork**\
  site: amazonaws.com inurl: yahoo\
  &#xNAN;**• Tool: S3 bucket finder**\
  (Download: [https://digi.ninja/projects/bucket\_finder.php)](https://digi.ninja/projects/bucket_finder.php\))

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*__JjgvvRCnLgru-ZNPngpQ.png" alt="" height="136" width="700"><figcaption></figcaption></figure>

* **Burp Suite** can also Help

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*C8q90DRC-mi_aV5RzZnmmQ.png" alt="" height="343" width="700"><figcaption></figcaption></figure>

## AWS HACKING <a href="#id-2576" id="id-2576"></a>

* Install awscli in kali

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*1N1w7Aq_bshwwrfh4QSg9g.png" alt="" height="92" width="700"><figcaption></figcaption></figure>

* Interact with Bucket

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*fx0ZWHDSEIwKH-1zY0rOHg.png" alt="" height="82" width="700"><figcaption></figcaption></figure>

* Find World Writable Directory.

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*IE8CphOAwfqCZaxdTnQY-A.png" alt="" height="67" width="700"><figcaption></figcaption></figure>

### Now We Have <a href="#id-3308" id="id-3308"></a>

WaybackURls\
+\
Subdomains\
+\
Subdomains of Subdomains\
+\
Other Sites on the same Domain\
+\
IP Range\
+\
New Endpoints From JS Files\
+\
S3 Buckets

## Github Recon <a href="#a103" id="a103"></a>

### What you can find on Github? <a href="#id-6677" id="id-6677"></a>

• FTP Credentials\
• Secret Keys \[API\_key, Aws\_secret key, etc.]\
• Internal credentials \[Employee credentials]\
• API Endpoints\
• Domain Patterns

* Go to github and search\
  Eg.\
  \- “target.com” “dev”\
  \- “dev.target.com”\
  \- “target.com” API\_key\
  \- “target.com” password\
  \- “api.target.com”

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*1MQrVjt2usX1uaPcSuV3TA.png" alt="" height="388" width="700"><figcaption></figcaption></figure>

* Google can also help\
  **Dork:**\
  site: “github.com” + “Target” + password

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*DfEC0dag7W9h44-LnFyErA.png" alt="" height="291" width="700"><figcaption></figcaption></figure>

**Tools are out to automate this**\
• Gitrob\
• Git-all-secrets\
• truffleHog\
• Git-secrets\
• Repo-supervisor\
• Do it manually \[Best way]\
– All tools are available on github

**Tool- truffleHog**

* **Usage:**\
  truffleHog — regex — entropy=False [https://github.com/dxa4481/truffleHog.git](https://github.com/dxa4481/truffleHog.git)

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*glxi9vz4-Q19uNxtVFB9cg.png" alt="" height="259" width="700"><figcaption></figcaption></figure>

## Content Discovery <a href="#ce46" id="ce46"></a>

• Google is your friend\
• Use Google Dork to find:-\
\- File Extensions\
\- Parameters\
\- Login Page\
\- Sometimes Directory Structure\
\- Important Stuff

• I often use Google Dork to find files with\
specific extension which also reveal\
technology used by Target.

* **Google Dork:**\
  -site:target.com filetype:php\
  \- site:target.com filetype:aspx\
  \- site:target.com filetype:swf (Shockwave Flash)\
  \- site:target.com filetype:wsdl

### Find Parameter <a href="#e6ff" id="e6ff"></a>

**• Google Dork:**\
\- site: target.com inurl:.php?id=\
\- site: target.com inurl:.php?user=\
\- site: target.com inurl:.php?book=

### Find Login Page <a href="#id-5788" id="id-5788"></a>

**• Google Dork**\
\- site: target.com inurl:login.php\
\- site: target.com intext: “login”\
\- site: target.com inurl:portal.php\
\- site: target.com inurl:register.php

**(Note:** _if site has register page, there are chances_\
&#xNAN;_&#x74;hat site also have login page)_

### Find Directory Structure <a href="#id-49d9" id="id-49d9"></a>

**• Google Dork:**\
-site: target.com intext: “index of /”

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*hVneoXZrQUQfkAxeG4Gk0g.png" alt="" height="412" width="700"><figcaption></figcaption></figure>

### Find important Stuff <a href="#efe2" id="efe2"></a>

**• Google Dork:**\
-site: target.com filetype:txt\
\- site: target.com inurl:.php.txt\
-site: target.com ext:txt

_In most cases you will find robot.txt_\
&#xNAN;_&#x42;ut sometimes you will find really juicy stuff_

### Tools: <a href="#id-5f02" id="id-5f02"></a>

**– GoBuster** \[[https://github.com/OJ/gobuster](https://github.com/OJ/gobuster)]\
**Use:**\
gobuster –w wordlist.txt –u [http://trgt.com](http://trgt.com/)\
&#xNAN;**– Dirbuster**

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*Uqu0fM8qkvOD9_44QMst8Q.png" alt="" height="497" width="700"><figcaption></figcaption></figure>

**Thank you for taking the time to read my article .** I hope that the information and insights shared here have been valuable and practical for you to apply in your own recon efforts.

If you found this article helpful, please consider following me for more content related to security, hacking, and Bugbounty.

<figure><img src="https://miro.medium.com/v2/resize:fit:282/1*Pg4fJVi-qSl7C9wU67H7Bg.jpeg" alt="" height="179" width="282"><figcaption></figcaption></figure>
