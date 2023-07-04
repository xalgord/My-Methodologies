---
description: >-
  source:
  https://medium.com/@shubhamrooter/deep-subdomains-enumeration-methodology-da606be0c4c3
---

# 🔎 Deep-Subdomains-Enumeration-Methodology

Mastering the art of subdomain enumeration is a crucial skill for those seeking to unlock the full potential of web architecture. By systematically discovering and mapping subdomains, researchers, security professionals, and curious enthusiasts can gain valuable insights, unravel digital footprints, and fortify cyber defenses. The process entails employing various techniques and tools to navigate through the labyrinthine structures of the internet, connecting the dots that form the intricate tapestry of online presence.

## What’s the need? <a href="#d93d" id="d93d"></a>

* A good subdomain enumeration will help you find those hidden/untouched subdomains, resulting lesser people finding bugs on that particular domain. Hence, fewer **duplicates**.
* Finding applications running on hidden, forgotten (by the organization) sub-domains may lead to uncovering critical vulnerabilities.
* For large organizations, to find what services they have exposed to the internet while performing an internal pentest.
* The methodology of collecting subdomains from tools like `amass`, `subfinder`, `findomain` and directly sending them to httpx/httprobe is **absolutely wrong**. Instead, you should first DNS resolve them using tools like [puredns](https://github.com/d3mondev/puredns) or [shuffledns](https://github.com/projectdiscovery/shuffledns).

## Enumeration <a href="#5ae3" id="5ae3"></a>

1. Discovering the IP space
2. Discovering the IP space: This step involves obtaining the Autonomous System Number (ASN) for a target domain from a website like “[https://bgp.he.net/](https://bgp.he.net/)" and finding the IP ranges associated with that ASN using the `whois` command. The IP ranges are then saved to a file.

```
# get the ASN from websites like 
https://bgp.he.net/ 

# find out the IP ranges that reside inside that ASN
apt-get install whois
whois -h whois.radb.net  -- '-i origin AS8983' | grep -Eo "([0-9.]+){4}/[0-9]+" | uniq -u > ip_ranges.txt
```

2\. PTR records (Reverse DNS)

In this step, the IP ranges obtained in the previous step are fed into the `dnsx` tool with the `-ptr` option to perform reverse DNS lookups and retrieve PTR records. The results are saved to a file.

```
cat ip_anges.txt | mapcidr -silent | dnsx -ptr -resp-only -o ptr_recrds.txt
```

3\. Favicon Search

A Python script called `favfreak.py` is used to search for favicons associated with the subdomains. The script takes a list of URLs as input and outputs the favicon hash for each domain.

```
cat urls.txt | python3 favfreak.py -o output
http.favicon.hash:-<hash>
```

4\. Finding related domains/acquisitions

This step involves using various sources, such as Google, Wikipedia, and the “[https://tools.whoisxmlapi.com/reverse-whois-search](https://tools.whoisxmlapi.com/reverse-whois-search)" website, to discover related domains or acquisitions associated with the target domain.

* use **CHATGPT,** Google, wikipedia,

## Vertical Enumeration <a href="#1a2d" id="1a2d"></a>

## Passive Enum <a href="#3df4" id="3df4"></a>

1. **Subfinder** \[ `subfinder -d test.com -o passive2.txt -all` ]

* It is important to add APIs to the config file to get better results. (Go see the detailed blog if u didn’t get it)
* **Internet Archive →** [district →](https://github.com/lc/gau) [waybackurls](https://github.com/tomnomnom/waybackurls)
* **Github Scraping →** [github-subdomains](https://github.com/gwen001/github-subdomains)
* **GitLab Scraping →** [gitlab-subdomains](https://github.com/gwen001/gitlab-subdomains)
* [https://chaos.projectdiscovery.io/#/](https://chaos.projectdiscovery.io/#/) → it is like database or something here u can get all subdomains for public bug bounty programs, yeah it is useless when you work in a private one.

## Active Enum <a href="#bc97" id="bc97"></a>

1. DNS Brute Forcing \[ using puredns]

DNS brute-forcing is performed using the `puredns` tool. This involves setting up prerequisites by installing `massdns` and `puredns`, downloading resolvers and DNS wordlists, and then using `puredns` to brute-force subdomains.

```
#Prerequisites
git clone https://github.com/blechschmidt/massdns.git
cd massdns
make
sudo make install

#Installing the tool
go install github.com/d3mondev/puredns/v2@latest

# Download Resolvers List
wget https://raw.githubusercontent.com/trickest/resolvers/main/resolvers-trusted.txt

# You even can make yours
git clone https://github.com/vortexau/dnsvalidator.git
cd dnsvalidator/
pip3 install -r requirements.txt
pip3  install setuptools==58.2.0
python3 setup.py install
dnsvalidator -tL https://public-dns.info/nameservers.txt -threads 100 -o resolvers.txt

# Download dns wordlist  
wget https://wordlists-cdn.assetnote.io/data/manual/best-dns-wordlist.txt 

# Brute Forcing
puredns bruteforce best-dns-wordlist.txt example.com -r resolvers.txt -w dns_bf.txt
```

2\. Permutations

Permutation techniques are used to generate variations of subdomains. Wordlists are used with the `gotator` tool to create permutations, which are then resolved using `puredns`.

```
# Permutation words Wordlist
wget https://gist.githubusercontent.com/six2dez/ffc2b14d283e8f8eff6ac83e20a3c4b4/raw
# Run 
gotator -sub subdomains.txt -perm dns_permutations_list.txt -depth 1 -numbers 10 -mindup -adv -md | sort -u > perms.txt
# DNS resolve them and check for valid ones.
puredns resolve permutations.txt -r resolvers.txt > resolved_perms
# Hint: Collect subdomains that is not valid and make compinations then resolve them u may git valid unique subdomains that is hard to find 
gotator -sub not_vali_subs.txt -perm dns_permutations_list.txt -depth 1 -numbers 10 -mindup -adv -md | sort -u > perms.txt
```

3\. Google Analytics

The `AnalyticsRelationships` tool is used to find subdomains associated with a target domain based on Google Analytics tracking codes.

```
git clone https://github.com/Josue87/AnalyticsRelationships.git
 cd AnalyticsRelationships/Python
 sudo pip3 install -r requirements.txt
 python3 analyticsrelationships.py -u https://www.example.com
```

4\. **TLS, CSP, CNAME Probing**

The `cero` tool is used for TLS, CSP, and CNAME probing to gather additional subdomain information.

```
go install github.com/glebarez/cero@latest
  #tls
  cero in.search.yahoo.com | sed 's/^*.//' | grep -e "\." | sort -u
  #cls
  cat subdomains.txt | httpx -csp-probe -status-code -retries 2 -no-color | anew csp_probed.txt | cut -d ' ' -f1 | unfurl -u domains | anew -q csp_subdomains.txt
  # cname
  dnsx -retry 3 -cname -l subdomains.txt
```

5\. **Scraping(JS/Source code)**

: Subdomains are probed using the `httpx` tool, and the obtained URLs are then fed into `gospider` for web crawling. The output is cleaned and filtered to obtain the scraped subdomains.

```
# Web probing subdomains
cat subdomains.txt | httpx -random-agent -retries 2 -no-color -o probed_tmp_scrap.txt

# Now, that we have web probed URLs, we can send them for crawling to gospider 
gospider -S probed_tmp_scrap.txt --js -t 50 -d 3 --sitemap --robots -w -r > gospider.txt

#Cleaning the output 
sed -i '/^.\{2048\}./d' gospider.txt
cat gospider.txt | grep -Eo 'https?://[^ ]+' | sed 's/]$//' | unfurl -u domains | grep ".example.com$" | sort -u scrap_subs.txt

# Resolving our target subdomains
 puredns resolve scrap_subs.txt -w scrap_subs_resolved.txt -r resolvers.txt
```

## Recursive Enumeration <a href="#799d" id="799d"></a>

This step involves performing recursive enumeration by iterating over the subdomains and using tools like `subfinder`, `assetfinder`, `amass`, and `findomain` to discover additional subdomains.

```
#!/bin/bash

go install -v github.com/tomnomnom/anew@latest
subdomain_list="subdomains.txt"

for sub in $( ( cat $subdomain_list | rev | cut -d '.' -f 3,2,1 | rev | sort | uniq -c | sort -nr | grep -v '1 ' | head -n 10 && cat subdomains.txt | rev | cut -d '.' -f 4,3,2,1 | rev | sort | uniq -c | sort -nr | grep -v '1 ' | head -n 10 ) | sed -e 's/^[[:space:]]*//' | cut -d ' ' -f 2);do 
    subfinder -d $sub -silent -max-time 2 | anew -q passive_recursive.txt
    assetfinder --subs-only $sub | anew -q passive_recursive.txt
    amass enum -timeout 2 -passive -d $sub | anew -q passive_recursive.txt
    findomain --quiet -t $sub | anew -q passive_recursive.txt
done
```

## Finish Work <a href="#e494" id="e494"></a>

Finally, the obtained subdomains from different steps (horizontal and vertical enumeration) are consolidated and filtered using the `httpx` tool.

```
cd subs/
cat horizontal/ptr_records.txt | sort -u > horizontal.txt
cat Vertical/Active/* | sort -u > active.txt
cat Vertical/Pssive/* | sort -u > passive.txt
cat * | sort -u > all_subs.txt
cat all_subs.txt | httpx -random-agent -retries 2 -no-color -o filtered_subs.txt
```
