## Get Parameter as much as possible

1. https://github.com/devanshbatham/ParamSpider
2. gospider -S tageturls.txt -c 10 -d 5 --blacklist ".(jpg|jpeg|gif|css|tif|tiff|png|ttf|woff|woff2|ico|pdf|svg|txt)" --other-source | grep -e "code-200" | awk '{print $5}'|grep "=" | qsreplace -a | dalfox pipe -o result.txt
3. https://github.com/s0md3v/Arjun
4. https://github.com/hakluke/hakrawler

Combine all in a file and remove duplicates.

## Run XSS fuzzer
1. https://github.com/s0md3v/XSStrike
2. https://github.com/hahwul/dalfox

<hr>
