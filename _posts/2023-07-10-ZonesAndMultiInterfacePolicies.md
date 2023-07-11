---
title: FortiGate Security Zones vs. Multi-Interface Policies
date: 2023-07-10 17:30:00 -300
categories: [fortinet,firewallpolicies,networksecurity]
tags: [zones,fortigate,firewallpolicies] 
---

# FortiGate Security Zones vs. Multi-Interface Policies
Often times, when posting information, screenshots or answers to questions on forums, or social media sites. I have gotten negative feedback from people when they notice that I have Multi-Interface policies enabled, and in use. The reaction is usually in the form of a bold statement, and rarely in the form of a question.  So in the wise words of Ted Lasso... ["Be curious, not judgmental"](https://www.youtube.com/watch?v=oZ4YSXv6Xkg)

This article will examine the pro's and con's of configuring the Security Zones, and Multi-interface Policies.  And why I believe that they are both valuable in the right circumstances.

## An Overview of Zones
### What is a Zone?
Zones are for lack of better terms a grouping interfaces that are considered the be the same security context.

### Benefits of Interface Grouping
Once an interface is added to a zone on a fortigate, all policies etc, have to reference the zone name.  This is convenient becuase firewall policies then encompass every interface that is a member of the zone. This grouping then allows for less policy duplication.

### Can a zone contain only a single interface?
Absolutely, a zone can contain only a single interface, and often this is something that is done on purpose when designing a fortigate firewall configuration. Wrapping a every interface in a zone, allows for flexibility moving forward.  Interfaces can be added or removed from zones at will. And because all policy references point to the zone name rather than an individual interface, there is additional flexibility if you want to delete, replace, or recreate VLAN interfaces without first removing all firewall policies.

## What is a multi-interface policy and how do I use them?

Mulit-interface policies are quite simply firewall policies that have more than one source or destination interface configured.  By default this is an optional feature that must be enabled, either in the cli, or the feature visability section of the Fortigate Web UI.

**Enable Multi-Interface Policies via CLI**
```console
config system settings
     set gui-multiple-interface-policy enable
end
```
### Why bother if I can just use zones to include multiple interfaces in polices?
The issue with using zones is that once more than one interface is included in a zone, you loose granularity.  Every interface in a zone will always and forevermore be welded together from a security perspective.  You are never able to use allow only or member of the zone to pass traffic that other member interfaces can't pass.  It is true that you can limit based on source or destination address, but that is not always easy.  And may make troubleshooting more difficult.