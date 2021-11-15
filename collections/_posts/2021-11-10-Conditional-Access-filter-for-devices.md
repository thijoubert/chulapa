---
title: "A first look at the new Conditional Access condition : Filter for devices"
subtitle:
excerpt: Microsoft just released a new condition for Conditional Access policies to improve the control over the devices authorized to access the tenant.   
tags:
  - Azure AD
  - Conditional Access
  - Microsoft 365
  - Security
header_img : "./assets/img/posts/2021-11-10_Conditional-Access-filter-for-devices_1.png"
---

****

One of my favorite features within Microsoft 365 is Conditional Access. I haven't blogged much about this yet, but it's coming soon. 

At Ignite, Microsoft announced the General Availability of a new condition: "filter for devices" which is actually the merger of "Device state (preview)" and "Filter for devices (preview)". 

<img src="https://thijoubert.github.io/assets/img/posts/2021-11-10_Conditional-Access-filter-for-devices_1.png" >

I find this new set of conditions very interesting because it will bring much more granularity in managing access on a per endpoint and per population basis. 

Of course, this is only a general context check. A user with knowledge of the verified criterias could very well circumvent the measure by renaming the workstation or by using this or that version of an OS. But one can also consider interesting quickwins like : 
- Restricting administrator access to names (without having to resort to MDM compliance)
- Restriction of workstations from a population that cannot use strong authentication to certain types of workstations (whether or not they are included in a specific geographical area)
- Restriction of workstations 
- Obsolete OS version blocking for unmanaged BYOD. 

The different filters available are:  

|Attribute|Description|
| :- | :- |
|deviceId|GUID of the device|
|displayName|Display name of the device|
|deviceOwnerhip|"Personal" or "Company"|
|isCompliant|"True" or "False"|
|manufacturer|Manufacturer of the device|
|mdmAppId|Application identifier used to register device into MDM|
|model|Model of the device|
|operatingSystem|Name of the operating system of the device|
|operatingSystemVersion|Version of the operating system of the device|
|physicalIds|Physical id of the device|
|profileType|"RegisteredDevice", "SecureVM", "Printer", "Shared", "IoT" |
|systemLabels|List of labels applied to the device by the system|
|trustType|"AzureAD", "ServerAD", "Workplace" |
|extensionAttribute1-15|Custom attributes|

The complete list of available operators and properties [is described in the documentation](https://docs.microsoft.com/en-us/azure/active-directory/conditional-access/concept-condition-filters-for-devices#supported-operators-and-device-properties-for-filters).
{: #myid .alert .alert-info .p-3 .mx-2 mb-3}

Note 1: the "Filter for devices" conditions cannot be used with the preview "Device state" conditions

Note 2: The maximum length for the filter rule is 3072 characters.



# Enough theory 

In order to test this feature, very promising on paper, I created a policy blocking the access to my user "Elf" from my company workstation "PO44xxxx" on a web browser. 

The policy is defined as follows:

<img src="https://thijoubert.github.io/assets/img/posts/2021-11-10_Conditional-Access-filter-for-devices_2.png" >

After implementing the policy, I tried to connect via my favorite browser (Chrome).

**First surprise**: I passed through the conditional access policy without any problem.

In the logs, I see that my terminal is not analyzed (Device: Unknwown). 

A closer reading of the documentation tells me that not all operators are supported for all device registration states. 
{: #myid .alert .alert-info .p-3 .mx-2 mb-3}

<img src="https://thijoubert.github.io/assets/img/posts/2021-11-10_Conditional-Access-filter-for-devices_3.png" >

I then decide to **register my enterprise workstation within my test tenant** (during sign-in in the Teams thick client) (sorry for the French screenshot 😉)

<img src="https://thijoubert.github.io/assets/img/posts/2021-11-10_Conditional-Access-filter-for-devices_4.png" >

Within two minutes, my device appears in AAD for my user "Elf". 

<img src="https://thijoubert.github.io/assets/img/posts/2021-11-10_Conditional-Access-filter-for-devices_5.png" >

In the logs, I see that the nature and information of my workstation are well known by Azure AD. 

<img src="https://thijoubert.github.io/assets/img/posts/2021-11-10_Conditional-Access-filter-for-devices_6.png" >

**Second surprise**: Except ... my user can still access the different workloads via a Brower. 

<img src="https://thijoubert.github.io/assets/img/posts/2021-11-10_Conditional-Access-filter-for-devices_7.png" >

I then try to use Edge and there: bingo! **Edge retrieves the information and the conditional access policy works** as excepted. 

My user sees this error message: 

<img src="https://thijoubert.github.io/assets/img/posts/2021-11-10_Conditional-Access-filter-for-devices_8.png" >

Sign-in logs show that the terminal has been recognized by Edge.

<img src="https://thijoubert.github.io/assets/img/posts/2021-11-10_Conditional-Access-filter-for-devices_9.png" >



# Wrap-up

We have seen in this article how to configure conditional access with the new condition "Filter for devices" (GA since early November).

I remember several things from it: 
- **Not all clients are supported for conditional access policy evaluation**. My various tests showed that at the moment Edge, PowerShell (with Azure AD module) were compatible while Safari, Chrome and Firefox were not supported.  
- As for other conditions, **it may be interesting to think more in an exclusion logic than in an inclusion logic** to force the application of the conditional access policy (e.g. block all devices, except those with a displayname starting with POxxxx)
- "Filter for devices" is a interesting condition but **the knowledge of it could enable a user to by-pass it**. 

Note for later, Continuous Access Evaluation does not support yet the "Filter for devices" condition. For example, a token could be extracted from on admin workstation and re-played on another device. Can't wait to see what Microsoft will release to handle this kind of case (via token binding, Azure AD Identity Protection or CAE). 
{: #myid .alert .alert-info .p-3 .mx-2 mb-3}