---
title: "Information Barriers - Feedback from the field: how to implement it in one hour?"
subtitle:
excerpt: Information Barriers is an interesting feature to restrict communication and collaboration in Office 365 (compliance, carve out, compromise, etc.). Find here some best practices and clarifications with a detailed example.  
tags:
  - Microsoft 365
  - Compliance
header_img : "https://thijoubert.github.io/assets/img/posts/2021-07-27_InformationBarriers-Feedback-from-the-field-1-2_0.png"

---



[Information Barriers is an advanced compliance option in Office 365 offered by Microsoft](https://docs.microsoft.com/en-us/microsoftteams/information-barriers-in-teams), allowing to restrict communication and collaboration between groups of users. 

This feature, initially designed to **enforce a compliance policy, can also be used to isolate a perimeter in the event of a carve out or compromise**.

Before anything else, I think it is important to **remind that Information Barriers will provide a technical response to a business need**. BUT, as any technical answer, the solution cannot be exhaustive and will not prevent a user from chatting, sending a picture or even a carrier pigeon... 

The solution covers the **main services in Office 365, but not all**. We will come back to this in the rest of the article.


<br/>
# A brief historical reminder

Information Barriers replaces an old feature in the Microsoft environment: [Address Book Policies](https://docs.microsoft.com/en-us/exchange/address-books/address-book-policies/create-an-address-book-policy). These policies allowed users to be partitioned to customize the directory (or GAL). 

Once implemented, a user could only see in the GAL the people with whom he had the right to interact. However, nothing technically prevented him from exchanging emails with an unauthorized person. 

It is required to remove any ABP before implementing Information Barriers. 

Il est requis de supprimer toute ABP avant implémentation d’Information Barriers. 


<br/>
# How to set up Information Barriers in less than an hour? 
If the prerequisites are there and the business needs are clear, the implementation can be straight forward. 


## What are the prerequisites?
 
Les principaux prérequis évoqués dans la [documentation sont les suivants ](https://docs.microsoft.com/en-us/microsoft-365/compliance/information-barriers-policies?view=o365-worldwide): 
- Assign licenses to users who need on (see dedicated section below)?
- Fill the [relevant user attributes](https://docs.microsoft.com/en-us/microsoft-365/compliance/information-barriers-attributes?view=o365-worldwide) for segmenting users.
- Enable [scoped directory search](https://docs.microsoft.com/en-us/MicrosoftTeams/teams-scoped-directory-search) for Microsoft Teams.
- Add the application “Information Barriers Processor” and give consent as an admin.

A tip, for those who do not have Azure PowerShell installed, it is possible to use the Azure AD:

```powershell
# Connect-AzureAD
$appId = "bcf62038-e005-436d-b970-2a472f8c1982" 
$sp = Get-AzureADServicePrincipal -ServicePrincipalName $appId
if ($sp -eq $null) { New-AzureADServicePrincipal -AppId $appId }
Start-Process  "https://login.microsoftonline.com/common/adminconsent?client\_id=$appId" 
```

<img src="https://thijoubert.github.io/assets/img/posts/2021-07-27_InformationBarriers-Feedback-from-the-field-1-2_1.png" >


<br/>
## Step 0: Define the target
This phase is very important: it is **necessary to write down in advance who is allowed to communicate and collaborate with whom**. Depending on the number of segments, this can be a real headache. 

As usual, let's take a **concrete example** (which a movie I particularly like for those who know me). 

In this example, we will have 6 **user groups**: "Mordor", "Fellowship of the Ring", "Gondor", "Rohan", "Isengard", "Isengard - Saruman Army":
- As a result of rivalries, Rohan and Gondor have broken off all diplomatic relations.
- By default, Good ("Fellowship of the Ring", "Gondor", "Rohan") and Evil ("Mordor", "Isengard", "Isengard - Saruman Army") cannot interact.
- Mordor has visibility on the actions of each.
- Saruman has managed to hide his army from Mordor.

This is reflected in the table below:

|**This segment …**|**…can communicate with**|**… cannot communicate with** |
|**Mordor**|Tout le monde sauf “Isengard – Saruman Army”|“Isengard – Saruman Army”|
|**Fellowship of the Ring**|“Mordor”, “Gondor”, “Rohan”|“Isengard”, “Isengard – Saruman Army”|
|**Rohan**|“Mordor”, “Fellowship of the Ring”|“Gondor”, “Isengard”, “Isengard – Saruman Army”|
|**Gondor**|“Mordor”, “Fellowship of the Ring”|“Rohan”, “Isengard”, “Isengard – Saruman Army”|
|**Isengard**|“Mordor”, “Isengard – Saruman Army”|“Fellowship of the Ring”, “Gondor”, “Rohan”|
|**Isengard – Saruman Army**|“Isengard”|“Mordor”, “Fellowship of the Ring”, “Gondor”, “Rohan”|

<br/>
We can also illustrate the communications in the following diagram.

<img src="https://thijoubert.github.io/assets/img/posts/2021-07-27_InformationBarriers-Feedback-from-the-field-1-2_2.png" >

In the case of your organization, you just need to replicate the same logic.


<br/>
## Step 1: Technically declare user segments

After the identification of the use cases, the next step is to technically **define the user groups, via segments**. 

Segments are like **dynamic groups based on user attributes**. The list of **possible fields** (Company, Department, UsageLocation, etc.) is described in the [documentation](https://docs.microsoft.com/en-us/microsoft-365/compliance/information-barriers-attributes?view=o365-worldwide). A user can belong to only one segment. 

Today, it is necessary to use **PowerShell**, which can be complex for a compliance officer.

In our example, we will use the Department field. 

```powershell
# Sign in to Compliance Center 
Import-Module ExchangeOnlineManagement
Connect-IPPSSession -UserPrincipalName "UPN of the admin" 

# Creation of the different segments 
New-OrganizationSegment -Name "Fellowship of the Ring" -UserGroupFilter "Department -eq 'Fellowship of the Ring'"
New-OrganizationSegment -Name "Gondor" -UserGroupFilter "Department -eq 'Gondor'"
New-OrganizationSegment -Name "Rohan" -UserGroupFilter "Department -eq 'Rohan'"
New-OrganizationSegment -Name "Mordor" -UserGroupFilter "Department -eq 'Mordor'" 
New-OrganizationSegment -Name "Isengard - Saruman Army" -UserGroupFilter " Department -eq 'Isengard - Saruman Army'"
New-OrganizationSegment -Name "Isengard" -UserGroupFilter "Department -eq 'Isengard'"
```

<img src="https://thijoubert.github.io/assets/img/posts/2021-07-27_InformationBarriers-Feedback-from-the-field-1-2_3.png" >


An **Azure B2B guest can also be attached to a segment** by editing the relevant attribute. The guest will then be impacted by the Information Barriers policies. 

However, **users of an organization authorized via the Teams federation** are not supported at this time, as it is not possible to attach them to a segment.

A user who does not belong to a segment is allowed to communicate with any user, regardless of his segment.
{: #myid .alert .alert-info .p-3 .mx-2 mb-3}

To list all segments, you can use the following PowerShell command: 

```powershell
Get-OrganizationSegment | Select Name, Guid, ExoSegmentId, UserGroupFilter | Sort-Object -Descending "Name" |  Format-Table 
```

<img src="https://thijoubert.github.io/assets/img/posts/2021-07-27_InformationBarriers-Feedback-from-the-field-1-2_4.png" >


<br/>
## Step 2: Technically declare Information Barriers policies

Now comes the time to **technically implement the policies**. Two types of policies are proposed by Microsoft:
- Type 1: **Block communication between two segments** (Block policy)
- Type 2: **Limit the segments with which a segment can communicate** (Allow policy)

Before choosing between the two types, it is essential to have in mind the underlying workings: 
- A segment can only have **one policy** applied to it.
- **Policies must be symmetrical**: it is not possible to restrict communication from an entity A to an entity B, but to allow B to communicate to A.
- The parameters "SegmentsAllowed" and "SegmentsBlocked" are **mutually exclusive**.
- **Policies must be technically symmetrical**: if we define a rule concerning two segments, we must have two policies.
- In the case of Allow policies, it must be specified that the segment can talk to itself.

Unlike what is written in the Microsoft documentation, an Allow policy does not stand alone. Under the hood, an Allow policy is like creating a Block policy with all segments except the ones that are allowed. 
This means that it is not possible to have an Allow policy and a segment without a policy.
{: #myid .alert .alert-info .p-3 .mx-2 mb-3}

Indeed, I had defined, according to the documentation example, the following four policies: 

```powershell
New-InformationBarrierPolicy -Name "IB Policy - Rohan vs Gondor" -AssignedSegment "Gondor" -SegmentsBlocked "Rohan" -State "Active"
New-InformationBarrierPolicy -Name "IB Policy - Rohan vs Gondor - reverse" -AssignedSegment "Rohan" -SegmentsBlocked "Gondor" -State "Active"
New-InformationBarrierPolicy -Name "IB Policy - Isengard" -AssignedSegment "Isengard" -SegmentsAllowed "Isengard","Isengard - Saruman Army","Mordor" -State "Active"
New-InformationBarrierPolicy -Name "IB Policy - Isengard - Saruman Army" -AssignedSegment "Isengard - Saruman Army" -SegmentsAllowed "Isengard","Isengard - Saruman Army" -State "Active”
```

<img src="https://thijoubert.github.io/assets/img/posts/2021-07-27_InformationBarriers-Feedback-from-the-field-1-2_5.png" >

But when applying the policies (see step 3), I received the following error message: 

<img src="https://thijoubert.github.io/assets/img/posts/2021-07-27_InformationBarriers-Feedback-from-the-field-1-2_6.png" >

After some testing, I was able to confirm that it was necessary to create a policy for each segment. I forwarded the information to Microsoft Support for documentation correction.

With the new policies, this gives: 

```powershell
New-InformationBarrierPolicy -Name "IB Policy - Gondor" -AssignedSegment "Gondor" -SegmentsBlocked "Rohan","Isengard","Isengard - Saruman Army" -State "Inactive"#-State "active"
New-InformationBarrierPolicy -Name "IB Policy - Rohan" -AssignedSegment "Rohan" -SegmentsBlocked "Gondor","Isengard","Isengard - Saruman Army" -State "Inactive"#-State "active"
New-InformationBarrierPolicy -Name "IB Policy - Isengard" -AssignedSegment "Isengard" -SegmentsAllowed "Isengard","Isengard - Saruman Army","Mordor" -State "Inactive"#-State "active"
New-InformationBarrierPolicy -Name "IB Policy - Isengard - Saruman Army" -AssignedSegment "Isengard - Saruman Army" -SegmentsAllowed "Isengard","Isengard - Saruman Army" -State "Inactive"#-State "active"
New-InformationBarrierPolicy -Name "IB Policy - Mordor" -AssignedSegment "Mordor" -SegmentsBlocked "Isengard - Saruman Army" -State "Inactive"#-State "active"
New-InformationBarrierPolicy -Name "IB Policy - Fellowship of the Ring" -AssignedSegment "Fellowship of the Ring" -SegmentsBlocked "Isengard","Isengard - Saruman Army" -State "Inactive"#-State "active"
```

To list all policies, you can use the following PowerShell cmdlet: 

```powershell
Get-InformationBarrierPolicy | Select Name, Guid, State, SegmentsAllowed, SegmentsBlocked | Format-Table 
```

<img src="https://thijoubert.github.io/assets/img/posts/2021-07-27_InformationBarriers-Feedback-from-the-field-1-2_7.png" >


<br/>
## Step 3: Apply the policy

Once all policies are defined (and with a "active" status), they can be applied with the following PowerShell cmdlet: 

```powershell
Start-InformationBarrierPoliciesApplication
```

In my case, the policies were always applied within **30 minutes**. In the case of a larger tenant, Microsoft specifies a figure of **5000 users per hour**. 

<img src="https://thijoubert.github.io/assets/img/posts/2021-07-27_InformationBarriers-Feedback-from-the-field-1-2_8.png" >

In the background, the "Information Barriers Processor" application defined previously will make sure to repair deviations .For example, the first owner of a Team (in the alphabetical order) defines the associated segment, all users in violation of the policy will be removed. 

**Well done! Information Barriers policies are active and applied.**


<br/>
## Step 4: Decide if Information Barriers should apply to SharePoint and OneDrive

**The measures defined so far apply only to Teams.** Two users in non-compatible segments (for example Saruman within “Isengard” or Frodo within “Fellowship of the Ring”) will not be able to :**    
- Be in the same call as another user 
- Be in the same Teams conversation 
- Be on the same Teams team
- Share a screen 
- Search for a user in the Exchange Online GAL or in Teams 

It will still be possible to add a user in a Microsoft 365 Groups linked to Teams via Azure AD, in violation of the policy, but the Information Barriers processor application will hide the user in Teams. 

However, these two users can always : 
- Share a document from a SharePoint site (even a SharePoint site behind a Teams team)
- Share a document from OneDrive
- Communicate and collaborate from Yammer (see screenshot below)
- Communicate and collaborate in a Planner  

<img src="https://thijoubert.github.io/assets/img/posts/2021-07-27_InformationBarriers-Feedback-from-the-field-1-2_9.png" >

At this point, it may be interesting to decide whether **OneDrive for Business and SharePoint Online should also be affected by Information Barriers policies**. This can be done with the following PowerShell command: 

```powershell
Set-Spotenant -InformationBarriersSuspension $false 
```

Once enabled: 
- A user will not be able to **share a document from their OneDrive with a user from another segment**, without adding said segment by a SharePoint Online administrator to the user's OneDrive
- **A user will not be able to share a document from a SharePoint site with a user from a different segment of the site**, without adding the said segment by a site administrator or a SharePoint Online administrator 

Note that users without a segment will not be able to collaborate on these sites or OneDrive.
{: #myid .alert .alert-info .p-3 .mx-2 mb-3}

I will come back to all these impacts from an end user’s eye in a second article. 


<br/>
## Step 5: Monitor the evolution of the Information Barriers

During my tests, I noticed some interesting points:  
- It is not possible to delete an applied policy, but it is possible to modify or deactivate it. The changes will only apply when the application will be performed with PowerShell. Note that the **policy behavior** got by Get-InformationBarrierPolicy **will not be the one of the policy in place**.
- It is not possible to delete a segment affected by an applied policy, but it is **possible to modify it**. The change is immediate. 

I would recommend to monitor actions related to segment or policy management to detect malicious actions or errors. 
{: #myid .alert .alert-info .p-3 .mx-2 mb-3}

<img src="https://thijoubert.github.io/assets/img/posts/2021-07-27_InformationBarriers-Feedback-from-the-field-1-2_10.png" >

Similarly, tracking the evolution of attributes could be interesting. **The alteration of an attribute** (e.g. "Rohan" replaced by "Rohann") **would remove the user from the policy**.


<br/>
# Licenses, always a tricky topic

**Who needs an Information Barriers license? [**As with other productivity, security or compliance services, this is THE major limiting factor.](https://www.thijoubert.com/2020-08/M365SecurityComplianceLicensing/)** 

**To answer the question, let's go back to the technical definition of Information Barriers policies.** 


## Case of the Block policies

It is very simple**. A user belonging to a segment concerned by a Block policy** must have an Information Barriers license. 


## Case of the Allow policies

Intuitively, the first thought is that only users in the segments affected by Allow policies should be licensed. 

The reality is more complex. Since **Allow policies are interpreted as "the segment cannot communicate with all segments except those specified", it is necessary to put ALL users in the organization into a segment** and thus assign licenses to the entire organization.  


## What about the guests? 

**Compliance licensing for guest users, remains unclear today**. From my testing, it is possible to define a segment and a policy that applies to a guest population. 

However, I don't find this to be consistent from a licensing standpoint. But the sales teams I contacted do not have this information either. 

As a reminder, the only licenses officially required for guest users are Azure AD Premium (P1 or P2) or premium licenses for the Power Platform.



<br/>
# What are the main limitations of Information Barriers today?

- **Complexity:** The definition of segments and policies can become very complex in the context of many use cases (due to the nature of Allow policies), especially since the implementation is done solely in PowerShell. 
- **Policy symmetry:** It is not possible to enforce one-way communication. 
- **Non-exhaustive service coverage:** Several main services are not covered today and allow policies to be bypassed (e.g. Yammer, Planner).** 
- **No granularity:** Policies are applied in all-or-nothing mode. It is not possible to allow two users to send each other messages via Teams, but to restrict file sharing.
- **Teams Federation:** Internal users will be able to communicate with users from federated organizations regardless of the Information Barriers policies implemented.
