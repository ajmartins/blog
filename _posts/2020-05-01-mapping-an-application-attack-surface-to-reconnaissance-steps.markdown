---
layout: post
title: Mapping an Application's Attack Surface to Reconnaissance Steps 
date: 2020-05-1 12:12:00 +0100
description: Using an attackers perspective to enumerate attack surfaces in web applications. # Add post description (optional)
img: attacksurface.png # Add image post (optional)
fig-caption: # Add figcaption (optional)
tags: [AttackSurface, Reconnaissance]
---

Gaining visibility into an application's attack surface is really important in identifying risk areas. In cases of an attack, it can also help in determining root cause i.e. the part(s) of an attack surface that was the origin of the breach. 

In using an attacker's perspective through reconnaissance, you can proactively identify attack surfaces. This will always help in quickly noticing attack surface changes or deciding on the best strategy for minimizing risk.

I will be highlighting the various attack surface components that can be found during the different phases of a typical (web app) reconnaissance workflow. To have a better understanding, I will make use of football analogies.

## Football Analogy: Attack Surface Enumeration via Recon

Let's do an illustration by using analogies from the beautiful game of football, specifically using a phase of play that has been well discussed in the last couple of years: `The Build-Up Play`. Think of this as any football game where in building up from the back, `the goal is to move the ball (in this case our workflow/process) from our half to the opponent’s half`. 

Befittingly we'll be using the 4-3-3 formation to show this, used by some of the greatest and most celebrated teams of all time; Ajax 1971–73, Bayern 1973–76, Barcelona 2008-2012.


## PATHWAY 1: Build-Up Play (Through the Middle)
***A flowing move through the middle is always fun***
![Through The Middle]({{site.baseurl}}/assets/img/433-1.png)

>[6] SUBDOMAIN ENUMERATION
{% highlight markdown %}
Usually, the first task is to discover subdomains in a given scope/target list and then map it to their corresponding ip addresses.
{% endhighlight %}
* **ATTACK SURFACE (DNS Information)**

There is great value in having complete knowledge of all DNS properties (e.g. subdomains) mapped to an application. Subdomain enumeration is a process that involves a lot of open source intelligence (OSINT), by combing various sources for valuable data like ip addresses, whois info, asn, zone records, email addresses etc. A company could have subdomains that they are not aware of, which affects visibility into those subdomains and could make them less secure. A good tool for finding, tracking and automating the DNS discovery process is Amass.

| Category | Component | Notes
|-------|--------|---------|
| DNS Information | Subdomains | test.corp.ng
|  | IP Address | 10.10.10.10 |

<br/>
<br/>

>[5] PORT SCAN
{% highlight markdown %}
Using the ip address(es) mapped to the (sub)domain, we do a port scan to get a list of open ports and the services running on them.
{% endhighlight %}
* **ATTACK SURFACE (Network Information)**

Disabling unneeded ports or services is a well known hardening control to help reduce attack surfaces.

| Category | Component | Notes
|--------|---------|
| Network Information | Open Ports/Protocol | 8443 (TCP)
|  | Running Services | Apache |

<br/>
<br/>

>[4] GATHER VALID TARGETS
{% highlight markdown %}
This is probably the most important step as it involves visually identifying subdomains through screenshots, walking through the application and mapping each functionality. The questions to ask here are: What does it do and How does it do it? 
{% endhighlight %}
* **ATTACK SURFACE (Input Vectors)**

In navigating the application, the aim here is to discover any functionality in the application that accepts input. 

| Category | Component | Notes
|--------|---------|
| Input Vectors | Data entry {CRUD} forms & Fields | /signup
|  | Inquiries and Search Functions | /search
|  | File Upload Functions | /upload

* **ATTACK SURFACE (Access Controls)**

We also want to understand how the application implements security measures for things like authentication, authorization or session management.

| Category | Component | Notes
|--------|---------|
| Access Controls | Roles | Admin, User
|  | Privileges | /upload, /search
|  | Session State/Cookies | #####, ####

* **ATTACK SURFACE (Key Functionality)**

Finally we try to identify key functions based on the business workflow or logic.

| Category | Component | Notes
|--------|---------|
| Key Functionality | Business Workflow/Logic | API Calls, Database Access

