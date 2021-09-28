---
title: "How to avoid uncontrolled (re)sharing in SharePoint Online?"
subtitle:
excerpt: In the case of long-term consulting or outsourcing assignments (e.g. IT support), it is not uncommon to give an Office 365 user account to allow to communicate and collaborate in the course of their duties. 
tags:
  - Microsoft 365
  - SharePoint Online
  - Sharing
  - Governance
header_img : "./assets/img/posts/2021-09-27_ResharingSharePoint_0.jpg"
---



By default, in SharePoint, a user with **Edit permissions on content can in turn (re)share it to other people**, who can in turn re-share it to a third person... The spread of data can then become **uncontrollable**. 

This poses an obvious problem of **potential data leakage** to external users, but also of a **clear lack of governance** leading to loss of control of information within the organization

In order to limit endless sharing chains, SharePoint offers several settings. In my experience, **these features are not used enough, even though they are simple**:  

- For a site, an owner can decide **who can share a file, a folder, or the site**.
- For a site, an administrator can decide to **block all sharing/re-sharing mechanisms for non-owners**.

Currently, it is not possible to block (re)sharing at the tenant level for SharePoint Online (except for guest users). This must be managed at the file level or site by site depending on the context and the sensitivity of the data.

Please note that I will not detail here the SharePoint permissions which allow to go further, but are generally too complex for general cases.  
{: #myid .alert .alert-info .p-3 .mx-2 mb-3}


# Site-level permissions management by an owner

A site owner can decide to restrict who can share the site or the content. There are [three options](https://support.microsoft.com/en-us/office/set-up-and-manage-access-requests-94b26e0b-2822-49d4-929a-8455698654b3): 
- **Option 1:** Site owners and members can share files, folders, and the site. People with Edit permissions can share files and folders (least restrictive - default setting).
- **Option 2:** Site owners and members, and people with Edit permissions can share files and folders.
- **Option 3:** Only site owners can share files, folders, and the site (most restrictive). 
"Sharing the site" here means adding a user to one of the default groups "Members" (Edit right) and "Visitors" (Read right) on the whole site. 

Note here that sharing lists follows the same behavior as sharing files and folders.


Beyond whom can share content or the site, **an owner can allow or disallow access requests**. This feature has two interests: 
- On the one hand, to allow **users in possession of a link but not authorized to access the content** to request access (Options 1, 2 and 3).
- On the other hand, to allow users who have access to content to **request access** for other users (Users with Read permissions for Option 1 and Users with Read or Write permissions for Option 2 and 3).
In cases where only owners should be able to share, it could be interesting to manage access and rights in a more detailed way with SharePoint permissions. 

These features are configured via "Settings > Site Permissions > Change how members can share".  

<img src="https://thijoubert.github.io/assets/img/posts/2021-09-27_ResharingSharePoint_1.png" >

<img src="https://thijoubert.github.io/assets/img/posts/2021-09-27_ResharingSharePoint_2.png" >

The above screenshots represent the new modern interface. The historical settings accessible via "Site Settings > Site Permissions > Access Request Settings" are identical (see screenshot below):  

<img src="https://thijoubert.github.io/assets/img/posts/2021-09-16_ResharingSharePoint_3.png" >

Unfortunately, these features are not yet supported by the sensitivity labels. This would allow adding limits on sharing in the protection templates of collaborative spaces. Let's hope Microsoft will add this to the roadmap. 
{: #myid .alert .alert-info .p-3 .mx-2 mb-3}


# Disabling sharing for a non-owner user by an administrator

A SharePoint Online administrator or a Global Administrator can also prevent (re)sharing. This is done via the [PowerShell cdmlet](https://docs.microsoft.com/en-us/powershell/module/sharepoint-online/set-sposite?view=sharepoint-ps) below: 

```powershell
Set-SPOSite -Identity siteurl -DisableSharingforNonOwners
```

<img src="https://thijoubert.github.io/assets/img/posts/2021-09-27_ResharingSharePoint_5.png" >

Before performing this action, it is necessary to have the following limitations in mind: 
- This action is **not reversible today in PowerShell**, an owner will have to modify the configurations manually in the GUI.
- This action disables content sharing, site sharing **but also access requests, without any granularity**.

In my tenant, the DisableSharingForNonOwnerStatus value remains empty after modification by an owner or via PowerShell. An immediate consequence is that it is not possible to report on sharing limitations.  
{: #myid .alert .alert-info .p-3 .mx-2 mb-3}

It is important to have in mind that this cmdlet is a **one shot action**: a owner could override the configuration if he wants to. 


# What is the end-user experience?

In the case where the owners have left the hand to the members to (re)share (Options 1 and 2), any user in possession of a sharing link will be able to (re)share it in turn: 
- A non-member user of the site, receiving a link with "Allow editing" **will be able to share the content to other users**.
- A non-member of the site, receiving a link without "Allow editing" **will only be able to request access for specific people whitout "Allow editing"** (if access requests are enabled).


As a reminder, the different sharing options are as follows: 

<img src="https://thijoubert.github.io/assets/img/posts/2021-09-27_ResharingSharePoint_6.png" >

In the case where owners do not allow members to (re)share (Option 3): a user who is a member or not of the site and has access to the content will only be able to **request access for specific people whitout "Allow editing"**, if access requests are enabled. 


**I have summarized all the behaviors in the following matrix:**

<img src="https://thijoubert.github.io/assets/img/posts/2021-09-27_ResharingSharePoint_7.png" >

To clarify the user experience, let's take three different concrete scenarios from the matrix.



## Scenario A: Access requests are enabled and a user with read (option 1 or 2) or write/read (scenario 3) permissions, Orc, wants to share a document with a new person, Dwarf

Orc can only create a sharing link for people with existing access or request access for specific people:

<img src="https://thijoubert.github.io/assets/img/posts/2021-09-27_ResharingSharePoint_8.png" >

<img src="https://thijoubert.github.io/assets/img/posts/2021-09-27_ResharingSharePoint_9.png" >

After clicking on "copy link" or "send link", user is **automatically notified** that an approval is required from a site owner. 

<img src="https://thijoubert.github.io/assets/img/posts/2021-09-27_ResharingSharePoint_10.png" >

The owners of the site **automatically receive an email notification** inviting them to validate or not the sharing:

<img src="https://thijoubert.github.io/assets/img/posts/2021-09-27_ResharingSharePoint_11.png" >

A user who does not (yet) have access to the content, Dwarf for example, **can request access** if he tries to access the document directly:

<img src="https://thijoubert.github.io/assets/img/posts/2021-09-27_ResharingSharePoint_12.png" >




## Scenario B: Access requests are disabled and a user with read (option 1 or 2) or write/read (scenario 3) permissions, Orc, wants to share a document with a new person, Dwarf

**Sharing is only possible with users who already have access**. Orc can't share his document with Dwarf.

<img src="https://thijoubert.github.io/assets/img/posts/2021-09-27_ResharingSharePoint_13.png" >

On his side, the other user, Dwarf, will see an **error message** if he tries to access directly to the url : 

<img src="https://thijoubert.github.io/assets/img/posts/2021-09-27_ResharingSharePoint_14.png" >



## Scenario C : a user, Orc, with read permissions (option 1) or with read / write permissions (option 2 and 3) wants to add a user, Dwarf, to the whole site

If he tries to share the site, Orc will get an **error message**:

<img src="https://thijoubert.github.io/assets/img/posts/2021-09-27_ResharingSharePoint_15.png" >

<img src="https://thijoubert.github.io/assets/img/posts/2021-09-27_ResharingSharePoint_16.png" >

It will also **not be able to add members to the Microsoft 365 Group** (in the case of a team site) either in SharePoint, Azure AD, or Teams: 

<img src="https://thijoubert.github.io/assets/img/posts/2021-09-27_ResharingSharePoint_17.png" >



# To conclude

We have seen in this blog post how to prevent users from creating endless and uncontrolled sharing chains. These features, although basic, are worth a reminder to SharePoint site owners.  

However, it is not necessary to activate them for all use cases: limiting sharing for storage sites can be interesting, but much less useful for a non-confidential internal project with many people.

Limiting sharing is an essential brick in the construction of data governance. But it must be combined with the implementation of a life cycle for spaces and guests, the review of external sharing, data loss prevention, etc. 
