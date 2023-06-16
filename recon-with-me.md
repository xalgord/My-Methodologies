---
description: >-
  By dhiyaneshgeek -
  https://dhiyaneshgeek.github.io/bug/bounty/2020/02/06/recon-with-me/
---

# 🔎 Recon With Me

Hi Everyone

In this blog, I will cover automating the enumeration part of reconnaissance and finding bugs using the following set of tools.

* [Subfinder](https://github.com/projectdiscovery/subfinder)
* [Chaos](https://github.com/projectdiscovery/chaos-client)
* [Nuclei](https://github.com/projectdiscovery/nuclei)
* [Httpx](https://github.com/projectdiscovery/httpx)
* [Notify](https://github.com/projectdiscovery/notify)
* [Anew](https://github.com/tomnomnom/anew)

![](https://dhiyaneshgeek.github.io/images/recon/logo.png)

**Subfinder**

![](https://dhiyaneshgeek.github.io/images/recon/sub1.png)

![](https://dhiyaneshgeek.github.io/images/recon/description1.png)

* [Download](https://github.com/projectdiscovery/subfinder) and install subfinder using the following command

`GO111MODULE=on go get -v github.com/projectdiscovery/subfinder/v2/cmd/subfinder`

Note: Make sure go1.14+ is installed in your system.

* To run the tool on any of the Target website , use the following command

`subfinder -d google.com`

![](https://dhiyaneshgeek.github.io/images/recon/sub2.png)

* It was able to fetch more than 50246+ subdomains in a short amount of time.
* Then i noticed that we can use `-v` flag to see the verbose output of the sources that are used to enumerate the subdomains.

`subfinder -d google.com -v`

![](https://dhiyaneshgeek.github.io/images/recon/sub3.png)

* Sources like chaos,threatminer,sublist3r,alienvault ,etc,. are being used to enumerate the subdomains of the target.
* In the Subfinder Github Repository it was mentioned that some of the services will not work until you set it up.

![](https://dhiyaneshgeek.github.io/images/recon/sub4.png)

* So i started looking into it to set-up the **config-file** with the API Keys that are mentioned to see what is the **major difference in the results of subdomain**
* Navigate to the following directory

`cd .config/subfinder/`

![](https://dhiyaneshgeek.github.io/images/recon/sub5.png)

`cat config.yaml` to see the config file

![](https://dhiyaneshgeek.github.io/images/recon/sub6.png)

* We can see many of the API Key services are **Empty** , so now are going to fill the necessary API Keys as source for Subdomain Enumeration.

**Note** The below following API Keys are **Free Of Cost** and has a Limited number of request in it.

* binaryedge
* censys
* certspotter
* chaos
* dnsdb
* github
* intelx
* passivetotal
* robtex
* securitytrails
* shodan
* spyse
* urlscan
* virustotal
* zoomeye

**Binaryedge**

**1 :** [Sign up](https://app.binaryedge.io/sign-up) for a free account, verify the account.

**2 :** Login into the account and Navigate to this URL [https://app.binaryedge.io/account/api](https://app.binaryedge.io/account/api) and give a name to the **TOKEN** and Click on Generate Token.

![](https://dhiyaneshgeek.github.io/images/recon/sub7.png)

**Censys**

**1 :** [Sign up](https://censys.io/register) for a free account, verify the account.

**2 :** Login into the account and Navigate to this URL [https://censys.io/account/api](https://censys.io/account/api) and you will be able to get **API ID** and **Secret**

![](https://dhiyaneshgeek.github.io/images/recon/sub8.png)

**Certspotter**

**1 :** [Sign up](https://sslmate.com/signup?for=certspotter\_api) for a free account.

**2 :** Login into the account and Navigate to this URL [https://sslmate.com/account/api\_credentials](https://sslmate.com/account/api\_credentials) and you will be able to get the **API Key**

Note : 100 queries an hour is **free**.

![](https://dhiyaneshgeek.github.io/images/recon/sub9.png)

**Chaos**

**1 :** Navigate to this URL [https://chaos.projectdiscovery.io/#/](https://chaos.projectdiscovery.io/#/)

**2 :** Early access is provided basis on signup and queue and Invite are send out Weekly basis.

**3 :** Contributor access is Provided on the basis of **PR** that is done under `github.com/projectdiscovery/*`.

![](https://dhiyaneshgeek.github.io/images/recon/sub10.png)

**DNSdb**

**1 :** [Sign up](https://www.farsightsecurity.com/dnsdb-community-edition/) for a free community account.

**2 :** It will ask for Company Email , use [Temp Email](https://temp-mail.org/).

**3 :** Create an account and verify the email and get the **API Key**.

Note : It has 30-day renewal (with valid email confirmation)

![](https://dhiyaneshgeek.github.io/images/recon/sub11.png)

**Github**

**1 :** [Sign up](https://github.com/join) for a free account, verify the account.

**2 :** Navigate to this URL [https://github.com/settings/tokens](https://github.com/settings/tokens) and generate a **Personal access tokens**.

![](https://dhiyaneshgeek.github.io/images/recon/sub12.png)

**Intelx**

**1 :** [Sign up](https://intelx.io/signup) for a free account, verify the account.

**2 :** Navigate to this URL [https://intelx.io/account?tab=developer](https://intelx.io/account?tab=developer) and you will get the **API details**.

Note: Trial 1 week for Free

![](https://dhiyaneshgeek.github.io/images/recon/sub13.png)

**Passivetotal**

**1 :** [Sign up](https://community.riskiq.com/home) for a free account, verify the account.

**2 :** Login into the account and Navigate to this URL [https://community.riskiq.com/settings](https://community.riskiq.com/settings) and you will be able to get **KEY** and **Secret** .

![](https://dhiyaneshgeek.github.io/images/recon/sub14.png)

**Robtex**

**1 :** Sign in using the google **Gmail Account**

**2 :** Navigate to this URL [https://www.robtex.com/dashboard/](https://www.robtex.com/dashboard/) , you will get the **API-Key** details.

![](https://dhiyaneshgeek.github.io/images/recon/sub15.png)

**Security Trails**

**1 :** [Sign up](https://securitytrails.com/app/signup) for a free account, verify the account.

**2 :** Login into the account and Navigate to this URL [https://securitytrails.com/app/account/credentials](https://securitytrails.com/app/account/credentials) and you will be able to get **API Key** .

Note : Monthly Quoto is 50 API Requests.

![](https://dhiyaneshgeek.github.io/images/recon/sub16.png)

**Shodan**

**1 :** [Register](https://account.shodan.io/login) for a shodan account.

**2 :** Login into the account and navigate to this URL [https://account.shodan.io/](https://account.shodan.io/) , you will get the **API Key** details.

![](https://dhiyaneshgeek.github.io/images/recon/sub17.png)

**Spyse**

**1 :** [Register](https://spyse.com/user/registration) for a Spyse account and verify it.

**2 :** Login into the account and navigate to this URL [https://spyse.com/user](https://spyse.com/user) , you will get the **API Token** details.

Note : It has 100 API Token valid for 5 days during the Trail Period.

![](https://dhiyaneshgeek.github.io/images/recon/sub18.png)

**UrlScan**

**1 :** [Sign up](https://urlscan.io/user/signup) for a free account, verify the account.

**2 :** Login into the account and Navigate to this URL [https://urlscan.io/user/profile/](https://urlscan.io/user/profile/) and click on Create new **API Key**.

![](https://dhiyaneshgeek.github.io/images/recon/sub19.png)

**Virustotal**

**1 :** [Register](https://www.virustotal.com/gui/join-us) for a Virustotal account and verify it.

**2 :** Login into the account and navigate to this URL [https://www.virustotal.com/gui/user/username/apikey](https://www.virustotal.com/gui/user/username/apikey) , you will get the **API Key** details.

![](https://dhiyaneshgeek.github.io/images/recon/sub20.png)

**Zoom Eye**

**1 :** [Register](https://sso.telnet404.com/accounts/register/) for a ZoomEye account and verify it.

**2 :** Login into the account and navigate to this URL [https://www.zoomeye.org/profile](https://www.zoomeye.org/profile) , you will get the **API Key** details.

![](https://dhiyaneshgeek.github.io/images/recon/sub21.png)

**Now the Final Config File Looks Full!!!**

![](https://dhiyaneshgeek.github.io/images/recon/sub22.png)

Now Let us compare the Results **Before** and **After** Adding API Keys.

**Before API Key**

![](https://dhiyaneshgeek.github.io/images/recon/sub24.png)

**After API Key**

![](https://dhiyaneshgeek.github.io/images/recon/sub23.png)

* From this we can see we got **114817** Addtional subdomains after using **Free** API Keys.
* Only difference is the duration that is taken during the API scan is **slow** than the normal one.

**Httpx**

![](https://dhiyaneshgeek.github.io/images/recon/httpx1.png)

![](https://dhiyaneshgeek.github.io/images/recon/description2.png)

* [Download](https://github.com/projectdiscovery/httpx) and install httpx using the following command

`GO111MODULE=on go get -v github.com/projectdiscovery/httpx/cmd/httpx`

Note: Make sure go1.14+ is installed in your system.

* To run this tool against all the hosts and subdomains in hosts.txt and returns URLs running HTTP webserver, use the following command

`cat hosts.txt | httpx`

![](https://dhiyaneshgeek.github.io/images/recon/httpx2.png)

**Nuclei**

![](https://dhiyaneshgeek.github.io/images/recon/nuclei1.png)

![](https://dhiyaneshgeek.github.io/images/recon/description3.png)

* [Download](https://github.com/projectdiscovery/nuclei) and install nuclei using the following command

`GO111MODULE=on go get -v github.com/projectdiscovery/nuclei/v2/cmd/nuclei`

Note: Make sure go1.14+ is installed in your system.

* Scanning for misconfiguration on given list of URLs.

`nuclei -l target_urls.txt -t misconfigured-docker.yaml`

![](https://dhiyaneshgeek.github.io/images/recon/nuclei2.png)

More **Nuclei Templates** can be found here [https://github.com/projectdiscovery/nuclei-templates](https://github.com/projectdiscovery/nuclei-templates).

**Notify**

![](https://dhiyaneshgeek.github.io/images/recon/notify1.png)

![](https://dhiyaneshgeek.github.io/images/recon/description4.png)

* [Download](https://github.com/projectdiscovery/notify) and install notify using the following command

`GO111MODULE=on go get -v github.com/projectdiscovery/notify/cmd/notify`

Note: Make sure go1.14+ is installed in your system.

* Notify also supports piping output of any tool and send it over discord/slack channel as notification.
* Now we need to configure **Slack Webhook URL** under the `notify` config file.
* Navigate to the following directory.

`cd .config/notify/`

cat `notify.conf` to see the config file.

![](https://dhiyaneshgeek.github.io/images/recon/notify3.png)

* From the above image , we can see **slack\_webhookurl,slack\_username,slack\_channel,slack** are madatory to set of a slack notification using **notify**

**Note** : Follow the below steps to create a Slack account and Generate WebhookURL.

**Step 1:** [Sign Up](https://slack.com/get-started#/create) for a slack account and verify the account.

**Step 2:** During the process , it will ask you to setup the **Team Name , Channel Name , Invites**.

![](https://dhiyaneshgeek.github.io/images/recon/notify4.png)

**Step 3:** Navigate to this URL [https://api.slack.com/apps](https://api.slack.com/apps) and click on create app.

![](https://dhiyaneshgeek.github.io/images/recon/notify5.png)

**Step 4:** Enter the **App Name** and **Slack Workspace** as shown below.

![](https://dhiyaneshgeek.github.io/images/recon/notify6.png)

**Step 5:** Choose **Incoming Webhooks** under the App Name and set the Activate Incoming WebhookURL to **ON**.

![](https://dhiyaneshgeek.github.io/images/recon/notify7.png)

**Step 6:** In the bottom of the page you will be able to see **Webhook URLs for Your Workspace**, click on **Add New Webhook to Workspace**.

![](https://dhiyaneshgeek.github.io/images/recon/notify8.png)

**Step 7:** It will ask you for the permission to access slack workspace and the **channel name** and click on **allow**.

![](https://dhiyaneshgeek.github.io/images/recon/notify9.png)

**Step 8:** Now your slack webhookurl is generated as shown below.

![](https://dhiyaneshgeek.github.io/images/recon/notify10.png)

* We have got the Slack WebhookURL , Slack Username and Channel name.

![](https://dhiyaneshgeek.github.io/images/recon/notify11.png)

* Add these into the **notify.conf** , To edit the file use the following command.

`vi .config/notify/notify.conf`

* Uncomment the following fields `slack_webhook_url, slack_username, slack_channel, slack` and add the necessary details.

![](https://dhiyaneshgeek.github.io/images/recon/notify12.png)

![](https://dhiyaneshgeek.github.io/images/recon/notify13.png)

* Sample command to check the configuration works fine.

`curl -X POST -H 'Content-type: application/json' --data '{"text":"Hello, World!"}' https://hooks.slack.com/services/**********************************`

![](https://dhiyaneshgeek.github.io/images/recon/notify14.png)

* Scan for subdomains, check alive domains and send slack notifications using **notify** use the following command.

`subfinder -d hackerone.com | httpx | notify`

![](https://dhiyaneshgeek.github.io/images/recon/notify2.png)

**Anew**

* [Download](https://github.com/tomnomnom/anew) and install this Tool using the following command

`go get -u github.com/tomnomnom/anew`

* This tool is used to compare the output with old file and give us what is newly added in the file

For example `domain.txt` contains a set of sub-domains and now `subs.txt` same set of subdomains but included few additional extra new subdomains.

* Using the following command we can compare both the files and get what is newly added.

`cat subs.txt | anew domain.txt`

![](https://dhiyaneshgeek.github.io/images/recon/anew.png)

**Security Through Intelligent Automation**

* Now by using the above all tools we are going to create a **One Linear**. **subfinder** to find new subdomains , check wheather it is alive or not using **httpx** then scan it with **nuclei** and send us a slack notification of the output using **notify**.

**First Run**

`subfinder -silent -dL domains.txt | anew subs.txt`

Note : `-dL` - File containing list of domains to enumerate.

**Second Run**

`while true; do subfinder -dL domains.txt -all | anew subs.txt | httpx | nuclei -t nuclei-templates/ | notify ; sleep 3600; done`

Note : `while true; do` - Keeps the script alive. `sleep 3600; done` - Run after exactly every one hour.

* The **First Run** is madatory for this process so that it will do subdomain enumeration for the list of domains that you give and save it in a `.txt` file.
* The **Second Run** is used to find new subdomains , check alive status , scan it with nuclei templates and notify us the output.

Note : I highly suggest to run these one linear on VPS system like Digital Ocean or AWS instance.

![](https://dhiyaneshgeek.github.io/images/recon/automation.png)

**Why Automation is needed in reconnaissance ?**

* The chances of getting **duplicates** is **very less** and you will be focusing more on **NEW subdomains**

**For Example**

**Real Scenario**

* I got an alert at 3:54 PM Feb 05, 2021

![](https://dhiyaneshgeek.github.io/images/recon/slack1.png)

* I reported the vulnerability to the program at 3:59 PM Feb 05, 2021

![](https://dhiyaneshgeek.github.io/images/recon/slack2.png)

* Reported got Triaged and Valid !!!
