---
description: 'Source: pentest-tools.com'
---

# 🕶 Google Dorks

* Publicly Exposed Documents:

{% code overflow="wrap" %}
```
site:target.com ext:doc | ext:docx | ext:odt | ext:rtf | ext:sxw | ext:psw | ext:ppt | ext:pptx | ext:pps | ext:csv
```
{% endcode %}

* Directory Listing Vulnerabilities:

```
site:target.com intitle:index.of
```

* Configuration Files Exposed:

{% code overflow="wrap" %}
```
site:target.com ext:xml | ext:conf | ext:cnf | ext:reg | ext:inf | ext:rdp | ext:cfg | ext:txt | ext:ora | ext:ini | ext:env
```
{% endcode %}

* Database Files Exposed:

```
site:target.com ext:sql | ext:dbf | ext:mdb
```

* Log Files Exposed:

```
site:target.com ext:log
```

* Backups and Old Files:

```
site:target.com ext:bkf | ext:bkp | ext:bak | ext:old | ext:backup
```

* Login Pages:

{% code overflow="wrap" %}
```
site:target.com inurl:login | inurl:signin | intitle:Login | intitle:"sign in" | inurl:auth
```
{% endcode %}

* SQL Errors:

{% code overflow="wrap" %}
```
site:target.com intext:"sql syntax near" | intext:"syntax error has occurred" | intext:"incorrect syntax near" | intext:"unexpected end of SQL command" | intext:"Warning: mysql_connect()" | intext:"Warning: mysql_query()" | intext:"Warning: pg_connect()"
```
{% endcode %}

* PHP Errors / Warnings:

```
site:target.com "PHP Parse error" | "PHP Warning" | "PHP Error"
```

* phpinfo()

```
site:target.com ext:php intitle:phpinfo "published by the PHP Group"
```

* Pastebin and Pasting Sites:

{% code overflow="wrap" %}
```
site:pastebin.com | site:paste2.org | site:pastehtml.com | site:slexy.org | site:snipplr.com | site:snipt.net | site:textsnip.com | site:bitpaste.app | site:justpaste.it | site:heypasteit.com | site:hastebin.com | site:dpaste.org | site:dpaste.com | site:codepad.org | site:jsitor.com | site:codepen.io | site:jsfiddle.net | site:dotnetfiddle.net | site:phpfiddle.org | site:ide.geeksforgeeks.org | site:repl.it | site:ideone.com | site:paste.debian.net | site:paste.org | site:paste.org.ru | site:codebeautify.org  | site:codeshare.io | site:trello.com "target.com"
```
{% endcode %}

* Search Github and Gitlab:

```
site:github.com | site:gitlab.com "target.com"
```

* Search Stackoverflow:

```
site:stackoverflow.com "target.com"
```

* Signup Pages:

```
site:target.com inurl:signup | inurl:register | intitle:Signup
```

* Find Subdomains:

```
site:*.target.com
```

* Find Sub-Subdomains:

```
site:*.*.target.com
```

* Search in Waybackmachine:

```
https://web.archive.org/web/*/target.com/*
```

* Show only IP addresses (Opens Multiple Tabs):

{% code overflow="wrap" %}
```
(target.com) (site:*.*.29.* |site:*.*.28.* |site:*.*.27.* |site:*.*.26.* |site:*.*.25.* |site:*.*.24.* |site:*.*.23.* |site:*.*.22.* |site:*.*.21.* |site:*.*.20.* |site:*.*.19.* |site:*.*.18.* |site:*.*.17.* |site:*.*.16.* |site:*.*.15.* |site:*.*.14.* |site:*.*.13.* |site:*.*.12.* |site:*.*.11.* |site:*.*.10.* |site:*.*.9.* |site:*.*.8.* |site:*.*.7.* |site:*.*.6.* |site:*.*.5.* |site:*.*.4.* |site:*.*.3.* |site:*.*.2.* |site:*.*.1.* |site:*.*.0.*)
```
{% endcode %}
