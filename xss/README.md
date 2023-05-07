## Get Parameter as much as possible

1. https://github.com/devanshbatham/ParamSpider
2. gospider -S tageturls.txt -c 10 -d 5 --blacklist ".(jpg|jpeg|gif|css|tif|tiff|png|ttf|woff|woff2|ico|pdf|svg|txt)" --other-source | grep -e "code-200" | awk '{print $5}'|grep "=" | qsreplace -a | dalfox pipe -o result.txt
3. Waybackurls | gau
4. https://github.com/s0md3v/Arjun
5. https://github.com/hakluke/hakrawler
6. https://github.com/PortSwigger/param-miner

Combine all in a file and remove duplicates.

## Run XSS fuzzer
1. https://github.com/s0md3v/XSStrike
2. https://github.com/hahwul/dalfox


Resources:
- https://blog.yeswehack.com/yeswerhackers/parameter-discovery-quick-guide-to-start/
- https://infosecwriteups.com/tale-of-my-first-xss-27f622bc47c0
<hr>

## dalfox usage:
```
gf xss domain.txt | grep -Eo 'https?://[^\"]+' > domain.txt
dalfox file domain.txt -w 20 --silence
```

<hr>

## another technique to find XSS

- Scrape all urls from the domain using gau
- then sort the result using grep command: `cat url.txt | grep “utm_”`
- do testing

resources:
- https://santoshdbobade.medium.com/how-i-got-reflected-cross-site-scripting-rxss-on-manchester-metropolitan-university-700b36cb4f53
