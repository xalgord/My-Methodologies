# 🔎 Static Analysis of Client-Side JavaScript for pen testers and bug bounty hunters

JavaScript has become one of the most ubiquitous technologies in the modern web browsers. Applications built using client-side JavaScript frameworks such as AngularJS, ReactJS and Vue.js push a lot of functionality and logic to the front-end.

> With the increased functionality/logic on the client-side, the attack surface on the client-side also increases.

As a security tester it is essential for us to understand the attack surface of these applications. It is also important for us to know what information to look for, where to look for and how to look for the information that will lead to the discovery of potential security issues in the applications.

In this blog post we will look at performing static analysis of client-side JavaScript code in order to uncover potential security issues in the applications.

> In this blog post we are particularly interested in performing static analysis to uncover security issues. We will not delve into performance analysis or functional testing.

<figure><img src="https://miro.medium.com/v2/resize:fit:1250/1*Jm1f5iF-jXygKoOY9_HJaA.png" alt="" height="180" width="1000"><figcaption><p>Static analysis is analysing code without executing it.</p></figcaption></figure>

## What to look for? <a href="#id-73ce" id="id-73ce"></a>

As a penetration tester, when we are performing static analysis of client-side JavaScript, we are more or less interested in the information that will fall under following categories —

1. Information that’ll increase the attack surface (URLs, domains etc)
2. Sensitive information (Passwords, API keys, Storage etc)
3. Potentially dangerous areas in code(eval, dangerouslySetInnerHTML etc)
4. Components with known vulnerabilities (Outdated frameworks etc)

## Steps to perform static analysis <a href="#id-96ce" id="id-96ce"></a>

We’ll perform static analysis by breaking it down into following steps:

1\. Identifying and gathering JavaScript files in an application\
2\. Making the gathered JavaScript code readable (Unminify/Deobfuscate)\
3\. Identifying information that may lead to discovery of security issues

## Gathering JavaScript files <a href="#id-0efa" id="id-0efa"></a>

1.If you use Burp Suite for testing applications then there are multiple ways to gather all the JavaScript files in an application.

> At Appsecco, We follow user-driven workflow for testing web applications where we start testing by navigating the entirety of the application like an end user would do.

Navigate through an application while the traffic is being sent through Burp proxy. Once you are done with the navigation, you can use Burp’s tool-set to extract all the JavaScript files.

* If you are using Burp Suite Community Edition, you can navigate to `proxy > HTTP history` and use the display filters to only display the JavaScript files used by the application. You can also copy the URLs for all the JavaScript files displayed.

<figure><img src="https://miro.medium.com/v2/resize:fit:875/1*8YLktaI23nHogmLT175ywg.png" alt="" height="317" width="700"><figcaption><p>Burp display filters to display only JavaScript files for a given application</p></figcaption></figure>

<figure><img src="https://miro.medium.com/v2/resize:fit:1250/1*2AzEsdFW7ymvijuZV593Bg.png" alt="" height="446" width="1000"><figcaption><p>Copy the URLs for all the JavaScript file displayed after filtering</p></figcaption></figure>

* If you are using Burp Suite Professional, You can not only copy the URLs for all the JavaScript files in an application but also export all the scripts. Under `Target > Site map` right click on the site of interest and select `Engagement tools > Find scripts` . Using this feature you can export all the scripts in that application and also copy URLs.

<figure><img src="https://miro.medium.com/v2/resize:fit:875/1*Asd2jV3vCOX55BzZLirdMA.png" alt="" height="352" width="700"><figcaption><p>Burp “Find Scripts” to identify all the JS files on an application</p></figcaption></figure>

<figure><img src="https://miro.medium.com/v2/resize:fit:875/1*q27kCqV73EHVah3T-uwZRA.png" alt="" height="344" width="700"><figcaption><p>Burp “Find scripts” can export all the scripts, not just URLs</p></figcaption></figure>

