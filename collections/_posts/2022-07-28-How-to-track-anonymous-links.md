---
title: "Why do my users generate a lot of anonymous links and how to track them"
subtitle:
excerpt: "Because of a too loose configuration, a lack of mastery of the tools and attention, users create many anonymous links without being aware of it."    
tags:
  - Microsoft 365
  - Office 365
  - OneDrive for Business
  - SharePoint Online
  - Sharing
header_img : "./assets/img/posts/2022-07-28_How-to-track-anonymous-links_15.png"
---


I will to try to answer a question I've been asked three times in the last few weeks: **why do my users generate a lot of anonymous links when they don't feel like they're doing so** (when adding an attachment to an email or in a Teams conversation, for example)? How can I detect this and make sure there is no data leak through these links?

# Let's start with the beginning
The creation of share links depends on a fundamental group of settings in Office 365: the “S**haring policy**” defined in the SharePoint Online Admin Center. This policy applies regardless of the workload involved: SharePoint Online, OneDrive for Business, Microsoft Teams, Exchange Online, etc. 


## Sharing policy
<img src="https://thijoubert.github.io/assets/img/posts/2022-07-28_How-to-track-anonymous-links_1.png" >


This capture, which is certainly the most well-known image of Office 365's multiple admin portals, shows the two main default settings for sharing within the entire platform: 

- **External sharing level allowed in SharePoint Online and OneDrive for Business**: Anonymous user (default value), SharePoint or Azure AD B2B guests users, or only internal users
- **Type of links used by default**: Anyone with the link (default value), People in your organization with the link, Specific user

The combination of these two settings is used on all SharePoint sites and OneDrive users unless a specific setting is applied. 

