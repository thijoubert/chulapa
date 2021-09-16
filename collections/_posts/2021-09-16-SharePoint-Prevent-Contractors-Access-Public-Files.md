---
title: "How to prevent contractors with Office 365 internal account from accessing public files ?"
subtitle:
excerpt: In the case of long-term consulting or outsourcing assignments (e.g. IT support), it is not uncommon to give an Office 365 user account to allow to communicate and collaborate in the course of their duties. 
tags:
  - Microsoft 365
  - SharePoint Online
  - External sharing
header_img : "./assets/img/posts/2021-09-16_PreventContractorsToAccessPublicFiles_0.jpg"
---


As coincidence has its way, I was asked twice last week how to **ensure that contractors with internal user accounts cannot access organization's files**, without explicitly having the right to do so. 

In the case of long-term consulting or outsourcing assignments (e.g. IT support), it is not uncommon to give an Office 365 user account to allow to communicate and collaborate in the course of their duties. 

However, by construction, any internal Office 365 user account is able to : 
- Join a public Teams team 
- Access a public SharePoint site
... and this logically raises confidentiality issues. 

<img src="https://thijoubert.github.io/assets/img/posts/2021-09-16_PreventContractorsToAccessPublicFiles_1.png" >

Two ideas may be interesting: **Information Barriers** and **SharePoint permissions**. 

