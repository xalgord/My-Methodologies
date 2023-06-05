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
