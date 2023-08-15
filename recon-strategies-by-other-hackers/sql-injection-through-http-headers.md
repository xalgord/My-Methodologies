---
description: >-
  source:
  https://resources.infosecinstitute.com/topics/application-security/sql-injection-http-headers/
---

# 🔎 SQL injection through HTTP headers

During vulnerability assessment or penetration testing, identifying the input vectors of the target application is a primordial step. Sometimes, when dealing with Web application testing, verification routines related to [SQL injection](https://www.infosecinstitute.com/skills/courses/sql-injection/?utm\_source=resources\&utm\_medium=infosec+network\&utm\_campaign=skills+pricing\&utm\_content=hyperlink) flaws discovery are restricted to the GET and POST variables as the unique inputs vectors ever.

What about other HTTP header parameters? Aren't they potential input vectors for SQL injection attacks? How can one test all these HTTP parameters and which vulnerability scanners to use in order to avoid leaving vulnerabilities undiscovered in parts of the application?

![11 courses, 8+ hours of training](https://resources.infosecinstitute.com/contentassets/268131c75b0249f5bd1b3b2d7d3b3a02/hackable-book-by-ted-harrington.jpg)

11 courses, 8+ hours of training

Learn cybersecurity from Ted Harrington, the #1 best-selling author of "Hackable: How to Do Application Security Right."[See Courses](https://app.infosecinstitute.com/portal/skills/path/3977/?utm\_source=resources\&utm\_medium=infosec%20network\&utm\_campaign=skills%20account%20ted%20harrington\&utm\_content=main%20top\&utm\_term=2a)

### Input parameter coverage in security web application scanners

A result of a comparison of 60 commercial and open-source black box web application vulnerability scanners was released and titled: « The Scanning Legion: Web Application Scanners Accuracy Assessment & Feature Comparison ». This benchmark, realized by the security researcher Shay Chen in 2011, focused on testing commercial and open source tools that are able to detect (and not necessarily exploit) security vulnerabilities on a wide range of URLs.

We have concluded the chart below which shows input parameter's coverage supported by tested web application scanners. These inputs are basically:

* HTTP Query String Parameters (GET): input parameters sent in the URL.
* HTTP Body Parameters (POST): input parameters sent in the HTTP body.
* HTTP Cookie Parameters: input parameters sent in the HTTP cookie.
* HTTP Headers: HTTP request headers used by the application.

75% of Web application scanners couldn't discover HTTP Headers parameters related flaws. Furthermore, 70% of these scanners failed inspecting HTTP Cookies vulnerabilities else. These rates refer exactly to the ability of the scanners to scan the input vector, not simply to interpret it. Comparing to the reasonable score made for GET and POST, some automated testing tools may lead to unsatisfied results when dealing with HTTP header as an SQL injection input vector.

![](https://resources.infosecinstitute.com/wp-content/uploads/033012\_1704\_SQLInjectio2.png)As a matter of fact, HTTP Headers and Cookies should not be underestimated. Therefore, these two vectors should be taken into consideration during testing plan. Yet, when the vulnerability scanners used are not supporting these features, we should think about testing these parameters manually.

### Potential HTTP headers for SQL injections

#### HTTP header fields

HTTP header fields are components of the message header of requests and responses in the Hypertext Transfer Protocol (HTTP). They define the operating parameters of an HTTP transaction.

Example: Request HTTP

`GET / HTTP/1.1`

Connection: Keep-Alive

Keep-Alive: 300

Accept:\*/\*

Host: host

Accept-Language: en-us

Accept-Encoding: gzip, deflate

User-Agent: Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US;

rv:1.9.2.16) Gecko/20110319 Firefox/3.6.16 ( .NET CLR 3.5.30729; .NET4.0E)

Cookie: guest\_id=v1%3A1328019064; pid=v1%3A1328839311134

We can consider the HTTP Cookies, when are stored in databases for sessions identification, as the first potential HTTP variables which should be tested. We will see next in an example of Cookie based SQL injection. There are also other HTTP headers related to the application.

#### X-Forwarded-For

X-Forwarded-For is an HTTP header field considered as a de facto standard for identifying the originating IP address of a client connecting to a web server through an HTTP proxy or load balancer.

We will see an example of this flaw basing of a form submission.

`$req = mysql_query("SELECT user,password FROM admins WHERE user='".sanitize($_POST['user'])."' AND password='".md5($_POST['password'])."' AND ip_adr='".ip_adr()."'");`

The variable login is correctly controlled due to the sanitize() method.

`function sanitize($param){ if (is_numeric($param)) { return $param; } else { return mysql_real_escape_string($param); } }`

Let us inspect the ip variable. It is allocating the output of the ip\_addr() method.

`function ip_adr() { if`

(isset($\_SERVER\['HTTP\_X\_FORWARDED\_FOR'])) { $ip\_adr = $\_SERVER\['HTTP\_X\_FORWARDED\_FOR']; } else { $ip\_adr = $\_SERVER\["REMOTE\_ADDR"]; } if (preg\_match("#^\[0-9]{1,3}.\[0-9]{1,3}.\[0-9]{1,3}.\[0-9]{1,3}#",$ip\_addr)) { return $ip\_adr; } else { return $\_SERVER\["REMOTE\_ADDR"]; } }

Obviously, the IP address is retrieved from the HTTP header X\_FORWARDED\_FOR. This later is controlled by the preg\_match which verifies if this parameter does hold at least one IP address. As a matter of fact, the environment variable HTTP\_X\_FORWARDED\_FOR is not properly sanitized before its value being used in the SQL query. This can lead to run any SQL query by injecting arbitrary SQL code into this field.

The simple modification of this header field to something like:

`GET /index.php HTTP/1.1`

Host: \[host]

X\_FORWARDED\_FOR :127.0.0.1' or 1=1#

will lead to bypass the authentication control.

#### User-agent

User agent is an HTTP header field gives the software program used by the original client. This is for statistical purposes and the tracing of protocol violations. It should be included. The first white space delimited word must be the software product name, with an optional slash and version designator.

Not all applications are written to capture the user-agent data, but sometimes applications are designed to store such information (ex: shopping cart providers) to make use of it. In this case, it's worth investigating the user-agent header for possible issues.

HTTP query example:

`GET /index.php HTTP/1.1`

Host: \[host]

User-Agent: aaa' or 1/\*

#### Referer

Referer is another HTTP header which can be vulnerable to SQL injection once the application is storing it in database without sanitizing it. It's an optional header field that allows the client to specify, for the server's benefit, the address ([ URI](http://www.w3.org/Protocols/HTTP/Request.html) ) of the document (or element within the document) from which the URI in the request was obtained. This allows a server to generate lists of back-links to documents, for interest, logging, etc. It allows bad links to be traced for maintenance.

Example:

`GET /index.php HTTP/1.1`

Host: \[host]

User-Agent: aaa' or 1/\*

Referer: http://www.yaboukir.com

### Attacker's perspective?

As we all know, injection flaws are ranked the first in The OWASP Top 10 Web Application Security Risks. Attackers are increasingly seeking for injection points to get full access of your databases. No matter the injection input vector's type, whether it's a GET, POST, Cookie or other HTTP headers; the important for intruders is always to have at least one injection point which let them start the exploitation phase.

### Manually testing Cookie based SQL injections

In this section, we will introduce some methods of inspecting HTTP Cookie variables.

#### Using a browsers add-on

#### Cookies Manager+

Cookie Manager+ allows view, edit and create new cookies. It also allows show extra information about cookies and allows edit multiple cookies at once, as well as backup/restore them.

After installing it, from the Tools menu, select Cookies Manager+.We select a Cookie variable related to the target application.

![](https://resources.infosecinstitute.com/wp-content/uploads/033012\_1704\_SQLInjectio3.png)We will edit the language\_id variable. To figure out the SQL injection flaw, we will add a quote "'" in the field

content of the variable language\_id.

![](https://resources.infosecinstitute.com/wp-content/uploads/033012\_1704\_SQLInjectio4.png)After refreshing the page, or clicking on other internal link of the application, the application submits the request using the edited HTTP cookie. The result is triggered an SQL error:

![](https://resources.infosecinstitute.com/wp-content/uploads/033012\_1704\_SQLInjectio5.png)This database error is alerting us for a susceptible SQL injection flaw.

The advantage of using Cookies Manager+ is that it's simple to use, act directly on the cookie and saves the previous edited value of the cookie.

We will try to determine the number of column using another Firefox plug-in.

#### Tamper Data

Tamper Data is a powerful Firefox add-on to view and modify HTTP/HTTPS headers and post parameters.

After installing it, from the Tools menu, select Tamper Data. Start tampering HTTP request by clicking the button Start Tamper.

When launching any request from the target application, Tamper Data pops up a box and asks if we want to tamper the current HTTP request just sent.

![](https://resources.infosecinstitute.com/wp-content/uploads/033012\_1704\_SQLInjectio6.png)After clicking on Tamper,we got the full Tamper popup:

![](https://resources.infosecinstitute.com/wp-content/uploads/033012\_1704\_SQLInjectio7.png)We add: order by 4 into the HTTP cookie variable as shown in the previous screenshot. The response is normal from the application.

![](https://resources.infosecinstitute.com/wp-content/uploads/033012\_1704\_SQLInjectio8.png)We increment the number and add this time: order by 5. The response to this injection is as follows:

![](https://resources.infosecinstitute.com/wp-content/uploads/033012\_1704\_SQLInjectio9.png)So we can conclude that the number of columns is 4.

Now, we will try to figure out the affected columns in order to inject in it more SQL queries. So, we will add the following query into the language\_id HTTP cookie variable:

\-1+UNION+ALL+SELECT+1,2,3,4

The exploitation may need sometimes advanced SQL injection techniques.

### Using automated penetration testing scanner

#### Sqlmap as example

Sqlmap is a popular open source penetration testing tool that automates the process of detecting and exploiting SQL injection flaws and taking over of database servers.

Sqlmap supports the HTTP cookie features so it can be useful in two ways:

* Authentication based upon cookies when the web application requires that.
* Detection and exploitation of SQL injection on such header values.

By default sqlmap tests all GET parameters and POST parameters. When the value of --level is set to 2 or above it tests also HTTP Cookie header values. When this value is set to 3 or above, it tests also HTTP User-Agent and HTTP Referer header value for SQL injections. It is however possible to manually specify a comma-separated list of parameter(s) that you want sqlmap to test. This will bypass the dependence on the value of --level too.

Tested HTTP parameter Level in sqlmapGET 1 (Default)POST 1 (Default)HTTP Cookie 2 ≥HTTP User-Agent 3 ≥HTTP Referer 3 ≥

For instance, to test for GET parameter id and for HTTP User-Agent only, provide -p id,user-agent.

This is an example of how we can test the parameter named security of an HTTP Cookie of the DVWA (Damn Vulnerable Web Application).

`./sqlmap.py -u 'http://127.0.0.1/vulnerabilities/sqli/?id=1&Submit=Submit#'`

\--cookie='PHPSESSID=0e4jfbrgd8190ig3uba7rvsip1; security=low'

\--string='First name' --dbs --level 3 -p PHPSESSID

The flag --string compare between the valid pages and the invalid one (due to the injection). In the other hand, the flag --dbs is used to enumerate the database management systems. Finally, the flag –p force the testing of the PHPSESSID variable.

![](https://resources.infosecinstitute.com/wp-content/uploads/033012\_1704\_SQLInjectio10.png)Tools for testing SQL injection: choose by its detection accuracy or by its input vector coverage?

In order to answer this question, we have exploited the results of the benchmark provided by sectoolmarket.com. We have take in hypothesis that the detection accuracy of the candidate scanners has the same importance as input vectors coverage and support. We have considered GET, POST, HTTP Cookie and HTTP Headers as the input vectors that should be supported. When all these parameters are supported, the scanners make a rate 100% of coverage (4/4).

We suggest the equation below of arithmetic mean to adapt a balancing score for vulnerability scanners.

After balancing the obtained rates with the percentage of detection accuracy, we stopped by this result below for the first 14 scanners:

Vulnerability Scanner Vendor Detection Rate Average ScoreArachni [Tasos Laskos](http://www.sectoolmarket.com/vendors/56.html) 100.00% 100.00%Sqlmap [sqlmap developers](http://www.sectoolmarket.com/vendors/37.html) 97.06% 98,53%IBM AppScan [IBM Security Sys Division](http://www.sectoolmarket.com/vendors/66.html) 93.38% 96,69%Acunetix WVS [Acunetix](http://www.sectoolmarket.com/vendors/13.html) 89.71% 94,85%NTOSpider [NT OBJECTives](http://www.sectoolmarket.com/vendors/61.html) 85.29% 92,64%Nessus [Tenable Network Security](http://www.sectoolmarket.com/vendors/65.html) 82.35% 91,17%WebInspect [HP Apps Security Center](http://www.sectoolmarket.com/vendors/23.html) 75.74% 87,87%Burp Suite Pro [PortSwigger](http://www.sectoolmarket.com/vendors/59.html) 72.06% 86,03%Cenzic Pro [Cenzic](http://www.sectoolmarket.com/vendors/60.html) 63.24% 81,62%SkipFish [Michal Zalewski - Google](http://www.sectoolmarket.com/vendors/7.html) 50.74% 75,37%Wapiti [OWASP](http://www.sectoolmarket.com/vendors/6.html) 100.00% 75.00%Netsparker [Mavituna Security](http://www.sectoolmarket.com/vendors/12.html) 98.00% 74.00%Paros Pro [MileSCAN Technologies](http://www.sectoolmarket.com/vendors/2.html) 93.38% 71,69%ZAP [OWASP](http://www.sectoolmarket.com/vendors/51.html) 77,21% 63,60%

### What's next?

#### For developers

Cookies and other stored HTTP headers should be treated by developers as another form of user input and be subjected to the same validation routines.

#### For testers

The manipulation of HTTP header information on page requests (especially the REFERER and USER-AGENT fields) is important to identify whether the application is vulnerable to SQL Injection vectors or even to other standard vulnerabilities (XSS). It's a good practice to define and describe every way that a user may manipulate data which is used by the application. These data may be stored, fetched and processed from Cookies, HTTP-headers (like HTTP\_USER\_AGENT ), form-variables (visible and hidden), Ajax-, JQuery-, XML-requests.

![11 courses, 8+ hours of training](https://resources.infosecinstitute.com/contentassets/a657be75f19f4620a977a3534aa70a42/hackable-book-by-ted-harrington.jpg)

11 courses, 8+ hours of training

Learn cybersecurity from Ted Harrington, the #1 best-selling author of "Hackable: How to Do Application Security Right."[See Courses](https://app.infosecinstitute.com/portal/skills/path/3977/?utm\_source=resources\&utm\_medium=infosec%20network\&utm\_campaign=skills%20account%20ted%20harrington\&utm\_content=main%20bottom\&utm\_term=2a)

#### Sources

* Penetration Testing with Improved Input Vector Identification, William G.J. Halfond, Shauvik Roy Choudhary, and Alessandro Orso College of Computing Georgia Institute of Technology
* [Security Tools Benchmarking](http://sectooladdict.blogspot.com/2011/08/commercial-web-application-scanner.html) - By Shay-Chen
* [Wikipedia](https://en.wikipedia.org/wiki/X-Forwarded-For)
* [Tech brunch SQL injection](http://www.techbrunch.fr/securite/blind-sql-injection-header-http/)
* [W3 HTML headers ](http://www.w3.org/Protocols/HTTP/HTRQ\_Headers.html#user-agent)
* [W3 Protocols Headers](http://www.w3.org/Protocols/HTTP/HTRQ\_Headers.html#z14)
* [Mozilla Cookies manager plus ](https://addons.mozilla.org/en-US/firefox/addon/cookies-manager-plus/)
* [Mozilla addon tamper data ](https://addons.mozilla.org/en-US/firefox/addon/tamper-data/)
* [Sourceforge ](ttp://sqlmap.sourceforge.net/doc/README.html)
* [Msdn Microsoft ](http://msdn.microsoft.com/en-us/library/ms161953.aspx)
