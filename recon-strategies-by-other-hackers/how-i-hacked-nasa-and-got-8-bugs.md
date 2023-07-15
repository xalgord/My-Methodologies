---
description: >-
  source:
  https://medium.com/@shari7a0x/how-i-hacked-nasa-and-get-8-bugs-e5cd397a6af9
---

# 🔎 How I hacked NASA and got 8 bugs ?

<figure><img src="https://miro.medium.com/v2/resize:fit:763/1*U1AmLMimGnezCeiobo0V0w.jpeg" alt="" height="212" width="700"><figcaption></figcaption></figure>

Hi hackers ,

I will explain , How did i get 8 bugs in NASA .

It’s about 7 cross site scripting (xss) and one open redirect .

First step collect subdomains and check live domains .

I always use tool [**subfinder**](https://github.com/projectdiscovery/subfinder) and add to it some API’s .

I always use tool **HTTPX** .

```
subfinder -d host.com -silent | httpx -mc 200 -o live txt 
```

second step collecting parameters .

I always use two tools [**paramspider**](https://github.com/devanshbatham/ParamSpider) and [**waybackurls**](https://github.com/tomnomnom/waybackurls) .

wait , are you real to collect parameters form domains by yourself ?

it’s many subdomains and paraspider can’t automate this alone .

I use simple bash script ,to automate collect parameters from subdomains .

```
for URL in $(</path-of-live-domains  ); do (python3 paramspider.py -d "${URL}" --level high  );done
```

> little not before run this script you should open directory of Paramspider .

**what after this ?**

You want to check parameters if you can add (tags <>) .or not

I used [**KXSS**](https://github.com/Emoe/kxss) **,** it’s very nice tool but not work at all the time .

```
Cat parameters.txt |kxss
```

<figure><img src="https://miro.medium.com/v2/resize:fit:763/1*hTxuiWhRzZUxqMiajqW4XQ.png" alt="" height="34" width="700"><figcaption><p>done .</p></figcaption></figure>

what after this ?

<figure><img src="https://miro.medium.com/v2/resize:fit:550/1*8fPNlNJT2oXqssvrB5aXQw.png" alt="" height="38" width="505"><figcaption><p>I can write html code .</p></figcaption></figure>

You can use tool for discovering hidden parameters also like [**Arjun**](https://github.com/s0md3v/Arjun) and check parameters if it work, you can use tool like [**dalfox**](https://github.com/hahwul/dalfox) very cool tool .

```
Arjun -u host.com
```

```
Dalfox url host.com?parameters=xss
```

<figure><img src="https://miro.medium.com/v2/resize:fit:763/1*UZelUPLVQUMc0M7RBKfrFQ.jpeg" alt="" height="424" width="700"><figcaption><p>boom xss is done .</p></figcaption></figure>

You can use automation by [**NUCL**](https://github.com/projectdiscovery/nuclei)**EI .** [**templates**](https://github.com/projectdiscovery/fuzzing-templates) **.**

```
Nuclei -l parameters -t /fuzzing-templates/xss
```

I get open redirect by NUCLEI .

```
cat parameters.txt | grep "redirect" | NUCLEI -t /fuzzing-templates/redirect/open-redirect.yaml
```

if you think i finish you are wrong .

<figure><img src="https://miro.medium.com/v2/resize:fit:241/1*IceXrSdcLUB7OSgCe4je4g.jpeg" alt="" height="229" width="221"><figcaption><p>lol .</p></figcaption></figure>

but that are two bug only , where are others ?

after i got first xss i had an idea , i can use some google dorks .

google dorks for xss !!!! yep .

some google dorks for discovering parameters

site:\*.host.com ext:asp

site:\*.host.com ext:jsp

site:\*.host.com ext:aspx

site:\*.host.com ext:jspx

site:\*.host.com ext:do

site:\*.host.com ext:action

I use them but didn't have useful parameters .

I check subdomain if i can use it to search about it in google .

I found something doesn’t see it usually it’s **`index.cgi .`**

I asked myself why didn’t try to use google dorks about it .

site: \*.host.com ext:cgi

site:\*.nasa.gov inurl:index.cgi

I see good result , i checked this result it’s like last domain ,that i had found xss in it ,I tried to use same parameter lol it is working nice .

I checked about 11 domains but 7 had xss .

<figure><img src="https://miro.medium.com/v2/resize:fit:763/1*tUm0Xs8Kmgup0NbRpH-0MQ.jpeg" alt="" height="536" width="700"><figcaption></figcaption></figure>

thinks for reading .

give me feedback .

can you follow me in [**linked in**](https://www.linkedin.com/in/shai7a0x/) and [**twitter**](https://twitter.com/Shari7a0X)
