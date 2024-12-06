# 🔎 Extensive Recon Guide For Bug Hunting

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

### WHAT IS RECONNAISSANCE?

Reconnaissance is one of the most important aspects of penetration testing. It’s also known as Recon.\
Recon will help you to increase attack surface area and may allow you to get more vulnerabilities but the ultimate goal is to dig deep in the target.

<img src="https://cdn.jsdelivr.net/gh/twitter/twemoji@14/assets/72x72/1f539.png" alt="🔹" data-size="line"> Recon = Increase in Attack surface = More vulnerabilities\
<img src="https://cdn.jsdelivr.net/gh/twitter/twemoji@14/assets/72x72/1f539.png" alt="🔹" data-size="line"> Recon = Finding untouched endpoints = Fewer duplicates\
<img src="https://cdn.jsdelivr.net/gh/twitter/twemoji@14/assets/72x72/1f539.png" alt="🔹" data-size="line"> Recon = Sharpening your axe before attack

***

### 1. SUBDOMAIN ENUMERATION

Subdomain enumeration is the process of finding subdomains for one or more domains.

**Tools used ⇒**

<img src="https://cdn.jsdelivr.net/gh/twitter/twemoji@14/assets/72x72/1f539.png" alt="🔹" data-size="line">Visual Recon⇒

