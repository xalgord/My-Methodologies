# 🚀 Oneliners

{% @github-files/github-code-block url="https://github.com/dwisiswant0/awesome-oneliner-bugbounty" %}

{% @github-files/github-code-block url="https://github.com/0xPugazh/One-Liners" %}

{% @github-files/github-code-block url="https://github.com/Elsfa7-110/Elsfa7110-Oneliner-bughunting" %}

```bash
set domain "hoasted.com"; subfinder -d $domain -silent -all && assetfinder -subs-only $domain && findomain -t $domain -q | grep "\.$domain" | sort -u | httpx -t 170 -status-code -title -location -tech-detect -probe | grep -v "FAILED" | anew "$domain-live.txt" | awk '{print $1}' | nuclei -es info,low -t cves -cloud-upload
```
