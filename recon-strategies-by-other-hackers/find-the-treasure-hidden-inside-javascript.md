---
description: >-
  source:
  https://pravinponnusamy.medium.com/find-the-treasure-hidden-in-javascript-546827e1a4e2
---

# 🔎 Find the treasure hidden inside JavaScript

This is another bug bounty automation blog explaining my thought process for crawling and enumerating JavaScript file. Modern web applications are heavily built on JavaScript. Starting from API calls to most business logics, many critical functionalities are built on client-side nowadays. It is just that if we are able to completely understand the client side logic of a target, then we will able to disclose some unique vulnerabilities.

There are lot of great open source tools specifically built for crawling and enumerating JavaScript files. Every bug hunting individual will have unique thought process in terms of approaching the target and that is what make everyone find different vulnerabilities. Since I have been exploring more on web application, I was wondering how to tweak existing methodologies. _**My thought process is what if I am able to automate my methodology according my requirement?**_

My requirement is to enumerate each end URL of website and get below details,(Example: https://example.com)

1. Get all the links and JavaScript files existing under the given end URL.(Tool: hakrawler)
2. Identify sensitive information in the given end URL and JavaScript files.(Tool: gf from Tomnomnom)
3. Extract any subdomains hidden inside the JavaScript files.(Tool: JSFinder.py)
4. Extract the relative and absolute path from JavaScript files.(Tool: jsa.py)
5. Extract secrets and links from JavaScript files using JScanner. (Tool: JScanner.py)
6. Finally, find hidden secrets using nuclei. (Tool: nuclei)

_Note: All the tools outlined above are open source tools. We can use them at our own risk and not to hack others._

Github link:

[offsecdawn/mini\_reconYou can't perform that action at this time. You signed in with another tab or window. You signed out in another tab or…github.com](https://github.com/offsecdawn/mini\_recon?source=post\_page-----546827e1a4e2--------------------------------)

Let me explain my approach with screenshots. I have given comments wherever required to make them easily understandable. Below screenshot shows how to extract only the FQDN value (domain name) from a given URL.

<figure><img src="https://miro.medium.com/v2/resize:fit:1250/1*5F0nASHR7FMgbLn9cegiRQ.png" alt="" height="308" width="1000"><figcaption><p>To extract the FQDN value</p></figcaption></figure>

_**Get all the links and JavaScript files existing under the given end URL:**_

<figure><img src="https://miro.medium.com/v2/resize:fit:875/1*nKp4p7le41w56uPWCjT5xg.png" alt="" height="276" width="700"><figcaption></figcaption></figure>

After extracting the domain value from the given URL, the value is passed to hakcrawler to crawl and extract all possible links and JavaScript files under the given path. The results are stored into _“files\_tmp.txt”_ temporarily and later refined such a way that the file contains only domain related values _(grep -i “$domain”) in there._

Now, file name _“files.txt”_ will contain the results.

_**Identify sensitive information in the given end URL and JavaScript files:**_

<figure><img src="https://miro.medium.com/v2/resize:fit:575/1*8EtPSXVqw-cd0a-ixE985A.png" alt="" height="471" width="460"><figcaption><p>“gf_patterns” file</p></figcaption></figure>

The screenshot given above shows the file called _“gf\_patterns”._ There are multiple gf patterns created by Tomnomnom to identify the patterns inside a file. This tool is very useful when hunter would like to identify patterns such as looking for AWS\_keys, firebase URL or details and so on. The great flexibility about gf tool is any bug bounty hunter will be able to create his/her pattern according their need.

As part of my script I have utilized only a part of the patterns such as

1. _awskeys- check for aws keys in JavaScript files_
2. _firebase-check for firebase URLs in JavaScript files_
3. _json\_file-check for Json file in JavaScript files_
4. _s3-buckets-check for aws s3 buckets in JavaScript files_
5. _sec-check for secrets in JavaScript files_
6. _ip-check for ip addresses in JavaScript files_
7. _upload-fields-check for file upload fields in JavaScript files_

<figure><img src="https://miro.medium.com/v2/resize:fit:875/1*SieWQmi-mq7LSY2GZCew0Q.png" alt="" height="249" width="700"><figcaption></figcaption></figure>

_gf\_PATTERN()_ function will load each JS files using command line browser called _curl_ and identify the patterns outlined above using tool called _gf._ By doing this, the hunter will be able to find some hidden information inside JavaScript files.

_**Extract any subdomains hidden inside the JavaScript files:**_

As a bug bounty hunter everyone knows how important it is to find a subdomain. Since modern applications will have communications with multiple internal domains, it is a good strategy to find out subdomains referred inside the JavaScript. Because, most of the time hidden sub domains will be always running and might lead to internal access to the target as well.

<figure><img src="https://miro.medium.com/v2/resize:fit:1250/1*sv9TCLb7Ku_-X3B6ivX4eQ.png" alt="" height="141" width="1000"><figcaption></figcaption></figure>

JSFinder.py is an open source tool which can extract the subdomains related to our main domain from all JavaScript files.

_**Extract the relative and absolute path from JavaScript files:**_

Content discovery is one of the most important tasks in bug bounty hunting as it might disclose some sensitive information, installation files, back up files and so on. Most of the time hunters will perform content discovery using pre-defined list. However, I would strongly suggest all to create a customized directory list according to the target. Because, the probability of success is higher as compared to general wordlist.

JavaScript files reside in the target will immensely help us a lot identify directory paths. Because, those path will be used regularly and the paths will be unique as per the target as well.

<figure><img src="https://miro.medium.com/v2/resize:fit:875/1*PZJFDt_ZNj7T42b81WHT8w.png" alt="" height="47" width="700"><figcaption></figcaption></figure>

_“jsa.py”_ tool _helped me a lot find out hidden directory paths inside JavaScript files._

_**Extract secrets and links from JavaScript files using JScanner:**_

JavaScript files are considered as treasure in modern day web applications. Sometimes, developers might mistakenly leave some sensitive information such as API keys, data base connection details on client side script. Thus, it is imperative to look for secrets and sensitive information inside JavaScript files.

<figure><img src="https://miro.medium.com/v2/resize:fit:1250/1*IB-4e8gOc7_Xwen1FT78lw.png" alt="" height="45" width="1000"><figcaption></figcaption></figure>

JScanner tool help us identify secrets and sensitive information from JavaScript files.

_**Find hidden secrets using nuclei:**_

As outline above, nuclei also does great job in identifying sensitive information from JavaScript files. This tool has lot of templates created by security researchers. I would recommend everyone to explore this tool.

<figure><img src="https://miro.medium.com/v2/resize:fit:875/1*jsqv0P0iZacKK9LRo10n8w.png" alt="" height="99" width="700"><figcaption></figcaption></figure>

I hope you have enjoyed the content.!!!

Since most of the hunters know what each tools do, I did not want to waste your time explaining the functionality of the tools. I have just explained my approach in bug hunting. Hope everyone finds their own way!!!

I would like to give credits to all security researchers who have created these tools.

_**Disclaimer:**_ You shall not misuse this information to gain unauthorized access. However you may try out these hacks on your own computer at your own risk. Performing **hack** attempts (without permission) on computers that you do not own is illegal.

_References:_

[tomnomnom/gfA wrapper around grep to avoid typing common patterns. I use grep a lot. When auditing code bases, looking at the…github.com](https://github.com/tomnomnom/gf?source=post\_page-----546827e1a4e2--------------------------------)[w9w/JSAJavascript security analysis (JSA) is a program for javascript analysis during web application security assessment…github.com](https://github.com/w9w/JSA?source=post\_page-----546827e1a4e2--------------------------------)[hakluke/hakrawlerhakrawler is a Go web crawler designed for easy, quick discovery of endpoints and assets within a web application.github.com](https://github.com/hakluke/hakrawler?source=post\_page-----546827e1a4e2--------------------------------)[Threezh1/JSFinderJSFinder is a tool for quickly extracting URLs and subdomains from JS files on a website.github.com](https://github.com/Threezh1/JSFinder?source=post\_page-----546827e1a4e2--------------------------------)[machinexa2/JScannerA simple yet effective tool to find using custom and predefined regex for recon, vulnerabilites and secrets. It scans…github.com](https://github.com/machinexa2/JScanner?source=post\_page-----546827e1a4e2--------------------------------)[projectdiscovery/nucleiFast and customisable vulnerability scanner based on simple YAML based DSL. How \* Install \* For Security Engineers \*…github.com](https://github.com/projectdiscovery/nuclei?source=post\_page-----546827e1a4e2--------------------------------)

If you like the content, please **follow me on **_**medium**_** and **_**LinkedIn**_

_**LinkedIn:**_ [_**https://www.linkedin.com/in/pravin-r-p-oscp-28497712b/**_](https://www.linkedin.com/in/pravin-r-p-oscp-28497712b/)