2.Another interesting technique to quickly list JavaScript files in an application is to dig through internet archive databases such as [Wayback Machine](https://archive.org/web/). This technique is completely passive because we are not sending any request to the target application server.

> _Digging through internet archives such as_ [Wayback Machine](https://archive.org/web/) _is quite useful in identifying JavaScript files in an application. Sometimes you’ll be able to find older JavaScript files that are not deleted on the server._

* [waybackurls](https://github.com/tomnomnom/waybackurls/) is a neat tool to search for JavaScript files(or any other URLs for that matter) that belong to a site of interest using [Wayback Machine](https://archive.org/web/)

```
go get github.com/tomnomnom/waybackurls
waybackurls internet.org | grep "\.js" | uniq | sort
```

<figure><img src="https://miro.medium.com/v2/resize:fit:875/1*2U4rw-JOF5sFEWyEsJdPxQ.png" alt="" height="380" width="700"><figcaption><p>Using “waybackurls” to extract URLs for JavaScript files that belong to a domain that are listed in Wayback Machine archive</p></figcaption></figure>

* Using [Wayback Machine](https://archive.org/web/) might result in false-positives i.e. JavaScript files that don’t exist on the server anymore. Once you have gathered the list of URLs for JavaScript files, you can use [curl](https://ec.haxx.se/usingcurl.html) to quickly check for the status of the JavaScript files on the server

<pre><code><strong>cat js_files_url_list.txt | parallel -j50 -q curl -w 'Status:%{http_code}\t Size:%{size_download}\t %{url_effective}\n' -o /dev/null -sk
</strong></code></pre>

<figure><img src="https://miro.medium.com/v2/resize:fit:1250/1*3ThluG1AInGCFLaJPfnjjQ.png" alt="" height="269" width="1000"><figcaption><p>Using <a href="https://ec.haxx.se/usingcurl.html">cURL</a> to quickly check for the status of the JavaScript files on the server</p></figcaption></figure>

* Using curl to check for the status of the JavaScript files on the server could be cumbersome. There are utilities that make the process of finding the status of files straight forward. [hakcheckurl](https://github.com/hakluke/hakcheckurl) is one such utility written in Golang that I use often for it’s simplicity and speed.

```
go get github.com/hakluke/hakcheckurl
cat lyftgalactic-js-urls.txt | hakcheckurl
```

<figure><img src="https://miro.medium.com/v2/resize:fit:875/1*QZdYdFsEmOBBzd3SiMtMJg.png" alt="" height="302" width="700"><figcaption><p>Using hakcheckurl to find status of files on the server</p></figcaption></figure>

## Making the gathered JavaScript code readable <a href="#d29d" id="d29d"></a>

Sometimes the JavaScript files you gathered might not be immediately intelligible or readable. This can happen because the developer might have minified or obfuscated the JavaScript code.

> [Minification](https://developers.google.com/speed/docs/insights/MinifyResources) refers to the process of removing unnecessary or redundant data without affecting how the resource is processed by the browser — e.g. code comments and formatting, removing unused code, using shorter variable and function names, and so on.
>
> Obfuscation involves making modifications to the program, changing the names of variables, functions, and members, making the program much harder to understand.

* There are various tools that can minify JavaScript. [UglifyJS](https://github.com/mishoo/UglifyJS) is a neat tool to minify JS code and it also available as npm package

<figure><img src="https://miro.medium.com/v2/resize:fit:875/1*CYJ2_yGXWT91kxoGTMdgRQ.png" alt="" height="197" width="700"><figcaption><p>Minify JavaScript using <a href="https://github.com/mishoo/UglifyJS2">UglifyJS</a></p></figcaption></figure>

* There are various tools that can un-minify JavaScript. [JS Beautifier](https://github.com/beautify-web/js-beautify) is a tool that can beautify and also deobfuscate certain obfuscation schemes. You can use this tool via [node.js](https://www.npmjs.com/package/js-beautify), [python](https://pypi.org/project/jsbeautifier/), [online](https://beautifier.io/) or in a code editor like [VS Code](https://github.com/HookyQR/VSCodeBeautify).

<figure><img src="https://miro.medium.com/v2/resize:fit:875/1*Yk_g9ivV9SipOqVftp5DCw.png" alt="" height="220" width="700"><figcaption><p>Unminify JavaScript using <a href="https://github.com/beautify-web/js-beautify">JS Beautifier</a></p></figcaption></figure>

When performing deobfuscation, especially when reversing malware there is no one size fits all technique or tool. You’ll have to try various tools, deobfuscation schemes and perform some manual analysis. There are various tools out there that can help you deobfuscate JavaScript code. Some of the tools we often use are [JStillery](https://github.com/mindedsecurity/JStillery), [JSDetox](http://relentless-coding.org/projects/jsdetox), [JS-Beautifier](https://github.com/einars/js-beautify), [IlluminateJs](https://github.com/geeksonsecurity/illuminatejs), [JSNice](http://www.jsnice.org/) etc.

## Identifying interesting information in JavaScript <a href="#dfd7" id="dfd7"></a>

1.One of the key information to look for in JavaScript files is endpoints i.e full URLs, relative paths etc.

> Identifying full URLs, relative paths in JavaScript files will help us uncover the attack surface and potentially find more vulnerabilities

* [relative-url-extractor](https://github.com/jobertabma/relative-url-extractor) by [Jobert Abma](https://github.com/jobertabma/) is quite handy to quickly identify all the relative paths in a JavaScript file. This tool can work on both local and remote JavaScript files. This tool can work directly on minified JavaScript

<figure><img src="https://miro.medium.com/v2/resize:fit:1250/1*Z7uNAw139KwYkCcq6HhvAw.png" alt="" height="412" width="1000"><figcaption><p>Using <a href="https://github.com/jobertabma/relative-url-extractor">relative-url-extractor</a> to extract relative paths from remote JS file</p></figcaption></figure>

* [LinkFinder](https://github.com/GerbenJavado/LinkFinder) by [Gerben Javado](https://github.com/GerbenJavado) is quite handy in identifying all the endpoints and their parameters in JavaScript files. This tool can also work directly on minified JavaScript and it uses `jsbeautifier` to unminify JavaScript. This tool can also be run against a domain to enumerate all the JavaScript files

```
python linkfinder.py -i xample.com -d -o cli
```

<figure><img src="https://miro.medium.com/v2/resize:fit:1250/1*MJ6LXXjIUwE0T-mdrScLQw.png" alt="" height="398" width="1000"><figcaption><p>Using <a href="https://github.com/GerbenJavado/LinkFinder">LinkFinder</a> to extract endpoints from all the JS files in an application</p></figcaption></figure>

2.JavaScript files might also contain sensitive information such as credentials and API keys. Broadly speaking, we can identify secrets in source code files using either regex or entropy. Regex search will be able to identify credentials that are set by users such as usernames and passwords. Entropy based search is effective in identifying sufficiently random secrets such as API keys and tokens.

* [DumpsterDiver](https://github.com/securing/DumpsterDiver)[, ](https://github.com/dxa4481/truffleHog)[Repo-supervisor](https://github.com/auth0/repo-supervisor#repo-supervisor) [and truffleHog](https://github.com/dxa4481/truffleHog) are some fantastic tools to search for secrets in source code files. Most of these tools support both entropy based and regex based search. They are also easily and highly customisable both in regex search and entropy search.

<figure><img src="https://miro.medium.com/v2/resize:fit:1250/1*Ibf1jeE2kpgjPIzMcJ0tnw.png" alt="" height="236" width="1000"><figcaption></figcaption></figure>

* Don’t forget that _grep/sed/awk_ are also quite powerful when searching for specific sensitive information the source code files.

3.When looking into JavaScript files, it is important to identify dangerous places in code i.e. places where developers tend to make mistakes that will lead to potential security issues.

* Usage of `innerHTML` indicates that there might be possible XSS issue. In the modern client-side JavaScript frameworks `innerHTML` equivalents do exist such as the aptly named `dangerouslytSetInnerHTML` in React framework and they did result in [serious security vulnerabilities in the past](https://thehackerblog.com/i-too-like-to-live-dangerously-accidentally-finding-rce-in-signal-desktop-via-html-injection-in-quoted-replies/index.html) . Improper usage of `bypassSecurityTrustX` methods in Angular can also lead to XSS issues. `eval` function is another place where things can go wrong both on client-side and server side.

<figure><img src="https://miro.medium.com/v2/resize:fit:875/1*VJLKbFxI4bjk-JQ8oyOqpQ.png" alt="" height="224" width="700"><figcaption><p><code>List of bypassSecurityTrustX</code> methods in Angular</p></figcaption></figure>

* [_postMessage_](https://developer.mozilla.org/en-US/docs/Web/API/Window/postMessage) API is an alternative to JSONP, XHR with CORS headers and other methods enabling sending data between origins by bypassing Same Origin Policy(SOP). The idea of bypassing SOP and communicating with different origin should be of interest to attackers. There are various [security pitfalls](https://labs.detectify.com/2016/12/08/the-pitfalls-of-postmessage/) when using postMessage. Once you understand the possible security issues associated with postMessage, you can look for the implementation in JavaScript files. On the message sender side, look for `window.postMessage` and on the receiver end look for a listener `window.addEventListener` . You’ll have to keep in mind that a lot of frameworks implement wrappers around postMessage
* localStorage and sessionStorage are HTML Web Storage Objects. With web storage, web applications can store data locally within the user’s browser. It is important to identify what is being stored using the Web Storage especially storing anything sensitive can lead to potential security issues. In the JavaScript, you can look for `window.localStorage` and `window.sessionStorage`.

Looking for dangerous places in JavaScript that might lead to potential security issues is highly dependent on stack that is being used. You should identify what frameworks are being used, identify dangerous functions in the framework and then look for them in the source code.

5.Using security linters and static security scanners will make it easy to identify low hanging vulnerabilities in JavaScript code. [JSPrime](https://github.com/dpnishant/jsprime) is a static analysis tool for finding security issues in JavaScript code but the project hasn’t been updated in a while. [ESLint](https://github.com/eslint/eslint) is one of the most popular JavaScript linters. ESLint is easily customisable by adding custom rules. A lot of custom [security rules](https://github.com/LewisArdern/eslint-plugin-angularjs-security-rules) are available for ESLint especially targeting modern frameworks like Angular, React etc.

<figure><img src="https://miro.medium.com/v2/resize:fit:1250/1*7_KdLVGWUWylX6CoHHignQ.png" alt="" height="249" width="1000"><figcaption><p>ESLint with Angular security rules reporting potential security issues</p></figcaption></figure>

6.When performing security assessments, it is important to identify old and vulnerable JavaScript frameworks/libraries being used in the application. [Retire.js](https://retirejs.github.io/retire.js/) is a tool that can identify outdated JavaScript frameworks in use. This tool can be used as a stand alone tool, browser extension, grunt plugin or Burp/ZAP extension. RetireJS Burp extension is especially useful if you are performing your testing in a user-driven manner. Although RetireJS can report some false positives and not everything reported by RetireJS is vulnerable

<figure><img src="https://miro.medium.com/v2/resize:fit:1250/1*yQ9ZE5hqtryY_wCYcoiQSQ.png" alt="" height="249" width="1000"><figcaption><p>RetireJS Burp suite extension reporting an outdated JavaScript library</p></figcaption></figure>

## Conclusion <a href="#id-9b38" id="id-9b38"></a>

In this blog post we have covered a generic approach to perform static-analysis of client-side JavaScript code. What we have covered is just one workflow that will take you from gathering JavaScript files to finding security issues using the JavaScript code.

This brings us to the end of this post. If there are static analysis techniques, tools or tricks that you frequently use that have yielded you interesting results and if you would like to share those, please do leave a comment.

Until next time, happy bug hunting!!

## References <a href="#ba2d" id="ba2d"></a>

* [Performing JavaScript Static Analysis by Lewis Ardern](https://www.youtube.com/watch?v=mGUsCAWwLGg\&t=2601s) \[Video]
* [https://statuscode.ch/2015/05/static-javascript-analysis-with-burp/](https://statuscode.ch/2015/05/static-javascript-analysis-with-burp/)
* [http://blog.blueclosure.com/2017/10/javascript-dangerous-functions-part-2\_29.html](http://blog.blueclosure.com/2017/10/javascript-dangerous-functions-part-2\_29.html)
* [https://reverseengineering.stackexchange.com/questions/4561/how-to-deobfuscate-an-obfuscated-javascript-file-like-this](https://reverseengineering.stackexchange.com/questions/4561/how-to-deobfuscate-an-obfuscated-javascript-file-like-this)
* [https://labs.detectify.com/2016/12/08/the-pitfalls-of-postmessage/](https://labs.detectify.com/2016/12/08/the-pitfalls-of-postmessage/)
* [https://angular.io/guide/security](https://angular.io/guide/security)
* [https://blog.jse.li/posts/marveloptics-malware/](https://blog.jse.li/posts/marveloptics-malware/)

_At_ [_Appsecco_](http://appsecco.com/) _we provide advice, testing, training and insight around software and website security, especially anything that’s online, and its associated hosting infrastructure — Websites, e-commerce sites, online platforms, mobile technology, web-based services etc._