* [https://Virustotal.com](https://virustotal.com/)
* [https://Subdomainfinder.c99.nl](https://subdomainfinder.c99.nl/)
* [https://crt.sh/?q=%25.target.com](https://crt.sh/?q=%25.target.com)
* [https://securitytrails.com/list/apex\_domain/target.com](https://securitytrails.com/list/apex_domain/target.com)
* [https://www.shodan.io/search?query=Ssl.cert.subject.CN%3A%22target.com%22](https://www.shodan.io/search?query=Ssl.cert.subject.CN%3A%22target.com%22)

<img src="https://cdn.jsdelivr.net/gh/twitter/twemoji@14/assets/72x72/1f539.png" alt="🔹" data-size="line">Command Line⇒

* [Amass](https://github.com/owasp-amass/amass)
* [Sunfinder](https://github.com/projectdiscovery/subfinder)
* [dnsx](https://github.com/projectdiscovery/dnsx)
* [Chaos](https://chaos.projectdiscovery.io/#/)
* [Assetfinder](https://github.com/tomnomnom/assetfinder)

**Oneliners for Subdomain Enumeration ⇒**

```shell
$ amass enum -passive -norecursive -noalts -df domians.txt -o subs-list.txt
$ dnsx -silent -d $domain -w ~/wordlist.txt -o ~/dnsbrute.txt
$ cat domain.txt | dnsgen - | massdns -r ~/resolvers.txt -o S -w alive.txt
```

***

### 2. FILTERING THE SUBDOMAINS WITH HTTPX

```shell
$ httpx -l domain.txt -timeout 13 -o domain-probe.txt
```

**PORT SCANNING ⇒**

```shell
$ naabu -list sub-list.txt -top-ports 1000 -exclude-ports 80,443,21,22,25 -o ports.txt
$ naabu -list sub-list.txt -p - -exclude-ports 80,443,21,22,25 -o ports.txt
$ cat domain-subs.txt | aquatone -ports xlarge -scan-timeout 300 -out aquatone.txt
```

**SUBDOMAIN OF SUBDOMAIN ENUMERATION ⇒**\
“This is one of the rare things people search for.”\
**Tools used:**

* **Subbrute**: [https://github.com/TheRook/subbrute](https://github.com/TheRook/subbrute)
* **Altdns**: [https://github.com/infosec-au/altdns](https://github.com/infosec-au/altdns)
  * Usage: `./altdns.py -i subdomains.txt -o data_output -w words.txt -r -s output.txt`

***

### 3. SCREENSHOT TOOLS

These tools are capable of taking screenshots of websites to view offline anytime.

**Tools used ⇒**

* **Eyewitness**: [https://github.com/FortyNorthSecurity/EyeWitness](https://github.com/FortyNorthSecurity/EyeWitness)
* **httpscreenshot**: [https://github.com/breenmachine/httpscreenshot](https://github.com/breenmachine/httpscreenshot)

***

### 4. BROADENING YOUR SCOPE

More targets lead to more option which ultimately lead to more opportunities.

* Crunchbase
* bgp.he.net
* tools.whoisxmlapi.com
* [https://whois.arin.net/](https://whois.arin.net/)

***

### 5. WHAT TO DO AFTER ENUMERATION? | Collecting URLs

* **Waybackurls**: [https://github.com/tomnomnom/waybackurls](https://github.com/tomnomnom/waybackurls)
* **Gau**: [https://github.com/lc/gau](https://github.com/lc/gau)
* **web archieve**: [https://web.archive.org/cdx/search/cdx?url=\*.target.com\&fl=original\&collapse=](https://web.archive.org/cdx/search/cdx?url=*.target.com\&fl=original\&collapse=) urlkey

***

### 6. TIPS AND TRICKS

1\. After collecting URLs, curl out the responses of the URLs and grep for the following URLs:

* drive.google
* docs.google
* /spreadsheets/d/
*   /document/d/

    ```shell
    $ cat domains.txt | katana -silent | while read url; do cu=$(curl -s $url | grep -E '(drive. google | docs. google | spreadsheet\/d | document.\/d\/)' ;echo -e " ==> $url" "\n"" $cu" ; done
    ```

2\. Using paramspider, gxss to detect Cross-site Scripting (XSS)

*   Commands:-

    ```shell
    $ cat params | qsreplace yogi | dalfox pipe --mining-dom --deep-domxss --mining-dict --remotepayloads=portswigger,payloadbox --remote wordlists=burp,assetnote -o xssoutput.txt
    ```

    ```shell
    $ cat alive.txt | waybackurls | gf xss | uro | httpx -silent | qsreplace '"><svg onload=confirm(1)>' | airixss -payload "confirm(1)" | tee xssBug3.txt
    ```

3\. Using **SQLidetector** to search for sqli

* **Tool Link**: [https://github.com/eslam3kl/SQLiDetector](https://github.com/eslam3kl/SQLiDetector)

***

### 7. SHODAN FOR RECON

**Shodan**: [https://www.shodan.io/](https://www.shodan.io/)

**Shodan Dorks ⇒**

> ssl: “target\[.]com” 200 http.title: “dashboard” –unauthenticated dashboard\
> org:“target.com” x-jenkins 200 — unauthenticated jenkins server\
> ssl:“target.com” 200 proftpd port:21 — proftpd port:21 org:“target.com”\
> http.html:zabbix — CVE-2022-24255 Main & Admin Portals: Authentication\
> Bypass org:“target.com” http.title:“phpmyadmin” —-php my admin\
> ssl:“target.com” http.title:"BIG-IP —F5 BIG-IP using CVE-2020-5902

**Example Writeup**:- [How I found XSS by searching in Shodan - Writeup](https://infosecwriteups.com/6943b799e648)

***

### 8. CENSYS FOR RECON

**Censys**: [https://www.censys.io/](https://www.censys.io/)\
**Example Writeup**:- [Let’s fuck waf using Origin IP: My approach on censys By Deepak Dhiman](https://virdoexhunter.com/lets-fuck-waf-using-origin-ip-my-approach-on-censys/)

***

### 9. FUZZING FOR SENSITIVE FILES & DIRECTORIES

* **Ffuf**: [https://github.com/ffuf/ffuf](https://github.com/ffuf/ffuf)
  * **Example Writeup**:-[Ffuf-ing Recon Writeup on FFuf](https://infosecwriteups.com/1ee4e79b3256)

```shell
$ for i in cat host.txt; do ffuf -u $i/FUZZ -w wordlist.txt -mc 200,302,401 -se ;done
```

* **Tip**: Fuzz for “/wp-content/debug.log” || Sometimes they contain SQL error, which can be chained.

***

### 10. FINDING SOURCE/BACKUP FILES

**Subdomain Name**: `y0gi.hacklido.com`

> y0gi.hacklido.com /y0gi.zip - hacklido.zip – admin.zip – backup.zip\
> y0gi.hacklido.com/y0gi/y0gi.zip - hacklido.zip – admin.zip – backup.zip\
> y0gi. hacklido.com/hacklido/y0gi.zip - hacklido.zip – admin.zip – backup.zip\
> y0gi. hacklido.com/admin/y0gi.zip - hacklido.zip – admin.zip – backup.zip

**Tool Link**: [https://github.com/musana/fuzzuli](https://github.com/musana/fuzzuli)

***

### 11. GOOGLE & GITHUB DORKING

**Trivial Tricks:**

* Find Sensitive Data in Cloud storage through Google Dork:

> site:[http://s3.amazonaws.com](http://s3.amazonaws.com/) “target\[.]com”\
> site:[http://blob.core.windows.net](http://blob.core.windows.net/) “target\[.]com”\
> site:[http://googleapis.com](http://googleapis.com/) “target\[.]com”\
> site:[http://drive.google.com](http://drive.google.com/) “target\[.]com”

* Github Leaks for AWS, Jira, Okta, etc:

> Org:“target” pwd/pass/passwd/password\
> “target.atlassian” pwd/pass/passwd/password\
> “target.okta” pwd/pass/passwd/password\
> “Jira.target” pwd/pass/passwd/password

* Also search in Google groups, Gitlabs.

***

### 12. JAVASCRIPT\[JS] FILES RECON

<img src="https://cdn.jsdelivr.net/gh/twitter/twemoji@14/assets/72x72/1f539.png" alt="🔹" data-size="line"> Grep all urls from wayback or gau.

* Collect all js file “.js”
*   Filter js file:

    ```go
    $ httpx -content-type | grep 'application/javascript'
    ```
*   Perform Nuclei scan

    ```shell
    $ nuclei -t /root/nuclei-templates/exposures/
    ```

<img src="https://cdn.jsdelivr.net/gh/twitter/twemoji@14/assets/72x72/1f539.png" alt="🔹" data-size="line"> Js Recon Tip:

* Collect all endpoints from Js files & Create a wordlist from those.
* Craft a POST request with any parameter.
* Use that request to fuzz for sensitive directory.
* **Tools**:- [JSFScan.sh](https://github.com/KathanP19/JSFScan.sh) , Jsminer {Burp Extension} , [Trufflehog](https://github.com/trufflesecurity/trufflehog)

***

### 13. SOME AUTOMATION FRAMEWORKS

<img src="https://cdn.jsdelivr.net/gh/twitter/twemoji@14/assets/72x72/1f539.png" alt="🔹" data-size="line">**Sudomy**: [https://github.com/Screetsec/Sudomy](https://github.com/Screetsec/Sudomy)\
<img src="https://cdn.jsdelivr.net/gh/twitter/twemoji@14/assets/72x72/1f539.png" alt="🔹" data-size="line">**Reconftw**: [https://github.com/six2dez/reconftw](https://github.com/six2dez/reconftw)

***

### Final Thoughts

<img src="https://cdn.jsdelivr.net/gh/twitter/twemoji@14/assets/72x72/1f539.png" alt="🔹" data-size="line">**Verify Data**

* Some data are intended, No bug here.

<img src="https://cdn.jsdelivr.net/gh/twitter/twemoji@14/assets/72x72/1f539.png" alt="🔹" data-size="line">**Reported > Invalid**

* Don’t get angry, You may lose bonds with good program

<img src="https://cdn.jsdelivr.net/gh/twitter/twemoji@14/assets/72x72/1f539.png" alt="🔹" data-size="line">**Yes, They do accept Third Party**

* Your crafting and exploits are gold. Make it high as you can

<img src="https://cdn.jsdelivr.net/gh/twitter/twemoji@14/assets/72x72/1f539.png" alt="🔹" data-size="line">**Be humble with Program**

* Money going no where. Don’t message constant to team
