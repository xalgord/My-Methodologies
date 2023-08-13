---
description: 'source: https://infosecwriteups.com/simple-recon-methodology-920f5c5936d4'
---

# 🔎 Simple Recon Methodology

Hey folks, here we back again with the most important topic in penetration testing or Bug Bounty Hunting “Recon” or “Information gathering”.

<figure><img src="https://miro.medium.com/v2/resize:fit:763/1*IXPf97mXAme5zx4zkAOT1w.png" alt="" height="395" width="700"><figcaption></figcaption></figure>

### Content <a href="#649f" id="649f"></a>

1. What’s Recon ?
2. Recon based scope
3. simple steps to collect all information in few time
4. Recommended tools and automation frameworks
5. Recommended blogs, streams to follow

### What’s Recon ? <a href="#b34b" id="b34b"></a>

Before we start our talk, let’s know what’s the recon first?

**Recon** is the process by which you collect more information about your target, more information like subdomains, links, open ports, hidden directories, service information, etc.

To know more about recon, just see this pic to know where you're before and after recon…

<figure><img src="https://miro.medium.com/v2/resize:fit:763/1*lSX0jOcyPxwYf00RJ1j8Ow.png" alt="" height="427" width="700"><figcaption><p>informations Before Recon and After Recon</p></figcaption></figure>

So the question of which in your mind now is how we will collect all this information, and what’s kind of tools we will use?\
Actually, to collect all this information you need to follow methodology, I’ll show you my own methodology and after a few minutes you will know how it works.

<figure><img src="https://miro.medium.com/v2/resize:fit:763/1*PRAW3GCI-RjH5T5vnvQ12g.png" alt="" height="413" width="700"><figcaption><p>My own methodology — 3klcon Automation framework — src: <a href="https://github.com/eslam3kl/3klCon/blob/v2.0/3klcon-MEthedology.png">https://github.com/eslam3kl/3klCon/blob/v2.0/3klcon-MEthedology.png</a></p></figcaption></figure>

> The Recon process should be based on scope, and I mean that you should collect information depending on your scope area **(small, medium, or large)**. The difference will be in the **amount** and **type** of data you will collect, so let’s get started.

### Recon based scope <a href="#c030" id="c030"></a>

We will divide the scopes into 3 types (Small, Medium and large scope)

**A. Small Scope**

In this type of scopes, you have the only _subdomain_ which you are allowed to test on it like `sub.domain.com` and you don’t have any permission to test on any other subdomain, the information which you should collect will be like this…

<figure><img src="https://miro.medium.com/v2/resize:fit:763/1*Ha5RF1gv3f98fwDDd_oYNg.png" alt="" height="402" width="700"><figcaption></figcaption></figure>

As you can see the information you should collect will be based on the subdomain you have permission to test on it only like directory discovery files, service information, JS files, GitHub dorks, waybackurls, etc

**B. Medium scope**

Here your testing area will be increased to contain all subdomains related to a specific domain, for example, you have a domain like `example.com` and on your program page, you’re allowed to test all subdomains like `*.domain.com` In this step the information which you should collect will be more than the small scope to contain for example all subdomains and treat every subdomain as small scope “_we will talk more about this point_”, just know the type of the information only.

<figure><img src="https://miro.medium.com/v2/resize:fit:763/1*1DBq5nqqIc3Z86ZkiHTQYw.png" alt="" height="442" width="700"><figcaption><p>Medium scope required informations</p></figcaption></figure>

**C. Large scope**

In this type of scopes, you have the permission to test all websites which belong to the main company, for example, you started to test on `IBM` company, so you need to collect all domains, subdomains, acquisitions, and ASN related to this company and treat every domain as medium scope. This type of scopes is the best scopes ever ❤

<figure><img src="https://miro.medium.com/v2/resize:fit:763/1*jn5H1ttjRMLLR1uVkM3dfw.png" alt="" height="447" width="700"><figcaption><p>Large scope required informations</p></figcaption></figure>

So here we know all the information which you need to collect for every scope, now let’s talk about how to collect all this info.

Let’s see how to collect this !

<figure><img src="https://miro.medium.com/v2/resize:fit:436/1*A8Kl_IDSefN6FPyPR18DAg.jpeg" alt="" height="400" width="400"><figcaption><p>Ready ?</p></figcaption></figure>

### Simple steps to collect all information <a href="#671a" id="671a"></a>

we will work here as medium scope to be simple to understand

> All the tools used here are free as open source on GitHub

* collect all subdomains from tools like `subfinder`, `amass`, `crtfinder`, `sublist3r` (Use more than tool)
* Use Google dorks for example `site:ibm.com -www`
* collect all these informations from `subdinder + amass + crtfinder + sublist3r + google_dorks` and collect all of them into one text file `all_subdomains.txt`

**\[\*] Now we have 1 text file contains all subdomains** `all_subdomains.txt`**, let’s continue…**

* Pass the text file over `httpx` or `httprobe` , these tools will filter all subdomains and return only live subdomains which works on ports 80 and 443
* take these live subdomains and collect them into separate file `live_subdomains.txt`

**\[\*] Now we have 2 text files** `all_subdomains.txt + live_subdomains.txt`

* take the `live_subdomains.txt` file and pass it over `waybackurls` tool to collect all links which related to all live subdomains
* collect all these links into new file `waybackurls.txt`

**\[\*] Now we have 3 text files** `all_subdomains.txt + live_subdomains.txt+ waybackurls.txt`

