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

if [ -z "$domain" ]; then
    echo "Usage: $0 <domain>"
    exit 1
fi

# Create the output directory if it doesn't exist
mkdir -p "$output_dir"

# AMASS
echo "[*] Running Amass..."
amass enum -d "$domain" -o "$output_dir/amass.txt"

# dnsenum (alternative to SubBrute)
echo "[*] Running dnsenum..."
dnsenum "$domain" > "$output_dir/dnsenum.txt"

# Knock
echo "[*] Running Knock..."
knockpy "$domain" -o "$output_dir/knock.txt"

# DNSRecon
echo "[*] Running DNSRecon..."
dnsrecon -d "$domain" -t brt -z -o "$output_dir/dnsrecon.txt"

# Sublist3r
echo "[*] Running Sublist3r..."
sublist3r -d "$domain" -o "$output_dir/sublist3r.txt"

# AltDNS
echo "[*] Running AltDNS..."
altdns -i "$output_dir/sublist3r.txt" -o "$output_dir/altdns.txt" -w /usr/share/seclists/Discovery/DNS/subdomains-top1mil-5000.txt -r -s "$output_dir/altdns_results.txt"

# Axiom
echo "[*] Running Axiom..."
axiom-scan -d "$domain" -o "$output_dir/axiom.txt"

# hakrawler (alternative to Haktrails)
echo "[*] Running hakrawler..."
hakrawler -url "$domain" -depth 2 -plain > "$output_dir/hakrawler.txt"

# assetfinder (alternative to Anubis)
echo "[*] Running assetfinder..."
assetfinder --subs-only "$domain" | tee "$output_dir/assetfinder.txt"

# subjs (alternative to Lepus)
echo "[*] Running subjs..."
subjs -c 50 -t 10 -timeout 5 -o "$output_dir/subjs.txt" -d "$domain"

# Subfinder
echo "[*] Running Subfinder..."
subfinder -d "$domain" -o "$output_dir/subfinder.txt"

# Amass Again for Comprehensive Results
echo "[*] Running Amass (Passive)..."
amass enum -passive -d "$domain" -o "$output_dir/amass_passive.txt"

# Aquatone (Screenshot Tool)
echo "[*] Running Aquatone..."
cat "$output_dir/amass.txt" | aquatone -out "$output_dir/aquatone" -chrome-path /usr/bin/google-chrome-stable

# httprobe
echo "[*] Running httprobe..."
cat "$output_dir/"*".txt" | grep -oE 'https?://[^[:space:]/?$.#].[^[:space:]]*' | sort -u | httprobe -c 50 | tee "$output_dir/httprobe.txt"

echo "[+] Subdomain enumeration completed. Results saved in '$output_dir' directory."

```
