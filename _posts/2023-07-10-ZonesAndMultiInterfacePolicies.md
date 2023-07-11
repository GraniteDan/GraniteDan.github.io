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

Mutiple-Interface policies allow the consolidation of firewall policies without sacrificing flexibility.
Consider a scenario where your Firewall is the layer 3 gateway for 10 Vlans. You want to reduce the amount of policies in use, and you want all of your vlans to be able to access the internet, but only VLANs 10 and 15 should be able to pass traffic to your DMZ.  On top of that Vlans 10 and 12 require access to a secure web application hosted on a secure HR_Apps vlan (vlan id 26).  In this scenario zone grouping may not provide the granular control you require.  Enter, the multi-interface policy.

## What solution do I prefer to use?
In a new new environment I would use a combination of both. 
Using zones for multiple interfaces that are always going to be treated the same security wise, makes perfect sense. I also like to wrap individual interfaces in zones, simply for the future proofing aspects.  If I add a vlan that **truely** needs all the same policies to apply as an existing vlan (ex. Staff Wired endpoints, and Staff WiFi endpoint). If a VLAN redesign is needed for some reason, or VLAN's need to be recreated. It can be useful to have all refernces point to zones rather than interfaces.

Additionally, multi-interface polices can be used with multiple zones as srcintf and dstintf as well.  So if each interface is zoned by itself you can still use multiple interface policies to group zones together and consolidate policies.  I think this provides the most flexibility and functionality when dealing with a large number of interfaces and internal segmentation policies.

## Closing

One of the most challenging aspects of dealing with technology today is simply keeping up with the number of features that are available.  There are so many options that may not even show up in the UI/CLI by default. Some features seem to compete with other, as in this case.  My advice is to ask as many questions as possible when you run into a new feature.  Determine popular use cases, and how you could make use of it. Often times I have found that features are complementary to each other, and should not discount each other from consideration.

