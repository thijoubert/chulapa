---
title: "How to avoid uncontrolled (re)sharing in OneDrive for Business?"
subtitle:
excerpt: By default, in OneDrive a user with **Edit permissions on content can in turn (re)share it to other people**, who can in turn re-share it to a third person...  
tags:
  - Microsoft 365
  - OneDrive for Business
  - Sharing
  - Governance
header_img : "./assets/img/posts/2021-10-05_ResharingOneDrive_3.jpg"
---


I regularly share a document from my OneDrive (e.g., an unfinished blog post to get a review). And like most users, I share my content with Edit permissions... **Not always a good idea!** 

Indeed, by default in OneDrive, any user with Edit permissions can in turn (re)share it to other people, who can in turn re-share it to a third person... The spread of data can become uncontrollable. 

The OneDrive owner may be faced with a fait accompli and **not know the reasons behind a sharing link when reviewing permissions (if there is a review...).** 

In order to limit endless sharing chains, OneDrive offers several settings. In my experience, these features are not used enough, even though they are simple. This is the same problem [I mentioned for SharePoint](https://www.thijoubert.com/2021-09/Prevent-Uncontrolled-Resharing-SharePoint/), but the remediation possibilities are slightly different. 



# Prevent guests to (re)share item they don’t own

The first thing to do is to **prevent external users** (SharePoint or Azure AD B2B guests) from (re)sharing content they don't own. 

This setting is done in the SharePoint admin center and covers **all SharePoint sites and OneDrive**.

By default, guests can share items they do not ownthe box is checked. 

<img src="https://thijoubert.github.io/assets/img/posts/2021-10-05_ResharingOneDrive_1.png" >

In practice, a guest will not be able to create a new sharing link **if he has not been given explicitly the "Full Control" permission** on the content or container.

On the screenshot below, we can see that he only has the right to share the content to people who already have access. 

<img src="https://thijoubert.github.io/assets/img/posts/2021-10-05_ResharingOneDrive_2.png" >

# Prevent internal users to (re)share items from another user's OneDrive 

The next step is to define whether an internal user can (re)share content from another user. Several scenarios are possible: 
- A user can decide to allow or not the sharing for his own OneDrive.
- An administrator can force a configuration for the whole organization.
- An administrator can change a configuration for a user.


## Whare are the settings available ? 

At the level of each OneDrive, two settings determine the conditions under which another user can access and (re)share content:

**"Allow members to share the site and individual files and folders:** 
- If this box is checked (yes by default), a user who has Edit rights to content will be able to (re)share it.
- If this box is unchecked, only the owner will be able to create share links for the data on their OneDrive.

**"Allow access requests":**
- If this box is checked (yes by default), a user who has Read rights will be able to suggest to the owner to grant permissions to a third person. A user who does not have access rights can request the access to the owner (or to the person indicated)
- If this box is not checked, a user who does not have access will simply see an error message telling him that he does not have access.


<img src="https://thijoubert.github.io/assets/img/posts/2021-10-05_ResharingOneDrive_3.png" >

These settings are available in the old OneDrive settings: "OneDrive settings > More Settings > Return to old Site settings Pages > Site permissions > Access Request level settings". 

By default, each owner can change the values which are very permissive.
{: #myid .alert .alert-info .p-3 .mx-2 mb-3}


## What possibilities for administrators? 

Unlike SharePoint sites, **it is possible to block (re)sharing at the tenant level for all OneDrive by non-owners**. 

For both settings shown above, a Global Administrator or SharePoint Administrator can force a value: 
- **"On"**: Enabled for all OneDrive. A user cannot override the set value. 
- **"Off":** Disabled for all OneDrive. A user cannot override the set value.
- "**Unassigned":** The value is left to the user's hand.

This configuration is done with the [PowerShell cmdlet](https://docs.microsoft.com/en-us/powershell/module/sharepoint-online/set-spotenant?view=sharepoint-ps):

```powershell
# Authorize users to share items from another user's OneDrive (FOR ALL ONEDRIVE)
Set-SPOTenant -ODBMembersCanShare Off #On, Off, Unspecified

# Authorize users to request access or sharing to another user's OneDrive (FOR ALL ONEDRIVE)
Set-SPOTenant -ODBAccessRequests Off #On, Off, Unspecified
```


If an administrator selects “On” or “Off”, a message in red will appear stating that the setting is managed at the organization level. The box will be grayed out. 

<img src="https://thijoubert.github.io/assets/img/posts/2021-10-05_ResharingOneDrive_4.png" >

<img src="https://thijoubert.github.io/assets/img/posts/2021-10-05_ResharingOneDrive_5.png" >


In case the values are left at "Unspecified**" an admin can disable (re)sharing and access requests for a specific OneDrive** via the [cmdlet PowerShell](https://docs.microsoft.com/en-us/powershell/module/sharepoint-online/set-sposite?view=sharepoint-ps):

```powershell
Set-SPOSite -Identity siteurl -DisableSharingforNonOwners
```

Note that an administrator will not be able to change the value of (re)sharing or access requests if they are configured at the organization level:

<img src="https://thijoubert.github.io/assets/img/posts/2021-10-05_ResharingOneDrive_6.png" >

After applying this command, we can see that both parameters have been disabled:

<img src="https://thijoubert.github.io/assets/img/posts/2021-10-05_ResharingOneDrive_7.png" >

A user can then modify these values. 

Two scenarios can be considered to improve the security posture: 
- Block (re)sharing for all users but leave access requests available.
- Disable (re)sharing for all users but let users change their settings if they want to. 
{: #myid .alert .alert-info .p-3 .mx-2 mb-3}

# How to track if values are changed for a OneDrive? 

In case one of the values is modified by a user or an administrator, **a log is generated**: 
- Modified 'Members Can Share' setting
- DisableSharingForNonOwners
- Modified access request setting

<img src="https://thijoubert.github.io/assets/img/posts/2021-10-05_ResharingOneDrive_8.png" >

Note that, as for SharePoint sites, it is not possible de get the status of access requests and (re)sharing through Graph API or PowerShell. 



# To conclude

We have seen in this blog post how to prevent users from creating endless and uncontrolled sharing chains in OneDrive. 

It is essential to take a stand at the organizational level and to make users aware of these two parameters (sharing and request access) in order to help them control their personal space
