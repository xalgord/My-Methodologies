# 🎯 subdomain-enumeration

```
$ amass enum -brute -passive -d target.com | httpx -silent -status-code | tee domain.txt
$ cat domain.txt | grep 200 | cut -d "[" -f1 > urls.txt
$ gf xss domain.txt | grep -Eo 'https?://[^\"]+' > domain.txt
```

#### Track new subdomains:

```bash
gungnir -r all-domains.txt | anew all-subs.txt | tee -a new-assets.txt | notify -id asset
```