* take all subdomains text file and pass it over `dirsearch` or `ffuf` to discover all hidden directories like [`https://community`](https://community/)`.ibm.com/database_conf.txt`
* collect and filter all the results to show only 2xx, 3xx, 403 response codes from the tool itself (use -h to know how to filter the results)
* collect all these informations into text file `hidden_directories.txt` and try to discover the leakage data or the forbidden pages and try to bypass them

**\[\*] Now we have 4 text files** `all_subdomains.txt + live_subdomains.txt + waybackurls.txt + hidden_directories.txt`

* pass `all_subdomains.txt` to `nmap` or `masscan` to scan all ports and discover open ports + try to brute force this open ports if you see that this ports may be brute forced, use `brute-spray`to brute force this credentials
* collect all the results into text file `nmap_results.txt`

**\[\*] Now we have 5 text files** `all_subdomains.txt + live_subdomains.txt + waybackurls.txt + hidden_directories.txt + nmap_results.txt`

* use `live_subdomains.txt` and search for credentials in GitHub by using automated tools like `GitHound` or by manual search (I’ll put pretty reference in the references section)
* collect all these information into text file `GitHub_search.txt`

**\[\*] Now we have 6 text files** `all_subdomains.txt + live_subdomains.txt + waybackurls.txt + hidden_directories.txt + nmap_results.txt + GitHub_search.txt`

* use `altdns` to collect subdomains from subdomains, for example `sub.sub.sub.domain.com`
* As usual :) collect all this info into text file `altdns_subdomain.txt`

**\[\*] Now we have 7 text files** `all_subdomains.txt + live_subdomains.txt + waybackurls.txt + hidden_directories.txt + nmap_results.txt + GitHub_search.txt + altdns_subdomain.txt`

* pass `waybackurls.txt` file over `gf` tool and use `gf-patterns` to filter the links to possible vulnerable links, for example if the link has parameter like `?user_id=` so this link may be vulnerable to **sqli** or **idor**, if the link has parameter like `?page=` so this link may be vulnerable to **lfi**
* collect all this vulnerable links into directory `vulnerable_links.txt` and into this directory have separated text files for all vulnerable links `gf_sqli.txt` , `gf_idor.txt` ,etc

**\[\*] Now we have 7 text files** `all_subdomains.txt + live_subdomains.txt + waybackurls.txt + hidden_directories.txt + nmap_results.txt + GitHub_search.txt + altdns_subdomain.txt` **and one directory** `vulnerable_links.txt`

* use `grep` to collect all JS files form `waybackurls.txt` as `cat waybackurls.txt | grep js > js_files.txt`
* you can analyze these files manually or use automation tools (I recommend manual scan, see references)
* save all the results to `js_files.txt`

**\[\*] Now we have 8 text files** `all_subdomains.txt + live_subdomains.txt + waybackurls.txt + hidden_directories.txt + nmap_results.txt + GitHub_search.txt + altdns_subdomain.txt + js_files.txt` **+ one directory** `vulnerable_links.txt`

* Pass `all_subdomain.txt + waybackurls.txt + vulnerable_links.txt` to `nuclei` “Automation scanner” to scan all of them.

Next step!! Don’t worry, No more steps :)

<figure><img src="https://miro.medium.com/v2/resize:fit:763/1*PBkdvNHgXwA1KjsYBtTydQ.jpeg" alt="" height="611" width="700"><figcaption></figcaption></figure>

**Congratulations,** you have finished the biggest part of your recon ❤

Now I’m sure you know all this steps good, go to the upper methodology and check it again and see if you understand it or not!

Good ! Let’s move to the next step…

### Recommended tools and automation frameworks <a href="#cece" id="cece"></a>

**> For Automation frameworks, I recommend 2 frameworks**

* `3klcon` [https://github.com/eslam3kl/3klCon](https://github.com/eslam3kl/3klCon) — My own framework and it depends on the upper methodology
* `Bheem` [https://github.com/harsh-bothra/Bheem](https://github.com/harsh-bothra/Bheem)

**> For the tools**

* `3klector` [https://github.com/eslam3kl/3klector](https://github.com/eslam3kl/3klector)
* `crtfinder` [https://github.com/eslam3kl/crtfinder](https://github.com/eslam3kl/crtfinder)
* `Subfinder` [https://github.com/projectdiscovery/subfinder](https://github.com/projectdiscovery/subfinder)
* `Assetfinder` [https://github.com/tomnomnom/assetfinder](https://github.com/tomnomnom/assetfinder)
* `Altdns` [https://github.com/infosec-au/altdns](https://github.com/infosec-au/altdns)
* `Dirsearch` [https://github.com/maurosoria/dirsearch](https://github.com/maurosoria/dirsearch)
* `Httpx` [https://github.com/projectdiscovery/httpx](https://github.com/projectdiscovery/httpx)
* `Waybackurls` [https://github.com/tomnomnom/waybackurls](https://github.com/tomnomnom/waybackurls)
* `Gau` [https://github.com/lc/gau](https://github.com/lc/gau)
* `Git-hound` [https://github.com/tillson/git-hound](https://github.com/tillson/git-hound)
* `Gf` [https://github.com/tomnomnom/gf](https://github.com/tomnomnom/gf)
* `Gf-pattern` [https://github.com/1ndianl33t/Gf-Patterns](https://github.com/1ndianl33t/Gf-Patterns)
* `Nuclei` [https://github.com/projectdiscovery/nuclei](https://github.com/projectdiscovery/nuclei)
* `Nuclei-templets` [https://github.com/projectdiscovery/nuclei-templates](https://github.com/projectdiscovery/nuclei-templates)
* `Subjack` [https://github.com/haccer/subjack](https://github.com/haccer/subjack)

### Credits <a href="#f905" id="f905"></a>

Harsh Bothra “[Recon based scope](https://cobalt.io/blog/scope-based-recon-smart-recon-tactics)”\
Jhaddix [offensity](https://www.offensity.com/en/blog/just-another-recon-guide-pentesters-and-bug-bounty-hunters/)
