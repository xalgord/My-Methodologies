---
description: >-
  source:
  https://medium.com/@h4x0r_dz/the-right-way-to-get-unique-subdomains-on-large-scope-899f834e702c
---

# 🔎 How to Get Unique Subdomains on Large scope

**how to Get The Uniq Subdomains on a big Subdomains list. to print unique ones only**

this writeup will be a short one forry about that xD, during My hunting on big companies such as Facebook, google, amazon …etc, and In the subdomain enumeration step, I found that I get so many same subdomains.

for example :

```
https://test.facebook.com 
https://test02.facebook.com
https://test03.facebook.com
```

these subdomains have the same web application, and it is a long journey to visit and check all these subdomains one by one, (and it worth nothing ).

so instead we can use a trick to filter similar subdomains. and print only unique one , this can be done using [httpx ](https://github.com/projectdiscovery/httpx)and awk built-in tool

I’m going to use twitter.com as an example :

## subdomain enumeration <a href="#9dcd" id="9dcd"></a>

the first step as expected is to extract the subdomains, we are going to enumerate all the subdomains and save them in a Text file.

you can use your favorite for subdomain enumeration, and save the result in text file :

<figure><img src="https://miro.medium.com/v2/resize:fit:768/1*bDBwqhimdNqUU9nMrRuTyg.png" alt="" height="755" width="614"><figcaption></figcaption></figure>

### httpx to enumerate live subdomains <a href="#5c0d" id="5c0d"></a>

now we are going to use httpx tool with these options :

### now we are going to use httpx tool with these options : <a href="#335f" id="335f"></a>

```
httpx -l subdomains.txt -sc -title -cl -wc -td | tee httpx2.txt
```

**-l subdomains.txt**: This flag specifies the input source for subdomains.\
**-sc**: This flag likely stands for “status code.”\
**-title**: This flag instructs the tool to extract and display the titles\
**-cl**: This flag stands for “content length.”\
**-wc**: This flag stands for “word count.”\
**-td**: display technology in use based on wappalyzer dataset\
**| tee httpx2.txt**: This part of the command is using the tee command, which is used to capture the standard output (stdout) of the preceding command and write it to a file. In this case, the output of the httpx command is being captured and written to a file named httpx2.txt.

<figure><img src="https://miro.medium.com/v2/resize:fit:873/1*hTsYe29KsvW0Hudh9Tpuhw.png" alt="" height="765" width="698"><figcaption></figcaption></figure>

as you can see, there are so many subdomains has the same HTTP status code & Title & content length.

### awk to filter the duplicate subdomains <a href="#3fa2" id="3fa2"></a>

now after getting the (maybe the same web application on different subdomains ) on our text list , we need to filter theme and print only the unique ones and doing our pen-testing on them . to do this we need to use a tool called awk

to do this we need to use a tool called awk

we need to filter the list of subdomains based on **status code** & co**ntent length** ….

full command

```
└─# cat httpx2.txt | awk -F"[" '!seen[$2, $3, $4, $5]++'
```

<figure><img src="https://miro.medium.com/v2/resize:fit:875/1*sGFWiYGip-zItgfSqQ-9_w.png" alt="" height="123" width="700"><figcaption></figcaption></figure>

* **F’\[‘:** This flag sets the field separator for awk as an opening square bracket \[. This means that awk will split input lines into fields based on the specified separator.

**-F’\[‘:** This flag sets the field separator for awk as an opening square bracket \[. This means that awk will split input lines into fields based on the specified separator.

**‘!seen\[$2, $3, $4, $5]++’:** This is the awk script that defines the processing to be applied to each input line.

this method is not working properly, but it still works fine on the large scope for me xD.
