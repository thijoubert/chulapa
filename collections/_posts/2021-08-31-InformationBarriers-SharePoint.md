---
title: "Information Barriers: What impacts and user experience for SharePoint?"
subtitle:
excerpt: Although the primary use of Information Barriers is for communication in Teams, it is possible to extend the coverage to OneDrive and SharePoint Online.
  
tags:
  - Microsoft 365
  - Compliance
  - Information Barriers
  - SharePoint
header_img : "./assets/img/posts/2021-08-31_InformationBarriers-SharePoint_0.jpg"
og_image: "./assets/img/posts/2021-08-31_InformationBarriers-SharePoint_0.jpg"
---


[Information Barriers is an advanced compliance option in Office 365 offered by Microsoft](https://docs.microsoft.com/en-us/microsoftteams/information-barriers-in-teams), allowing to **restrict communication and collaboration** between groups of users.

This article is part of a series on Information Barriers with : 
- [Information Barriers - Feedback from the field: how to implement it in one hour?](https://www.thijoubert.com/2021-07/InformationBarriers-Feedback-from-the-field-1-2/)
- [Information Barriers: What impacts and user experience for OneDrive?](https://www.thijoubert.com/2021-08/InformationBarriers-OneDrive/)



# Introduction to Information Barriers within SharePoint Online

This feature, initially designed to enforce an organization compliance policy, can also be used to isolate a perimeter in the event of a carve out or compromise. I have explained in a [previous article](https://www.thijoubert.com/thijoubert.github.io/2021-07/InformationBarriers-Feedback-from-the-field-1-2/) how to define and activate Information Barriers segments and policies.

Although the primary use of Information Barriers is for communication in Teams, it is possible to **extend the coverage to OneDrive and SharePoint Online**. This is done today via the Powershell cmdlet below:

```powershell
Set-Spotenant -InformationBarriersSuspension $false
```

Immediately after running the cmdlet, a **new property, InformationSegment**, is used for SharePoint sites and OneDrive libraries. This property lists the GUIDs of the segments associated with the concerned workspace. A new column also appears in the SharePoint Online Administration Center for SharePoint sites with the display name of these segments.



# What are the principles of Information Barriers for SharePoint?

According to [Microsoft documentation](https://docs.microsoft.com/en-us/sharepoint/information-barriers#enable-sharepoint-and-onedrive-information-barriers-in-your-organization), Content sharing in SharePoint follows these principles:
- The site and its content can be shared only with users whose segment matches that of the site. 
- New users can be added to the site as site members only if their segment matches that of the site.
- In the case where no segment is associated with a SharePoint site, a user can share an item to people who belong to compatible segments with his, even if those segments are not compatible with one another.

Note that a SharePoint site without segments could be used to share content across the organization. Where this could be seen as a bypass of the organization's compliance policies, it can also be seen as an opportunity to have a common intranet for all.  
{: #myid .alert .alert-info .p-3 .mx-2 mb-3}


# In real life, how are the segments applied to a SharePoint site? 

The basic principle is that **when a collaboration and communication space is created, the SharePoint site or Microsoft 365 Group will inherit the owner segment.** 

However, there are several **context-dependent subtleties**: 
- **Who performs the creation action:** a user or an administrator?** 
- **What is the object created in the first place:** a SharePoint site, a Team, a Microsoft 365 group, a Planner, etc.? 
- **What is the interface used?** 

The next two parts detail some specific cases that I could meet during my tests.  

Note that in all the cases described below, I only consider native interfaces. SharePoint sites, Teams and Microsoft 365 groups created by service accounts or third party management tools might follow a different behavior derived from the cases described.

In order to better explain these tests, I have used the same organization as in my previous articles:

|**This segment …**|**…can communicate with**|**… cannot communicate with** |**Users**|
| :-: | :-: | :-: | :-: |
|**Orcs**|Everyone except “Goblins – Army”|“Goblins – Army”|Orc|
|**Men**|“Orcs”, “Elves”, “Dwarves”|“Goblins”, “Goblins – Army”|Man 1, Man 2|
|**Elves**|“Orcs”, “Men”|“Dwarves”, “Goblins”,<br>“Goblins – Army”|Elf|
|**Dwarfs**|“Orcs”, “Men”|“Elves”, “Goblins”, <br>“Goblins – Army”|Dwarf, Dwarf 2|
|**Goblins**|“Orcs”, “Goblins – Army”|“Men”, “Elves”, “Dwarves”|Goblin|
|**Goblins - Army**|“Goblins”|“Orcs”, “Men”, “Elves”, “Dwarves”|Goblin Army 1, Goblin Army 2, Goblin Army 3|


## Creation of a space by an end user

### Case 1: A user creates a new communication site 

:point_right:**When a user creates a communication site, his segment is automatically associated to the site.** 
The segments associated to a SharePoint site are **visible to the end users (see screenshot below).** 

<img src="https://thijoubert.github.io/assets/img/posts/2021-08-31_InformationBarriers-SharePoint_1.png" >

A site owner can then **add a segment compatible with his or her own or delete an existing segment** via "Site Information" > "Add segments". 

<img src="https://thijoubert.github.io/assets/img/posts/2021-08-31_InformationBarriers-SharePoint_2.png" >

<img src="https://thijoubert.github.io/assets/img/posts/2021-08-31_InformationBarriers-SharePoint_3.png" >

In the above screenshots, we can see that our user Dwarf can only add the "Orcs" and "Men" segments. These are indeed the only ones compatible with his.


### Case 2: A user creates a new team site

:point_right: **When a user creates a team site, his segment is automatically associated to the site.**

Note that when creating a site a user can add secondary owners or users (see screeshot below). 

These users or owners will only have access if their segments are manually associated by the owner (as explained in case 1). 

<img src="https://thijoubert.github.io/assets/img/posts/2021-08-31_InformationBarriers-SharePoint_4.png" >

If one of the owners or users added by the primary owner try to access the site, before the addition of their segments, he will see the following error message: 

<img src="https://thijoubert.github.io/assets/img/posts/2021-08-31_InformationBarriers-SharePoint_5.png" >


### Case 3: A user creates a new Team

As a reminder, **the segments associated with a Team depend on the members added by the owner(s)**: 
- A Team automatically gets the segment of its primary owner. 
- Only users belonging to a segment compatible with all the segments already associated to the Team can be added.
- Adding a user from a new segment automatically adds this segment to the segments already associated.

When a user creates a new Team, a Microsoft 365 Group and a team SharePoint site are provisioned by construction. 

The assignment of segments to the created SharePoint site follows a different behavior than the cases 1 and 2. 

:point_right: **The newly created site is automatically associated with the Team segments, i.e. the team members' segments**. 

The owner cannot add segments via the SharePoint interface. 
{: #myid .alert .alert-info .p-3 .mx-2 mb-3}

<img src="https://thijoubert.github.io/assets/img/posts/2021-08-31_InformationBarriers-SharePoint_6.png" >


### Case 4: A user creates a new Microsoft 365 Group via the Azure portal

When a user creates a new Microsoft 365 Group, a new Team SharePoint site is provisioned by construction. 

:point_right: **The newly created site is not associated with any segments.** 

The owner can then add or remove segments as in case 1 and 2.


### Case 5: A user creates a new Microsoft Group via 365 through Outlook

Same behavior than the case 4: 

:point_right: **The newly created site is not associated with any segments.** 


### Case 6: A user creates a new Planner

When a user creates a new Planner, he can decide to create a new Microsoft 365 Group or to reuse an existing Group. In the first case, this Planner, a team SharePoint site is provisioned by construction. 

:point_right: ** The newly created site is not associated with any segments.** 

The owner can then add or remove segments as in case 1 and 2.

I summarized my different tests in the table below:

<img src="https://thijoubert.github.io/assets/img/posts/2021-08-31_InformationBarriers-SharePoint_7.png" >



## Creation of a space by an administrator

To simplify the reading, I have grouped the **different tests by interface.** 


### Case 1: An administrator creates a new site via the Admin portal

During my tests, I realized that there were several sub-cases when creating a new SharePoint site via the SharePoint administrator portal depending on the site template, on whether a segment was added manually by an administrator or not, if a Team was created:  

For each of the cases tested, I added the following members and owners: 
- Primary owner: “Dwarf”
- Other owner (except for the communication site) : “Goblin”
- Members (except for the communication site) : “Goblin – Army 1”,“Orc”, “Man 1”

List of the tested use cases:
- **IB - Admin - SharePoint 1** : Creating a communication site
- **IB - Admin - SharePoint 2** : Creating a team site
- **IB - Admin - SharePoint 4** : Creating a team site, then creating a Team by “Dwarf”
- **IB - Admin - SharePoint 4** : Create a team site, then create a team by “Goblin”
- **IB - Admin - SharePoint 5** : Creation of a team site, assignment of a segment (Dwarves) 
- **IB - Admin - SharePoint 6** : Creating a team site, assigning a segment (Goblins)
- **IB - Admin - SharePoint 7** : Creating a team site, assigning a segment (Elves)
- **IB - Admin - SharePoint 8** : Create a team site, assign a segment (Dwarves) then create a team by “Dwarf”
- **IB - Admin - SharePoint 9** : Creation of a team site, assignment of a segment (Goblins) then creation of a Team by “Dwarf”
- **IB - Admin - SharePoint 10** : Creation of a team site, assignment of a segment (Elves) then creation of a Team by “Dwarf”

Before presenting the results of the various tests, I would like to remind that an administrator can remove or add compatible segments in the SharePoint administration portal via the "Policies" tab.

<img src="https://thijoubert.github.io/assets/img/posts/2021-08-31_InformationBarriers-SharePoint_8.png" >

<img src="https://thijoubert.github.io/assets/img/posts/2021-08-31_InformationBarriers-SharePoint_9.png" >

You can also find the segments associated with the different sites via : 

```powershell
$Sites **=** Get-SPOSite -IncludePersonalSite $false -Limit all **|** Sort Owner

$arrUsersSegments **=** New-Object System.Collections.Generic.List[System.Object] *# [System.Collections.Generic.List[Object]]::new() # Create output file for report*

foreach($site in $Sites){
     $userSegment = Get-SPOSite -Identity $site**.Url** **|** Select Owner, InformationSegment
     $objUserSegments = New-Object PSObject
     $objUserSegments | Add-Member -MemberType NoteProperty -Name Owner -Value $userSegment**.Owner**
     $objUserSegments | Add-Member -MemberType NoteProperty -Name InformationSegment -Value $userSegment**.InformationSegment**
$arrUsersSegments**.Add**($objUserSegments)
} 
```

Let's go back to the test results. 
The first thing I noticed was that the **application of the segments was done in several steps and was not instantaneous.**

<br/>
**Immediately**: only SharePoint sites with a segment manually associated by the administrator have a segment. 

<img src="https://thijoubert.github.io/assets/img/posts/2021-08-31_InformationBarriers-SharePoint_10.png" >

<br/>
**After 24 hours:** the creation of the Teams seems to have changed the segments associated with the linked SharePoint sites.** 

<img src="https://thijoubert.github.io/assets/img/posts/2021-08-31_InformationBarriers-SharePoint_11.png" >

<br/>
**After 7 days:** there is an automatic cleaning of the segments associated with the SharePoint sites. The logic seems to be to only keep the segments that are compatible with the creator of the linked Team if any.

Note that it is always possible for a SharePoint administrator or a space owner to change these associated segments afterwards.

<img src="https://thijoubert.github.io/assets/img/posts/2021-08-31_InformationBarriers-SharePoint_12.png" >

<br/>
We can find a trace of these three steps changes in the unified audit logs:

<img src="https://thijoubert.github.io/assets/img/posts/2021-08-31_InformationBarriers-SharePoint_13.png" >

I summarized my different tests in the table below:

<img src="https://thijoubert.github.io/assets/img/posts/2021-08-31_InformationBarriers-SharePoint_14.png" >

I was also able to test that the behavior was identical when creating a site via Graph API or via PowerShell.


### Case 2: An administrator creates a new Microsoft 365 Group from the Azure Portal 

When an administrator creates a new Microsoft 365 Group, a new Team SharePoint site is provisioned by construction. 

Again, for each of the cases tested, I added the following members and owners: 
- Primary Owner: "Dwarf"
- Secondary owner (except for the communication site): "Goblin".
- Members (except for the communication site) : "Goblin - Army 1", "Orc", "Man 1"

List of tested cases: 
- IB - Admin - Azure AD 1 : Creation of a Microsoft 365 Group
- IB - Admin - Azure AD 2 : Creation of a Microsoft 365 Group, then creation of a Team by “Dwarf”
- IB - Admin - Azure AD 3 : Creation of a Microsoft 365 Group, then creation of a Team by “Goblin”

<br/>
**Immediately:** Newly created sites are not associated with a segment. **
<img src="https://thijoubert.github.io/assets/img/posts/2021-08-31_InformationBarriers-SharePoint_15.png" >

<br/>
**After 24 hours:** Sites are associated with the Teams creator’s segment and compatible members’ segments. Sites not linked to the Teams are not associated with segments. **
<img src="https://thijoubert.github.io/assets/img/posts/2021-08-31_InformationBarriers-SharePoint_16.png" >

I summarized my different tests in the table below: 
<img src="https://thijoubert.github.io/assets/img/posts/2021-08-31_InformationBarriers-SharePoint_17.png" >

I was also able to test that the behavior was identical when creating a Microsoft 365 Group via Graph API or via PowerShell.



# What is the user experience for a site SharePoint covered by an Information Barriers policy?

When our user clicks on “Share”, the sharing options are quite identical with or without Information Barriers. 

Indeed, **all options are the same except for “Anyone with the link” which is disabled.** 
<img src="https://thijoubert.github.io/assets/img/posts/2021-08-31_InformationBarriers-SharePoint_18.png" >

For the other links, **the behaviors are identical to those of OneDrive.** I have described them in [another article](https://www.thijoubert.com/2021-08/InformationBarriers-OneDrive/).

It may be interesting to remember that : 
- A user who has access to a "People in the organization" link but does not belong to one of the site's segments will get an error message if he wants to access it. 
- A user who does not belong to one of the segments of the site will not be able to be put in recipient of a sharing link.



# Other feedbacks ? 

When applying Information Barriers policies to Sharepoint :

- Previously created share links are not removed, but a non-compatible user will no longer be able to access the shared item.
- Some users with whom items have been shared still appear in the SharePoint cache, especially users without segments.
- Users with whom items have been shared are not removed from the library contacts.

An interesting - and fundamental - question was asked to me recently: what about existing SharePoint sites? I definitely need to do this test.
{: #myid .alert .alert-info .p-3 .mx-2 mb-3}
