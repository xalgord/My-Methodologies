# 🔧 Tools and their Uses

### Takeover ([https://github.com/m4ll0k/takeover](https://github.com/m4ll0k/takeover))

```
python3 takeover.py -l all.txt -v
```

### Dalfox ([https://github.com/hahwul/dalfox](https://github.com/hahwul/dalfox))

```
dalfox file gf_xss.txt -w 50 --silence
```

### Breach-Parse ([https://github.com/hmaverickadams/breach-parse](https://github.com/hmaverickadams/breach-parse))

```
breach-parse @gmail.com gmail.txt
```

## Subdomain Enumeration

```bash
#!/bin/bash

# Subdomain Enumeration Script

domain="$1"
output_dir="subdomain_enum_output"
resolvers_file="resolvers.txt"
wordlist_file="wordlist.txt"

if [ -z "$domain" ]; then
    echo "Usage: $0 <domain>"
    exit 1
fi

# Create the output directory if it doesn't exist
mkdir -p "$output_dir"

# AMASS with timeout, increased concurrency, resolvers, and brute-force enumeration
echo "[*] Running Amass..."
amass enum -d "$domain" -o "$output_dir/amass.txt" -max-dns-queries 200 -rf "$resolvers_file" -passive -brute -w "$wordlist_file" -timeout 15

# Rest of the subdomain enumeration tools...

# Knock
echo "[*] Running Knock..."
knockpy "$domain" -o "$output_dir/knock.txt"

# Sublist3r
echo "[*] Running Sublist3r..."
sublist3r -d "$domain" -o "$output_dir/sublist3r.txt"

# assetfinder (alternative to Anubis)
echo "[*] Running assetfinder..."
assetfinder --subs-only "$domain" | tee "$output_dir/assetfinder.txt"

# Subfinder
echo "[*] Running Subfinder..."
subfinder -d "$domain" -o "$output_dir/subfinder.txt"

# theHarvester
echo "[*] Running theHarvester..."
theHarvester -d "$domain" -l 500 -b all > "$output_dir/theharvester.txt"

# bbot
echo "[*] Running bbot..."
bbot -t "$domain" -f subdomain-enum -o "$output_dir/bbot"
find "$output_dir/bbot" -name "output.txt" -exec awk '/\[DNS_NAME\]/ {sub(/\[DNS_NAME\]\s+/, ""); print $1}' {} \; > "$output_dir/bbot.txt"
rm -rf "$output_dir/bbot"

# Amass Again for Comprehensive Results
echo "[*] Running Amass (Passive)..."
amass enum -passive -d "$domain" -o "$output_dir/amass_passive.txt"

# httprobe
echo "[*] Running httprobe..."
cat "$output_dir/"*".txt" | sort -u | httprobe -c 50 | tee "$output_dir/httprobe.txt"

echo "[+] Subdomain enumeration completed. Results saved in '$output_dir' directory."

```