Note that a guest account could meet the need, if the limits in terms of security and functionalities are accepted (e.g. no corporate messaging in particular and more limited controls on the user's identity chain).
{: #myid .alert .alert-info .p-3 .mx-2 mb-3}


# Information Barriers 

[Information Barriers is an advanced compliance option in Office 365 offered by Microsoft](https://docs.microsoft.com/en-us/microsoftteams/information-barriers-in-teams), allowing to restrict communication and collaboration between groups of users.

I have described in two previous articles [how to set up Information Barriers](https://www.thijoubert.com/2021-07/InformationBarriers-Feedback-from-the-field-1-2/) and [what the user experience was for SharePoint](https://www.thijoubert.com/2021-08/InformationBarriers-OneDrive/). 

Information Barriers can be an interesting option if you want to limit the communication and collaboration possibilities of a contractor in a defined area.

However: 
- It requires some Office 365 E5, Microsoft 365 E5 or Microsoft E5 Insider Risk Management licenses
- The concerned users will no longer be able to communicate with other users, which can be too restrictive in the case of IT support outsourcing, for example.



# Management of SharePoint permissions

The second approach allows contractors to communicate with all members of the organization but prevents them from accessing public spaces without being explicitly granted the right. 

It is simply a **matter of taking back control** of SharePoint permissions **without breaking the bank**.

Some reminders about SharePoint permissions

By default, a SharePoint site comes with a number of groups with predefined rights. It is possible to change these groups and associated permissions, but this is not strongly recommended without a clear information sharing architecture. 


For a "communication site" there are three default groups: 
- Owners (Full control): Owners of the site
- Members (Edit) : Empty
- Visitors (Read) : Empty

For a private "team site" (based on a Microsoft 365 Group and linked or not to a Team), there are also three groups but populated differently: 
- Owners (Full control) : Owners of the Microsoft 365 Group
- Members (Edit) : Members of the Group Microsoft 365
- Visitors (Read) : Empty


For a public "team site" (based on a Group Microsoft 365 and linked or not to a Team): 
- Owners (Full control) : Owners of the Microsoft 365 Group
- Members (Edit) : Members of Group Microsoft 365 and "Everyone except external users
- Visitors (Read) : Empty

"Everyone except external users" and "Everyone" are [security groups native to SharePoint](https://docs.microsoft.com/en-US/office365/troubleshoot/access-management/grant-everyone-claim-to-external-users). Here, external users mean Azure AD B2B guests, not a provider with an internal account). 
{: #myid .alert .alert-info .p-3 .mx-2 mb-3}

These groups cannot be modified. However, they can be hidden so that a user cannot share an item to one of these groups. 
{: #myid .alert .alert-info .p-3 .mx-2 mb-3}

The challenge is to slightly modify the public sites / Microsoft 365 Groups so that they are accessible ONLY by internal users.


## Solution 

In order to answer the original question of preventing a contractor from accessing public spaces, simply follow these steps: 

1. Create a security group with all internal users
1. Remove the "Everyone except external users" group from the "Members" permission group
1. Hide the "Everyone except external users" group
1. Adding the new group to the "Members" permission group
1. Test that all is well


### 1. Create a security group with all internal users

For the demonstration, I created a provider "Z - Contractor" with the CompanyName field set to "Contactor". 

<img src="https://thijoubert.github.io/assets/img/posts/2021-09-16_PreventContractorsToAccessPublicFiles_2.png" >

The first step is to create a security group in Azure AD with all internal users only. 

<img src="https://thijoubert.github.io/assets/img/posts/2021-09-16_PreventContractorsToAccessPublicFiles_3.png" >

The ideal is to create a **dynamic group with all the internal users only**. Here, this means getting all members except users with a CompanyName field equal to "Contractor".  

<img src="https://thijoubert.github.io/assets/img/posts/2021-09-16_PreventContractorsToAccessPublicFiles_4.png" >

A best practice is to check that the membership rules dynamics are working well. For this, I test that a guest and a contractor are not part of the group. 

<img src="https://thijoubert.github.io/assets/img/posts/2021-09-16_PreventContractorsToAccessPublicFiles_5.png" >



### 2.  Remove the "Everyone except external users" group from the "Members" permission group

On my SharePoint "Intranet" site, I removed the "Everyone except external users" group from my "Intranet Members" permission group.

<img src="https://thijoubert.github.io/assets/img/posts/2021-09-16_PreventContractorsToAccessPublicFiles_6.png" >



### 3.  Hide the "Everyone except external users" group

Since I don't want an internal user to **mistakenly share content with a contractor** via the "Everyone except external users" group, I hide it via the [commande PowerShell ](https://docs.microsoft.com/en-us/powershell/module/sharepoint-online/set-spotenant?view=sharepoint-ps): 

Set-SPOTenant -ShowEveryoneExceptExternalUsersClaim $false



### 4.  Adding the new group to the "Members" permission group
I then add the newly created group to the relevant permission group:


<img src="https://thijoubert.github.io/assets/img/posts/2021-09-16_PreventContractorsToAccessPublicFiles_7.png" >

<img src="https://thijoubert.github.io/assets/img/posts/2021-09-16_PreventContractorsToAccessPublicFiles_8.png" >


### 5. Test that all is well
In order to validate the behavior, I verify that my "Z - Contractor" account cannot access the site.

<img src="https://thijoubert.github.io/assets/img/posts/2021-09-16_PreventContractorsToAccessPublicFiles_9.png" >

Everything is fine!



## Automatization
For an organization that already has hundreds or even thousands of public groups, **the above manual method is no longer sufficient and it is mandatory to automate this**. 

In PowerShell, it is very simple via the [Add-SPOUser command](https://docs.microsoft.com/en-us/powershell/module/sharepoint-online/set-spositegroup?view=sharepoint-ps) as shown below: 

```powershell
$tenant = "Your tenant ID"
$site = "Your site name"
$NewMember = "Everyone except external users and contractors"
$TargetGroup = "Your site permission group"
Add-SPOUser -Site "https://dwpsecurity.sharepoint.com/sites/$site" -LoginName $NewMember -Group $TargetGroup 
```

Avant les permissions associées à notre site étaient les suivantes : 

<img src="https://thijoubert.github.io/assets/img/posts/2021-09-16_PreventContractorsToAccessPublicFiles_10.png" >

Après ajout des permissions : 

<img src="https://thijoubert.github.io/assets/img/posts/2021-09-16_PreventContractorsToAccessPublicFiles_11.png" >



# Conclusion

A contractor with an internal user account is able to access files stored on public spaces in Office 365, just like all other internal users. 

We saw in this article that a simple solution was to modify the SharePoint permissions groups defined at the site collection level, by playing with a group containing all internals.

SharePoint permissions allow you to have a very fine granularity on access and rights to content, but it is recommended not to touch them too much or you will do more harm than good.  

Note that a provider could always be added to a public group, but with a little monitoring the matter is in the bag.
