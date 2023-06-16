# 🔼 Subdomain Takeover

<figure><img src=".gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

```
#!/bin/sh
filename=$1
while read url; do
    echo "Subfinder"
    subfinder -d $url -o $url.txt
    echo "Assetfinder"
    assetfinder --subs-only $url | tee -a $url.txt
    sort -u $url.txt -o $url.txt
    echo "Subjack"
    subjack -w $url.txt -o $url_takeover.txt
done < $filename
```
