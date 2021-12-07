---
title: "How to assess and define a default connectors group for Power Platform data policies?"
subtitle:
excerpt: Microsoft regularly publishs new connectors in Power Platform, either native or from partners.    
tags:
  - DLP
  - Hardening
  - Power Apps
  - Power Automate
  - Power Platform
header_img : "./assets/img/posts/2021-12-07_PowerPlatform-Default-Data-Group_1.png"
---



While preparing for my December 15 aMS Online session on "Power Platform Hardening: Everyone Affected?", I remembered that Microsoft regularly adds new connectors from its partners into the Power Platform (mainly for Power Automate and Power Apps). 

Seven connectors have indeed appeared in the non-business group of my default policy. 

<img src="https://thijoubert.github.io/assets/img/posts/2021-12-07_PowerPlatform-Default-Data-Group_1.png" >

How to manage the emergence of these new connectors that can jeopardize the security of the tenant?
{: #myid .alert .alert-info .p-3 .mx-2 mb-3}

Microsoft provides some guidance to [monitor new connectors within Power Platform](https://docs.microsoft.com/en-us/power-platform/guidance/adoption/new-connectors), but it is only reactive. 


# A small reminder on the Power Platform connectors 
As a reminder**, data policies make it possible to define groups of connectors** (and therefore of data sources or processes) **authorized to communicate**. Since last year, a policy can contain three groups of connectors: 

- Business connectors
- Non-business connectors : 
- Blocked connectors 

Although it is not the focus of this article, I think it is important to reiterate that the **names "business" or "non-business" do not have a concrete meaning**. Putting all Office 365 connectors (and therefore all business data) in the non-business group and all others in the business group is the same as doing the opposite. 

**The blocked connectors are actually blocked**. Unfortunately, native Office 365 connectors cannot be blocked. 



# What is the issue related to the Default group? 
When we write a new data policy, we assign the connectors to one of the three groups. It's rather straight to the point! 

But what happens to the new connectors? It would be better to avoid inappropriate connectors appearing where my business connectors are. 


## Setting of the default group in the UI
For each data policy, a "Set default group" parameter allows you to define the category in which the new connectors will arrive. By default, this parameter is set to "non-business". 


<img src="https://thijoubert.github.io/assets/img/posts/2021-12-07_PowerPlatform-Default-Data-Group_2.png" >

To be safe**, it is recommended to set the slider to "Blocked".** A new native Office 365 connector would however be positioned in "Non-business".

The question then becomes, "**If I want to change a large number of policies or analyze existing settings without looking at the settings one by one, how do I do that?**"


## Initial thoughts
My first idea was to look in PowerShell at the commands to manage DLP policies: ["Get-AdminDlpPolicy" and "Set-AdminDlpPolicy"](https://docs.microsoft.com/en-us/connectors/powerplatformforadmins/?WT.mc_id=M365-MVP-5002876#create-environment-dlp-policy). Two bad surprises. 

**The first one: Get-AdminDlpPolicy does not give any information about the default group.** 


<img src="https://thijoubert.github.io/assets/img/posts/2021-12-07_PowerPlatform-Default-Data-Group_3.png" >

The second one: **Set-AdminDlpPolicy allows today to set only the values "hbi" and "lbi":** 

- Hbi for High business impact = Business data
- Lbi for Low business impact = Non-business dat

<img src="https://thijoubert.github.io/assets/img/posts/2021-12-07_PowerPlatform-Default-Data-Group_4.png" >

<img src="https://thijoubert.github.io/assets/img/posts/2021-12-07_PowerPlatform-Default-Data-Group_5.png" >

So we need to find another solution. From what I have seen, [the Center of Excellence (aka CoE)](https://github.com/microsoft/powerapps-tools/tree/master/Administration/CoEStarterKit) does not provide an answer to this problem either. 

<img src="https://thijoubert.github.io/assets/img/posts/2021-12-07_PowerPlatform-Default-Data-Group_6.png" >




# An answer: the Power Platform API 
By unboxing the Powershell commands "Get-AdminDlpPolicy" and "Set-AdminDlpPolicy" on [PowerShellGallery](https://www.powershellgallery.com/packages/Microsoft.PowerApps.Administration.PowerShell/2.0.63/Content/Microsoft.PowerApps.Administration.PowerShell.psm1), we can see that **Microsoft provides APIs that bring a solution**. 

The script below allows to retrieve the desired value. 

```powershell
# Initialization 
$PolicyName = "8c48feb0-b56e-4894-8088-132430253b5d"
$PolicyDisplayName = "000-Test"
$ApiVersion = "2016-11-01"
$route = "https://api.bap.microsoft.com/providers/Microsoft.BusinessAppPlatform/scopes/admin/apiPolicies/$($PolicyName)?api-version=$($Apiversion)"

# Get the default group
$policy = InvokeApi -Method GET -Route $route -ApiVersion $ApiVersion
$policy.properties.definition.defaultApiGroup 
```


<img src="https://thijoubert.github.io/assets/img/posts/2021-12-07_PowerPlatform-Default-Data-Group_6.png" >

To define a new value, it is the same thing via the command below. 

```powershell
# Set a new valuee for the default group setting 
$defaultApiGroup = "blocked" #hbi, lbi, blocked
$policy.properties.definition.defaultApiGroup = $defaultApiGroup 
InvokeApi -Method PUT -Route $route -Body $policy -ApiVersion $ApiVersion
```


<img src="https://thijoubert.github.io/assets/img/posts/2021-12-07_PowerPlatform-Default-Data-Group_8.png" >

These two little scripts can be easily generalized to check and correct the default group value for the organization's data policies.

if you know a cleaner way to do this, I would be very interested!
