---
description: 'source: https://sechunter.medium.com/js-is-love-%EF%B8%8F-ca393a4849e9'
---

# 🔎 JS is l0ve ❤️.

Hello Everyone (Ram Ram Ji),

In this article, I will share one of my recent finding which is basically related to JS files. In which, i will tell you that how these JS files will help you to find a High Severity bugs.

If you are too lazy then I suggest you please use this tool i.e LinkFinder. It’s a tool to discover JS endpoints which is written by GerbenJavado. You can find it here, [LinkFinder](https://github.com/GerbenJavado/LinkFinder).

But, I personally prefer doing this stuff as manually. Because sometimes Automated tools don’t come out useful & they skipped out some important things. So, Let’s get Started.

## Phase 1: Extracting JS Files & Beautify them : <a href="#116b" id="116b"></a>

1. Extract JS Files from source code of redacted.com
2. Bash I used for extracting .js files using [**waybackurl**](https://github.com/tomnomnom/waybackurls):

> Extraction(){
>
> waybackurl $1| grep -iE “\\.js$” | uniq | sort
>
> }

3\. For Beautifying the JS Code I used [**js-beautify**](https://www.npmjs.com/package/js-beautify) for pretty print js code (Quite Easy).

> js-beautify \<file.js> | tee -a \<beautify.js>

You can install it from the given link below:

[js-beautifybeautifier.io for nodewww.npmjs.com](https://www.npmjs.com/package/js-beautify?source=post\_page-----ca393a4849e9--------------------------------)

## Phase 2: Searching for Patterns & Endpoints in JS files: <a href="#bd70" id="bd70"></a>

1. These are few patterns that i use while reading any JS file.

> {api\_key, api-key, apikey , api , access , AccessToken , gmap , gmaps , algolia\_api\_key , credentials, etc..}

2\. Mapping of Endpoints that you found while reading the JS file. **(Important one)**

3\. Most of the bug hunter’s skip the second point just because they can’t map out the correct endpoints which leads to high severity security bugs & loses their chances to getting High severity bugs.

> **NOTE:** Read every path and try to mapping that endpoints until you succeed. Because JS file contains all the solutions related to mapping of endpoints.

## Phase 3. Searching for Respective Documentation: <a href="#be7f" id="be7f"></a>

1. If you found any Rest-Api-Key then go for it’s Api Documentation (search on google about respective Api docs) & understand how it works & how you will use it to gain access of target system.
2. [**Keyhacks**](https://github.com/streaak/keyhacks) (This will help you for quick exploitation. Because this Repository contain’s 50+ Working Exploitable Endpoints of Different Rest-API-Keys).

> Now, lets take a look at my disclosed report where i found a HelpShift-Rest-Api-Key using that i can modified the content of accounts & also i can delete the accounts of redacted.com

## Steps to Reproduce (Acc. to Report): <a href="#91cd" id="91cd"></a>

### Finding API Key: <a href="#429c" id="429c"></a>

1. Go to “[https://redacted.com/dist/main.7be5c75d761008c183e6.min.js](https://redacted.com/dist/main.7be5c75d761008c183e6.min.js)".
2. Analyzing the JS Code & you will found base64 value in HelfShiftApiKey parameter.

<figure><img src="https://miro.medium.com/v2/resize:fit:875/1*tdmtjQJW4kqcepfl-cd0OA.png" alt="" height="63" width="700"><figcaption><p>HelpShift Rest API Key</p></figcaption></figure>

### Full Exploitation: <a href="#ab58" id="ab58"></a>

1. Go to “[https://apidocs.helpshift.com](https://apidocs.helpshift.com/)" & Enter Required details that is needed.
2. You got Swagger API & Successfully Logged In. Now do whatever you want to do.

Note: You can also use curl command :

> curl -X PUT — header ‘Content-Type: application/x-www-form-urlencoded’ — header ‘Accept: application/json’ — header ‘Authorization: Basic \<base64\_API\_Key>’ -d ‘username=\<username>’ ‘[https://api-a.helpshift.com/v1/](https://api-a.helpshift.com/v1/)\<domain\_name>/users/\<profile\_id>'

you can get all profile id’s from getUser query.

## Timeline: <a href="#82f2" id="82f2"></a>

<figure><img src="https://miro.medium.com/v2/resize:fit:875/1*teRF1ZmnCAq5ESsE6qqCsw.png" alt="" height="181" width="700"><figcaption><p>Bounty Rewarded</p></figcaption></figure>

Sept 26, 2020 — Reported to private program\
Sept 26, 2020 — Report Triaged\
Sept 29, 2020 — Vulnerability Fixed\
Oct 08, 2020 — Bounty of $5000 USD awarded

Special thanks to [nullr3x](https://twitter.com/nullr3x) for this (Big Bad Brother 🤑)
