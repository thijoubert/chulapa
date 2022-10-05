---
title: "How to disable company links within Office 365 ?"
subtitle:
excerpt: "Restricting the use of company links can be useful for very sensitive sites to limit wide sharing done accidentally."    
tags:
  - Microsoft 365
  - Office 365
  - OneDrive for Business
  - SharePoint Online
  - Sharing
header_img : "./assets/img/posts/2022-10-05_Disable-Company-Links_3.png"
---

*The great thing about working on collaborative platforms like Microsoft 365 is that there are always new things coming up. And therefore every day more things to learn and share.* 

Except that last week... I discovered a basic feature that was unknown to me and that would have been very useful for previous missions. To my great surprise, I saw that it was possible to disable company links (i.e. OneDrive for Business and SharePoint Online share links that can be accessed by anyone in the company). 

This feature is obviously not new, as the history of the "[Get-SPOSite](https://learn.microsoft.com/fr-fr/powershell/module/sharepoint-online/get-sposite?view=sharepoint-ps)" page already mentions it in 2016. However, I couldn't find any documentation outside of PowerShell and the CSOM documentation. So it must be a SharePoint on-premises feature, migrated to SharePoint Online but not many people have detailed in Online Cloud version... 

Restricting the use company links can be interesting for particularly sensitive sites, where you want to prevent users from inadvertently sharing an item with too large an audience.

From what I've seen, only a SharePoint service administrator (or a Global Administrator) will be able to act on such and such a site. 




# How to disable company links for a site? 

As you can see in the image below, the default link for a SharePoint site in my tenant is a company link.  

<img src="https://thijoubert.github.io/assets/img/posts/2022-10-05_Disable-Company-Links_1.png" >

With the PowerShell cmdlet "Set-SPOSite" you can modify the attribute DisableCompanySharingLinks (accepted values: Unknown, Disabled, Enabled). 

<img src="https://thijoubert.github.io/assets/img/posts/2022-10-05_Disable-Company-Links_2.png" >

As soon as this value is changed, the company link is no longer available. 

The default link becomes "Specific people".	

<img src="https://thijoubert.github.io/assets/img/posts/2022-10-05_Disable-Company-Links_3.png" >


## Can a user (Owner or Site Administrator) act on this setting? 

From what I have seen, no! I did not see a corresponding setting in the site settings. 

The DisableCompanyWideSharingLinks parameter seems to exist in PowerShell Pnp. But this one seems to be deprecated, as it has no effect. 

<img src="https://thijoubert.github.io/assets/img/posts/2022-10-05_Disable-Company-Links_4.png" >



## Is it possible to disable company links for the whole company? 

From what I've seen, no! The settings of the tenant configurable through Get-SPOTenant and Set-SPOTenant do not indicate anything like that.

<img src="https://thijoubert.github.io/assets/img/posts/2022-10-05_Disable-Company-Links_5.png" >



# How to report on this feature? 

Here again, the possibilities are very limited: 
- Graph API does not contain this type of attribute. 
- The [SharePoint](https://learn.microsoft.com/fr-fr/previous-versions/office/sharepoint-csom/mt642957\(v=office.15\)) CSOM APIs should be able to meet this need, but I haven't tried. 
- PowerShell only returns this information for a given site. If no Identity is specified, the default value is returned, i.e. "Unknown".

Even when using the "Filter" and "Limit" parameters, as recommended in the documentation, the value is still the default. 

<img src="https://thijoubert.github.io/assets/img/posts/2022-10-05_Disable-Company-Links_6.png" >


<img src="https://thijoubert.github.io/assets/img/posts/2022-10-05_Disable-Company-Links_7.png" >

*In conclusion, it is possible to disable company links for a given site. This action can only be done by a SharePoint Administrator, not by an end user. Since the reporting is limited for this attribute, I would recommend to limit this action to well-identified sensitive sites.*  