<br/>
<br/>

>[8] DIRECTORY-FILE DISCOVERY
{% highlight markdown %}
Moving forward, using the list of valid subdomain targets as input, we try to check for information disclosure on existing/hidden paths and files with tools like dirsearch/gobuster. Doing this will also help us have an idea of the directory structure. This step also marks the first time we move to the opposition’s half, thereby making our build-up play successful.
{% endhighlight %}
* **ATTACK SURFACE (Hidden Content)**

It is time to find additional data, anything that will uncover parts of the application that may be hidden or content that does not have direct links to pages.

| Category | Component | Notes
|--------|---------|
| Hidden Content | Files | .bak .tmp .conf “cache:” robots.txt
|  | Directories | /ccard/2020, admin panel, error pages

<br/>
<br/>

>[10] PLATFORM IDENTIFICATION
{% highlight markdown %}
What does it run on? Finally, using the valid list of subdomains as input again, we can identify the different platforms or technologies (e.g. development/cloud platform) used by the web application.
{% endhighlight %}
* **ATTACK SURFACE (Tech Stack)**

| Category | Component | Notes
|--------|---------|
| Tech Stack | Language | python
|  | Framework | django
|  | Technologies/Platforms | aws github
|  | Integrations/Plugins | shopify

<br/>
<br/>

## PATHWAY 2: Build-Up Play (Long Ball Over the Top)
***A long ball over the top could achieve the same goal***
![Through The Middle]({{site.baseurl}}/assets/img/433-3.png)

>[9] FIND MISCONFIGURATIONS
{% highlight markdown %}
Quick misconfiguration checks can either be made on the list of valid subdomains or from the target list. We can check for things like subdomain takeover or CORS misconfiguration etc. depending on the identified platforms.

{% endhighlight %}
* **ATTACK SURFACE (3rd Party Platforms)**

| Category | Component | Notes
|--------|---------|
| 3rd Party Platforms | Configuration Weaknesses | (subdomain takeover) example.corp.ng/404.html
|  | | (github page) api secret key


<br/>
<br/>

## PATHWAY 3: Build-Up Play (Through the Flanks)
***Sometimes the flanks provide a better path to move upfield***
![Through The Middle]({{site.baseurl}}/assets/img/433-2.png)

>[2]&[7] DISCOVER PUBLIC EXPLOITS  
{% highlight markdown %}
Going through the left flank, we can use the output of the nmap results in the previous port scan to enumerate running web services (e.g. CMS Version) and search for disclosed vulnerabilities (searchsploit). 
{% endhighlight %} 
* **ATTACK SURFACE (Known Vulnerabilities)**

Vulnerability scanners primarily perform this function, which is to identify service/software versions and compare it with a database of known vulnerabilities or use any signature-matching process. However because of the possibility of false positives or negatives, it is always a good idea to manually review vulnerability scan results when possible.

| Category | Component | Notes
|--------|---------|
| Known Vulnerabilities | Service/Software Exploits | RCE exploit (apache version)

<br/>
<br/>

>[3]&[11] DISCOVER ENTRYPOINTS
{% highlight markdown %}
Through the right, we try to gather interesting entrypoints by looking through js files, api calls (e.g get/post requests), url links, old versions of a webpage, web services etc.
{% endhighlight %}
* **ATTACK SURFACE (ENDPOINTS)**

| Category | Component | Resource | Method | Entrypoint | Parameter |
| | |-------|--------|---------|---------|
| Endpoints | Rest API | /login | POST | api.corp.ng | username=admin&password=*** |

<br/>
<br/>

# Conclusion
Attack surfaces change all the time from the introduction of new app servers, to changes in the software packages running on them and so on. Therefore it is crucial that different strategies be taken to keep accurate information of an applications attack surface. Today we have looked at enumerating attack surfaces via a typical recon workflow, but beyond identification it is also necessary to do analysis for determining high risk areas based on the different functionalities of the application. To read further on this, I recommend going through the owasp cheatsheet for attack surface analysis: [Owasp Attack Surface Cheatsheet][owasp-link].

[owasp-link]: https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Attack_Surface_Analysis_Cheat_Sheet.md