Note: if anonymous links are not allowed for OneDrive (or for a specific SharePoint site) and the default link is "Anyone with the link", the default sharing link will be "People in your organization with the link". 
{: #myid .alert .alert-info .p-3 .mx-2 mb-3}

## Customization for a SharePoint or OneDrive site
Once the group of settings is defined, all collaborative resources (mainly SharePoint and Teams) and personal resources (mainly OneDrive) **will follow this default template**.

It will however be possible to **modify the inherited configuration, on a given OneDrive or site / Teams**. 

**If the sensitivity labels are not used to [manage the level of sharing and the default link type**](https://docs.microsoft.com/en-us/microsoft-365/compliance/sensitivity-labels-teams-groups-sites?view=o365-worldwide)**: 
- Only a SharePoint or General administrator will be able to change the level of external sharing allowed and the type of link used by default.

<img src="https://thijoubert.github.io/assets/img/posts/2022-07-28_How-to-track-anonymous-links_2.png" >


**If sensitivity labels are used to manage the default sharing level and link type of collaborative spaces**: 
- An owner of the collaborative space will be able to change the label in Teams or SharePoint UIs, and thus the links used in the resource. 
- A SharePoint or General administrator will also be able to change the label via their administration interfaces.

<img src="https://thijoubert.github.io/assets/img/posts/2022-07-28_How-to-track-anonymous-links_3.png" >


<img src="https://thijoubert.github.io/assets/img/posts/2022-07-28_How-to-track-anonymous-links_4.png" >


<img src="https://thijoubert.github.io/assets/img/posts/2022-07-28_How-to-track-anonymous-links_5.png" >




# Behavior details according the workload
In order to clarify the default behavior of link creation on the different workloads, I performed several tests on items in my OneDrive. 

The behavior will be similar for SharePoint Online, and collaborative resources relying on OneDrive. 

<img src="https://thijoubert.github.io/assets/img/posts/2022-07-28_How-to-track-anonymous-links_6.png" >



## OneDrive for Business
Sharing from OneDrive (or any of the Office applications) is quite straightforward, and is becoming more and more mastered by end-users. However, it is still necessary to pay attention to the settings of the links used. 

### Test 1: Sharing of a document from OneDrive
If a user wants to share a document from his or her OneDrive, **the default link used will be the one of the OneDrive** (“DefaultSharingLinkType” property). 

The link will be created if the user clicks on "**Send**" (a mail will be sent with the defined parameters, even if it is sent to only one user) or "**Copy**".

<img src="https://thijoubert.github.io/assets/img/posts/2022-07-28_How-to-track-anonymous-links_7.png" >



### Test 2: Modification of a sharing link from OneDrive
If the user then creates a new link for a wider or smaller audience, **the new link will be created in addition to the previous one**. 

This is how we can see up to 8 anonymous links, 4 links for the whole company, and 4 links for specific users for a single document.

<img src="https://thijoubert.github.io/assets/img/posts/2022-07-28_How-to-track-anonymous-links_8.png" >



## Microsoft Teams
Teams is being used more and more to share content from a OneDrive or SharePoint site, but end-users still don't realize that sharing is just a matter of using a OneDrive or SharePoint share links, which have a lifecycle independent of Teams.

It is important to clarify at this point that deleting a Teams message that contains a share link will not delete the share link. It will be necessary to delete the link directly from OneDrive.
{: #myid .alert .alert-info .p-3 .mx-2 mb-3}


### Test 3: Addition of a document in a Teams chat 
If a document is added from a OneDrive and shared as is in a Teams chat, **a share link is created** and sent. The link created follows the **default link type of the user's OneDrive**.  

<img src="https://thijoubert.github.io/assets/img/posts/2022-07-28_How-to-track-anonymous-links_9.png" >

Note: the behavior is identical if the document is loaded from the computer and then sent, with the difference that the document is placed in the "Microsoft Teams Chat Files" folder.  
{: #myid .alert .alert-info .p-3 .mx-2 mb-3}


### Test 4: Modification of the sharing link in a Teams chat
If the user then wishes to adapt the sharing link to his target audience, **the new link is created, regardless of whether the document is sent or not.** 

<img src="https://thijoubert.github.io/assets/img/posts/2022-07-28_How-to-track-anonymous-links_10.png" >



### Test 5: Addition of a document in Teams chat (without sending it)
If a document is added from a OneDrive into a Teams chat, without being sent or modified, **no link is created**. 

<img src="https://thijoubert.github.io/assets/img/posts/2022-07-28_How-to-track-anonymous-links_11.png" >




## Outlook
Outlook is by far the **most complex workload when it comes to sharing links and anonymous links, but it is also the most emblematic of the new collaboration modes**. 

For some time now, the recommendations in terms of collaboration have been to prefer sending sharing links rather than static documents weighing several MB each. 


### Test 6: Adding an attachment to an email
If a document is added from an email from Outlook as a share link, **a link is created**, according to the default link type used for the user's OneDrive, **whether the email is sent or not**. 

<img src="https://thijoubert.github.io/assets/img/posts/2022-07-28_How-to-track-anonymous-links_12.png" >

<img src="https://thijoubert.github.io/assets/img/posts/2022-07-28_How-to-track-anonymous-links_13.png" >


### Test 7: Modification of the sharing link in an email
If the user then wishes to adapt the sharing link to his target audience, a new sharing link is created and the default link is kept.  

<img src="https://thijoubert.github.io/assets/img/posts/2022-07-28_How-to-track-anonymous-links_14.png" >


## Synthesis of the tests

||**Workloads**|**Use case**|**Result**|
| :-: | :-: | :-: | :-: |
|Test1|OneDrive|Sharing of a document from OneDrive|Creation of a link with the default setting|
|Test2|OneDrive|Modification of a sharing link from OneDrive|Creation of a new link in addition to the existing one|
|Test3|Teams|Sharing of a document in a Teams chat|Creation of a link with the default setting|
|Test4|Teams|Modification of the link in a Teams chat|Creation of a link (regardless the document is shared)|
|Test5|Teams|Addition of a document in a Teams chat (without sending it)|No link is created|
|Test6|Outlook|Addition of an attachment in an email|Creation of a link (even if the email is not sent)|
|Test7|Outlook|Modification of the link in an email|Creation of a new link in additito of the existing one|

<img src="https://thijoubert.github.io/assets/img/posts/2022-07-28_How-to-track-anonymous-links_15.png" >



# How to hunt for anonymous share links
As soon as an anonymous link is generated, a unified audit log is created "**Created an anonymous link**". This log is available in all Purview solutions (Unified Audit Logs, Defender for Cloud Apps, Insider Risk Management, etc.). 

From what I have seen, **it is not possible to distinguish the workload used to generate a sharing link**. So it will be **complicated to know if this activity was intentional** (e.g. explicitly sharing in OneDrive) **or due to an error** (e.g. creating a new draft email). 

The only exception I have seen concerns Outlook. Indeed, the **UserAgent specified in the log details allows to distinguish Outlook from a web UserAgent** (Chrome, Safari, Edge, etc.). 


## Creating an anonymous share link in OneDrive or Teams:  

<img src="https://thijoubert.github.io/assets/img/posts/2022-07-28_How-to-track-anonymous-links_16.png" >


## Creating an anonymous share link in Outlook:  
<img src="https://thijoubert.github.io/assets/img/posts/2022-07-28_How-to-track-anonymous-links_17.png" >




# How to deal with anonymous links
The topic of anonymous links is always a hot one. I remember that my first assignments around Office 365 security regularly focused on the question, "Should anonymous links be left available to users?" 

Here, as with all cybersecurity topics, the **answer is a fine balance between risk aversion, business issues, and security**. 

Here are a few elements to help you think about it: 
- It is possible to **limit in time the anonymous links**, and especially to adapt this time limit according to the sensitivity label of the site / Teams hosting the document. 
- It is also possible to **restrict the use of certain link types to defined collaborative spaces** (either via the administration UI, PowerShell, or APIs or via sensitivity labels) **or defined users**. 
- Sharing links are different from sending documents by email, as we give **access to all future modifications and all the underlying content** in the context of a folder. 
- It is **not possible to set DLP rules** only on anonymous sharing links, but it is possible via Defender for Cloud Apps to implement file policies to monitor the use of anonymous links according to the chosen policy (for example according to the sensitivity level of the information).  
- The **default link types configuration is key to user adoption**, as users often don't pay attention to tailoring the sharing link to the target audience (either by oversight or lack of knowledge of the mechanisms).  
- The **creation of an anonymous link does not pose an immediate risk** to the company because the generated link contains a unique token of about 50 characters (containing numbers, letters, symbols, and respecting the case). It can only be used by users who have it in their possession. 

If anonymous links are opened, it will be however **opportune to ensure with users that the generated sharing links are legitimate and do not pose inappropriate security risks**. Indeed, the users are on the front line and are the only ones who can assess the need and whether the actions performed are voluntary or a mistake. 

For this, I will present in a future article how to contact users and fix errors in two clicks, using Defender for Cloud Apps (ex-MCAS) and Power Automate. 