```bash
#!/bin/bash
url=$1

if [ ! -d "$url" ];then
    mkdir "$url"
fi

if [ ! -d "$url/recon" ];then
    mkdir "$url/recon"
fi

if [ ! -d "$url/recon/scans" ];then
    mkdir "$url/recon/scans"
fi

if [ ! -d "$url/recon/httprobe" ];then
    mkdir "$url/recon/httprobe"
fi

if [ ! -d "$url/recon/potential_takeovers" ];then
    mkdir "$url/recon/potential_takeovers"
fi

if [ ! -d "$url/recon/wayback" ];then
    mkdir "$url/recon/wayback"
fi

if [ ! -d "$url/recon/wayback/params" ];then
    mkdir "$url/recon/wayback/params"
fi

if [ ! -d "$url/recon/wayback/extensions" ];then
    mkdir "$url/recon/wayback/extensions"
fi

if [ ! -f "$url/recon/httprobe/alive.txt" ];then
    touch "$url/recon/httprobe/alive.txt"
fi

if [ ! -f "$url/recon/final.txt" ];then
    touch "$url/recon/final.txt"
fi

echo "[+] Harvesting subdomains with assetfinder..."
assetfinder "$url" >> "$url/recon/assets.txt"
cat "$url/recon/assets.txt" | grep "$1" >> "$url/recon/final.txt"
rm "$url/recon/assets.txt"

echo "[+] Double checking for subdomains with amass..."
amass enum -passive -norecursive -noalts -d "$url" >> "$url/recon/f.txt"
subfinder -d "$url" -o "$url/recon/subfinder.txt" >> "$url/recon/f.txt"
sublist3r -d "$url"  -o "$url/recon/sublist3r.txt" >> "$url/recon/f.txt"

sort -u "$url/recon/f.txt" >> "$url/recon/final.txt"

rm "$url/recon/f.txt"

echo "[+] Probing for alive domains..."
cat "$url/recon/final.txt" | sort -u | httprobe -s -p https:443 | sed 's/https\?:\/\///' | tr -d ':443' >> "$url/recon/httprobe/a.txt"
sort -u "$url/recon/httprobe/a.txt" > "$url/recon/httprobe/alive.txt"
rm "$url/recon/httprobe/a.txt"

echo "[+] Checking for possible subdomain takeover..."
if [ ! -f "$url/recon/potential_takeovers/potential_takeovers.txt" ];then
    touch "$url/recon/potential_takeovers/potential_takeovers.txt"
fi

subjack -w "$url/recon/final.txt" -t 100 -timeout 30 -ssl -c ~/opt/subjack/fingerprints.json -v 3 -o "$url/recon/potential_takeovers/potential_takeovers.txt"

# echo "[+] Scanning for open ports..."
# nmap -iL "$url/recon/httprobe/alive.txt" -T4 -oA "$url/recon/scans/scanned.txt"

echo "[+] Scraping gau data..."
cat "$url/recon/final.txt" | gau --threads 20 --fc 404 --timeout 60 >> "$url/recon/wayback/wayback_output.txt"
sort -u "$url/recon/wayback/wayback_output.txt"

echo "[+] Pulling and compiling all possible params found in wayback data..."
cat "$url/recon/wayback/wayback_output.txt" | grep '?*=' | cut -d '=' -f 1 | sort -u >> "$url/recon/wayback/params/wayback_params.txt"
parallel -j 10 --delay 1 'echo {}=' >> "$url/recon/wayback/params/wayback_params.txt" :::: "$url/recon/wayback/params/wayback_params.txt"

echo "[+] Pulling and compiling js/php/aspx/jsp/json files from wayback output..."
cat "$url/recon/wayback/wayback_output.txt" | parallel -j 10 --delay 1 'ext={##*.}; if [[ "$ext" == "js" ]]; then echo {} >> "$url/recon/wayback/extensions/js1.txt"; sort -u "$url/recon/wayback/extensions/js1.txt" >> "$url/recon/wayback/extensions/js.txt"; elif [[ "$ext" == "html" ]]; then echo {} >> "$url/recon/wayback/extensions/jsp1.txt"; sort -u "$url/recon/wayback/extensions/jsp1.txt" >> "$url/recon/wayback/extensions/jsp.txt"; elif [[ "$ext" == "json" ]]; then echo {} >> "$url/recon/wayback/extensions/json1.txt"; sort -u "$url/recon/wayback/extensions/json1.txt" >> "$url/recon/wayback/extensions/json.txt"; elif [[ "$ext" == "php" ]]; then echo {} >> "$url/recon/wayback/extensions/php1.txt"; sort -u "$url/recon/wayback/extensions/php1.txt" >> "$url/recon/wayback/extensions/php.txt"; elif [[ "$ext" == "aspx" ]]; then echo {} >> "$url/recon/wayback/extensions/aspx1.txt"; sort -u "$url/recon/wayback/extensions/aspx1.txt" >> "$url/recon/wayback/extensions/aspx.txt"; fi' :::: "$url/recon/wayback/wayback_output.txt"

rm "$url/recon/wayback/extensions/js1.txt"
rm "$url/recon/wayback/extensions/jsp1.txt"
rm "$url/recon/wayback/extensions/json1.txt"
rm "$url/recon/wayback/extensions/php1.txt"
rm "$url/recon/wayback/extensions/aspx1.txt"

#echo "[+] Running eyewitness against all compiled domains..."
#python3 EyeWitness/EyeWitness.py --web -f "$url/recon/httprobe/alive.txt" -d "$url/recon/eyewitness" --resolve
```

