---
title: FortiGate Threat Feeds Hosted in a Private GitHub Repo
date: 2023-07-05 19:30:00 -400
categories: [github,fortinet,cybersecurity,IR]
tags: [threatfeeds,fortigate,IR] 
---

![GitHub Feeds](https://github.com/GraniteDan/GraniteDan.github.io/blob/main/img/GithubFeeds.jpg?raw=true)

# Hosting Fortigate Threat Feed Data in a Private GitHub Repo

Fortigate firewalls allow for the configuration of external threat feeds.  These are very usefull in some instances.  I created a Youtube video on this topic a while back.  If you are not yet familiar with this functionality you can visit the video here:

[Configure and use 3rd Party threat feeds on a Fortigate Firewall](https://youtu.be/CarI6_URN90)

In the video mentioned above I referenced a use case, where one may choose to build some custom threat feeds for their organization.  These could be used during incident response scenarios.

During an incident response you may come across various indicators of compromise, or indicators of attack.  These can be IP addresses, Malware hashes, domain names that could be attributed to data exfiltration or command & control activity, or malicious URLs.  The Fortigate NGFW platform has the ability to ingest threat feed data for each of these types.  Typically these are downloaded by the Fortigate from a web service as a text file (http, https, and STIX protocols are supported).  Feed data may also be updated by Push API updates to the Fortigate REST API (*Topic for a Future Post*).

External Feed Downloads Support only basic Authentication.

Fortinet Documentation outlines additional configuration and feed data format, as well as any specific size limitations etc:

* [Threat Feed Documentation](https://docs.fortinet.com/document/fortigate/7.2.5/administration-guide/9463/threat-feeds)
* [Configuring a Threat Feed](https://docs.fortinet.com/document/fortigate/7.2.5/administration-guide/379433/configuring-a-threat-feed?fbclid=IwAR1MBgmO43rpNOB0pJQAISyZYWrKQjIrw9cOSGfqx5NhWd9mx7f3qwmoHRU)

## Hosting Custom Feeds
Although it would be very easy to compile custom feed data into text files and host them on an internal webserver, this may not always be the best solution.  In the event of a cybersecurity incident, these resources may become unavailable.  Finding an external source to host your feed data may work better.

## Benefits of Hosting on Github
Hosting feed data on Github comes with a number of benefits:
* A robust infrastructure that is highly available
* Git Repositories allow custom automation of updates and synchronization to the GitHub Repository.
* File Versioning and Change History, coupled with commit messages to identify why updates were added
* Private repositories can prevent external entities from accessing your feeds to gain insight into any potential compromise that you may be dealing with, or have dealt with in the past.
* [Fine-Grained Personal Access Tokens](https://docs.github.com/en/rest/overview/permissions-required-for-fine-grained-personal-access-tokens?apiVersion=2022-11-28) allow for secure authentiation and authorization to specfic Private respositories.

To create a new fine-grained Personal Access Token, login to your GitHub Account, and then:

1. Click your profile picture from the upper right corner
2. Click Settings
3. Scroll to the bottom of the navigation pane on the left and click **<>Developer Settings**
4. Click **Personal Access Tokens** from the navigation pane
5. Select **Fine-Grained Tokens**
6. Click **Generate New Token**

### Note:
Ensure that the Fine-Grained PAT has **Read** access to content and metadata for the repository that will host your Threat Feed Data.

## Configure Fortigate to Authenticate using a Personal Access token (PAT)

As Mentioned above the Fortigate only supports Basic Authentication when connecting to a an external threat feed. The key to this is to configure the fortigate to pass the "Authorization" header along with the PAT.  This can be done via the FortiOS CLI.

**Configure an External threat Feed from the CLI**
```console
config system external-resource
    edit <name>
        set status {enable | disable}
        set type {category | address | domain | malware}
        set category <integer, 192-221>
        set update-method {feed | push}
        set username <string>
        set password <string>
        set comments <string>
        *set resource <resource-uri>
        set user-agent <string>
        set server-identity-check {none | basic | full}
        set refresh-rate <integer>
        set source-ip <ip address>
        set interface-select-method {auto | sdwan | specify}
    next
end
```
Set the resource equal to the URL of the raw data file in your private GitHub repository. The "Authorization" header can be appended to the user-agent string using \r\n between them as follows

```console
config system external-resource

edit "Malicious_Domains"

set type=domain

set resource = "https://raw.githubusercontent.com/GitHubAcct/ThreatFeeds/main/Domains.txt"

set user-agent "curl/7.58.0\r\nAuthorization: token github_pat_11N0TR3A1h576ekyM_xQqR6XlNOASDFsdfasdgjn3w3B0Gu5K3y5wYLblX8b7R25DPVL9ZsH51AK"

End
```

I hope to put together a quick tutorial video and adding it to my [YouTube Channel](https://youtube.com/granitedan) soon.  Once this is done I will add a link here as well.