## Subdomain Finder tools:

chaos , subfinder , finddomain, assetfinder, amass

| Type          | Tool              | Repo                                                                                                 | Stars | Latest release | Latest commit | Programming Language |
| ------------- | ----------------- | ---------------------------------------------------------------------------------------------------- | ----- | -------------- | ------------- | -------------------- |
| Passive       | Amass             | [https://github.com/OWASP/Amass](https://github.com/OWASP/Amass)                                     | 6,1K  | 3.15.0         | 12 nov 2021   | Go                   |
| Passive       | Sublist3r         | [https://github.com/aboul3la/Sublist3r](https://github.com/aboul3la/Sublist3r)                       | 6,5K  | 1.1            | 29 jul 2020   | Python               |
| Passive       | crobat            | [https://github.com/Cgboal/SonarSearch](https://github.com/Cgboal/SonarSearch)                       | 428   | –              | 17 sept 2021  | Go                   |
| Passive       | chaos             | [https://github.com/projectdiscovery/chaos-client](https://github.com/projectdiscovery/chaos-client) | 270   | 0.1.9          | 20 oct 2021   | Go                   |
| Passive       | subfinder         | [https://github.com/projectdiscovery/subfinder](https://github.com/projectdiscovery/subfinder)       | 4,4K  | 2.4.9          | 20 oct 2021   | Go                   |
| Passive       | assetfinder       | [https://github.com/tomnomnom/assetfinder](https://github.com/tomnomnom/assetfinder)                 | 1,5K  | –              | 15 Apr 2020   | Go                   |
| Passive       | waybackurls       | [https://github.com/tomnomnom/waybackurls](https://github.com/tomnomnom/waybackurls)                 | 1,5K  | –              | 24 Apr 2020   | Go                   |
| Passive       | gau               | [https://github.com/lc/gau](https://github.com/lc/gau)                                               | 1,6K  | 2.0.6          | 15 nov 2021   | Go                   |
| Passive       | github-subdomains | [https://github.com/gwen001/github-subdomains](https://github.com/gwen001/github-subdomains)         | 276   | –              | 15 Apr 2021   | Go                   |
| Passive       | findomain         | [https://github.com/Findomain/Findomain](https://github.com/Findomain/Findomain)                     | 2,1K  | 5.0.0          | 9 nov 2021    | Rust                 |
| Passive       | OneForAll         | [https://github.com/shmilylty/OneForAll](https://github.com/shmilylty/OneForAll)                     | 4,1K  | 0.4.3          | 19 nov 2021   | Python               |
| Resolution/BF | shuffledns        | [https://github.com/projectdiscovery/shuffledns](https://github.com/projectdiscovery/shuffledns)     | 692   | 1.0.4          | 20 oct 2021   | Go                   |
| Resolution/BF | puredns           | [https://github.com/d3mondev/puredns](https://github.com/d3mondev/puredns)                           | 580   | 2.0.1          | 25 jun 2021   | Go                   |
| Resolution/BF | dnsx              | [https://github.com/projectdiscovery/dnsx](https://github.com/projectdiscovery/dnsx)                 | 629   | 1.0.7          | 19 nov 2021   | Go                   |
| Resolution/BF | dnscan            | [https://github.com/rbsec/dnscan](https://github.com/rbsec/dnscan)                                   | 643   | –              | 28 oct 2021   | Python               |
| Resolution/BF | gobuster          | [https://github.com/OJ/gobuster](https://github.com/OJ/gobuster)                                     | 5,3K  | 3.1.0          | 21 jun 2021   | Go                   |
| Resolution/BF | aiodnsbrute       | [https://github.com/blark/aiodnsbrute](https://github.com/blark/aiodnsbrute)                         | 403   | 0.3.2          | 4 jun 2019    | Python               |
| Resolution/BF | massdns           | [https://github.com/blechschmidt/massdns](https://github.com/blechschmidt/massdns)                   | 2,1K  | 1.0.0          | 11 nov 2021   | C                    |
| Resolution/BF | Amass             | [https://github.com/OWASP/Amass](https://github.com/OWASP/Amass)                                     | 6,1K  | 3.15.0         | 12 nov 2021   | Go                   |
| Resolution/BF | rusolver          | [https://github.com/Edu4rdSHL/rusolver](https://github.com/Edu4rdSHL/rusolver)                       | 136   | 0.7.0          | 20 oct 2021   | Rust                 |
| Wordlists     | altdns            | [https://github.com/infosec-au/altdns](https://github.com/infosec-au/altdns)                         | 1,6K  | –              | 10 sep 2021   | Python               |
| Wordlists     | dnscewl           | [https://github.com/codingo/DNSCewl](https://github.com/codingo/DNSCewl)                             | 213   | –              | 7 Jun 2021    | C++                  |
| Wordlists     | gotator           | [https://github.com/Josue87/gotator](https://github.com/Josue87/gotator)                             | 126   | 1.1            | 24 jul 2021   | Go                   |
| Wordlists     | dmut              | [https://github.com/bp0lr/dmut](https://github.com/bp0lr/dmut)                                       | 99    | –              | 5 jun 2021    | Go                   |
| Wordlists     | dnsgen            | [https://github.com/ProjectAnte/dnsgen](https://github.com/ProjectAnte/dnsgen)                       | 511   | 1.0.4          | 24 mar 2020   | Python               |

#### Passive results

| Tools with apis   | Apis added  | lazada.com Time | lazada.com Results | redmart.com Time | redmart.com Results | lazada.sg Time | lazada.sg Results | Notes                                       | Command                                                                                                                                                             |
| ----------------- | ----------- | --------------- | ------------------ | ---------------- | ------------------- | -------------- | ----------------- | ------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Amass             | 18          | 0:02:12         | 5251               | 0:02:15          | 5239                | 0:03:00        | 6610              |                                             | amass enum -passive -d domain.com                                                                                                                                   |
| Sublist3r         | –           | 0:00:11         | 143                | 0:00:11          | 69                  | 0:00:12        | 102               |                                             | python3 sublist3r.py -d domain.com \| tail -n +25                                                                                                                   |
| crobat            | –           | 0:00:01         | 37                 | 0:00:01          | 10                  | 0:00:01        | 22                |                                             | crobat -s domain.com                                                                                                                                                |
| chaos             | 1           | 0:00:01         | 81                 | 0:00:01          | 55                  | 0:00:01        | 70                |                                             | chaos -d domain.com -silent                                                                                                                                         |
| subfinder         | 15          | 0:00:41         | 5265               | 0:00:28          | 5110                | 0:00:30        | 5194              |                                             | subfinder -d domain.com -all -silent                                                                                                                                |
| assetfinder       | 3           | 0:00:48         | 241                | 0:00:05          | 160                 | 0:00:01        | 229               | Inconsistente en tiempo                     | assetfinder –subs-only domain.com                                                                                                                                   |
| waybackurls       | –           | 0:00:01         | 127                | 0:00:01          | 60                  | 0:00:23        | 26                |                                             | waybackurls domain.com \| unfurl -u domains                                                                                                                         |
| gau               | –           | 0:01:29         | 134                | 0:01:32          | 69                  | 0:05:01        | 43                | Inconsistente en tiempo y resultados (71m!) | gau –subs domain.com \| unfurl -u domains                                                                                                                           |
| github-subdomains | 1           | 0:00:10         | 144                | 0:00:15          | 72                  | 0:00:10        | 97                |                                             | github-subdomains -d domain.com -k -q -t .github\_tokens -o result.txt                                                                                              |
| findomain         | 4           | 0:00:06         | 246                | 0:00:02          | 181                 | 0:00:08        | 201               |                                             | findomain –quiet -t domain.com                                                                                                                                      |
| OneForAll         | 10          | 0:03:18         | 425                | 0:02:20          | 613                 | 0:01:42        | 155               |                                             | python3 oneforall.py –target domain.com –alive False –brute False –dns False –fmt json –path results/ run && cat results/domain.com.json \| jq ‘.\[] \| .subdomain’ |
|                   |             |                 |                    |                  |                     |                |                   |                                             |                                                                                                                                                                     |
| No APIS           | Custom Apis | lazada.com Time | lazada.com Results | redmart.com Time | redmart.com Results | lazada.sg Time | lazada.sg Results | Notes                                       | Command                                                                                                                                                             |
| Amass             | –           | 0:02:02         | 4233               | 0:02:58          | 253                 | 0:03:14        | 3938              |                                             | amass enum -passive -d domain.com                                                                                                                                   |
| Sublist3r         | –           |                 |                    |                  |                     |                |                   |                                             | python3 sublist3r.py -d domain.com \| tail -n +25                                                                                                                   |
| crobat            | –           |                 |                    |                  |                     |                |                   |                                             | crobat -s domain.com                                                                                                                                                |
| chaos             | –           |                 |                    |                  |                     |                |                   |                                             | chaos -d domain.com -silent                                                                                                                                         |
| subfinder         | –           | 0:00:24         | 249                | 0:00:06          | 115                 | 0:00:26        | 160               |                                             | subfinder -d domain.com -all -silent                                                                                                                                |
| assetfinder       | –           |                 |                    |                  |                     |                |                   | Inconsistente en tiempo                     | assetfinder –subs-only domain.com                                                                                                                                   |
| waybackurls       | –           |                 |                    |                  |                     |                |                   |                                             | waybackurls domain.com \| unfurl -u domains                                                                                                                         |
| gau               | –           |                 |                    |                  |                     |                |                   | Inconsistente en tiempo y resultados (71m!) | gau –subs domain.com \| unfurl -u domains                                                                                                                           |
| github-subdomains | –           |                 |                    |                  |                     |                |                   |                                             | github-subdomains -d domain.com -k -q -t .github\_tokens -o result.txt                                                                                              |
| findomain         | –           |                 |                    |                  |                     |                |                   |                                             | findomain –quiet -t domain.com                                                                                                                                      |
| OneForAll         | –           |                 |                    |                  |                     |                |                   |                                             | python3 oneforall.py –target domain.com –alive False –brute False –dns False –fmt json –path results/ run && cat results/domain.com.json \| jq ‘.\[] \| .subdomain’ |

## Feroxbuster sorting result example:

```
egrep "^200" feroxbuster.txt | tr -s " " | cut -d " " -f 6

```

*   **egrep** - regular expression to give only those urls which have 200 in the beginning of the line.\


    <figure><img src=".gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>


*   **tr** - trim the spaces\


    <figure><img src=".gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>


*   **cut** - give cut the delimeter of single space using -d " " and then it asks for the the filed number by -f 6\


    <figure><img src=".gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

## Sent the multiple urls to Burp or Zap using Command Line:&#x20;

```
set urls (egrep "^200" feroxbuster.txt | tr -s " " | cut -d " " -f 6)
```

it will set the `urls` variable which have urls stored

```
for u in $urls
    curl $u --proxy http://localhost:8080
end
```

it will send all the urls requests to the configured proxy via web browser.
