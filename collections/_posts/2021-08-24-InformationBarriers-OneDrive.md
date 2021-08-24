---
title: "Information Barriers: What impacts and user experience for OneDrive? "
subtitle:
excerpt: Although the primary use of Information Barriers is for communication in Teams, it is possible to extend the coverage to OneDrive and SharePoint Online.
  
tags:
  - Microsoft 365
  - Compliance
  - Information Barriers
header_img : "./assets/img/posts/2021-08-24_InformationBarriers-OneDrive_0.jpg"
og_image: "./assets/img/posts/2021-08-24_InformationBarriers-OneDrive_0.jpg"
---


[Information Barriers is an advanced compliance option in Office 365 offered by Microsoft](https://docs.microsoft.com/en-us/microsoftteams/information-barriers-in-teams), allowing to **restrict communication and collaboration** between groups of users. 

This feature, initially designed to enforce an organization compliance policy, can also be used to isolate a perimeter in the event of a carve out or compromise. I have explained in a [previous article](https://www.thijoubert.com/thijoubert.github.io/2021-07/InformationBarriers-Feedback-from-the-field-1-2/) how to define and activate Information Barriers segments and policies. 

Although the primary use of Information Barriers is for communication in Teams, it is possible to **extend the coverage to OneDrive and SharePoint Online**. This is done today via the Powershell cmdlet below: 

```powershell
Set-Spotenant -InformationBarriersSuspension $false
```

Immediately after running the cmdlet, a **new property, InformationSegment**, is used for SharePoint sites and OneDrive libraries. This property lists the GUIDs of the segments associated with the concerned workspace. A new column also appears in the SharePoint Online Administration Center for SharePoint sites with the display name of these segments.



<br/>
# What are the principles of Information Barriers for OneDrive? 

According to [Microsoft documentation](https://docs.microsoft.com/en-us/onedrive/information-barriers): 

1. *When a segment is applied to a user, that segment is automatically associated with the user's OneDrive.*
1. *The segments that are compatible with the user's segment and with each other will also get associated with the OneDrive.*

In other words, if the segments that are compatible (or Allowed) with the user's segment are all compatible with one another, then they are also associated with OneDrive. **If two of them are not compatible, then only the user's segment is applied.** 

Difference with Teams: A user could communicate with a user in a Teams conversation or meeting but not be able to share content with them from their OneDrive. 
{: #myid .alert .alert-info .p-3 .mx-2 mb-3}

Content sharing in OneDrive follows these principles:  

- A user can only share an item with people who belong to compatible segments AND whose segments are associated with OneDrive.
- In the case where no segment is associated with a OneDrive, a user can share an item to people who belong to compatible segments, even if those segments are not compatible with one another. 

To note: a user not covered by Information Barriers is not affected by the above restrictions and can create "Anonymous with the link" and share a document to users belonging to segments that are not compatible with each other. 

Difference with Teams: Teams does not allow to create a meeting or a conversation with users belonging to non-compatible segments, while OneDrive would allow to have a document shared to people not allowed to collaborate.
{: #myid .alert .alert-info .p-3 .mx-2 mb-3}



<br/>
# In real life, how are the segments applied on OneDrive? 

From my testing: 

- Extending the Information Barriers coverage does result in the technical application of the policies for existing OneDrive within 24 hours. However, the "InformationSegment" property is not completed. 
- The OneDrive of new users are covered within one hour after creation. The "InformationSegment" property is completed as expected. 

It is necessary to redefine the segments to force the completion of this property.
{: #myid .alert .alert-info .p-3 .mx-2 mb-3}


To get a OneDrive report, I used the following script: 

```powershell
$Sites = Get-SPOSite -IncludePersonalSite $true -Limit all -Filter "Url -like '-my.sharepoint.com/personal/'"  | Sort Owner

$arrUsersSegments = New-Object System.Collections.Generic.List[System.Object] # [System.Collections.Generic.List[Object]]::new() # Create output file for report

foreach($site in $Sites){
     $userSegment = Get-SPOSite -Identity $site.Url | Select Owner, InformationSegment
     $objUserSegments = New-Object PSObject
          $objUserSegments | Add-Member -MemberType NoteProperty -Name Owner -Value $userSegment.Owner
          $objUserSegments | Add-Member -MemberType NoteProperty -Name InformationSegment -Value $userSegment.InformationSegment
     $arrUsersSegments.Add($objUserSegments)
} 
```

For the demonstration, I will use my favorite example:  

|**This segment …**|**…can communicate with**|**… cannot communicate with** |**Users**|
|**Orcs**|Everyone except “Goblins – Army”|“Goblins – Army”|Orc|
|**Men**|“Orcs”, “Elves”, “Dwarves”|“Goblins”, “Goblins – Army”|Man 1, Man 2|
|**Elves**|“Orcs”, “Men”|“Dwarves”, “Goblins”,<br>“Goblins – Army”|Elf|
|**Dwarfs**|“Orcs”, “Men”|“Elves”, “Goblins”, <br>“Goblins – Army”|Dwarf, Dwarf 2|
|**Goblins**|“Orcs”, “Goblins – Army”|“Men”, “Elves”, “Dwarves”|Goblin|
|**Goblins - Army**|“Goblins”|“Orcs”, “Men”, “Elves”, “Dwarves”|Goblin Army 1, Goblin Army 2, Goblin Army 3|

A first execution of the script gives an empty InformationSegment column. I then performed the actions below and ran the script again.

- Creation of a new user: "Dwarf 2
- Redefinition of the segment "Goblins - Army
- Modification of the segment of "Man 1

<img src="https://thijoubert.github.io/assets/img/posts/2021-08-24_InformationBarriers-OneDrive_1.png" >

The associated segments by default are consistent because  

- For the segment "Dwarves", all allowed segments are compatible with each other ("Dwarves", "Orcs" and "Men").
- For the segment "Men", two allowed segments are not compatible with each other ("Dwarves" and "Elves").
- For the segment "Goblins - Army", all allowed segments are compatible with each other ("Goblins - Army" and "Goblins").

<img src="https://thijoubert.github.io/assets/img/posts/2021-08-24_InformationBarriers-OneDrive_2.png" >

It is possible to add or remove a compatible segment via the cmldets below: 

```powershell
Set-SPOSite -Identity <site URL> -AddInformationSegment <segment GUID>
Set-SPOSite -Identity <site URL> -RemoveInformationSegment <segment GUID>
```



<br/>
# What is the user experience for a OneDrive covered by an Information Barriers policy? 
According to the [documentation](https://docs.microsoft.com/en-us/onedrive/information-barriers), *when a segment is associated with a OneDrive*:

- *The option to share with "Anyone with the link" is disabled.*
- *Files and folders can be shared only with users whose segment matches that of the OneDrive.*
- *Non-segment users can access shared OneDrive files only from other non-segment users.*

<br/>
To clarify the user experience, let's put ourselves in the shoes of one of our users, Goblin Army 1. By definition, this user is only allowed to communicate and collaborate with users from the "Goblins" and "Goblins - Army" segments.

When our user clicks on "Share", the sharing options are identical with or without Information Barriers.

<img src="https://thijoubert.github.io/assets/img/posts/2021-08-24_InformationBarriers-OneDrive_3.png" >


## New sharing to “Anyone with the link”: 

Contrary to what is described, “Anyone with the link” is available to our user. 

However, when our user tries to create a new anonymous share link with “Copy link” or “Send link”, he gets an error message.

<img src="https://thijoubert.github.io/assets/img/posts/2021-08-24_InformationBarriers-OneDrive_4.png" >

Note that the details of the error message are very technical. 

<img src="https://thijoubert.github.io/assets/img/posts/2021-08-24_InformationBarriers-OneDrive_5.png" >


## New sharing link to “People in the organization”

Our user, Goblin - Army 1, can create a link accessible to all users in the organization via “Copy link” or send it directly to a user in a compatible segment via “Send link”.

<img src="https://thijoubert.github.io/assets/img/posts/2021-08-24_InformationBarriers-OneDrive_6.png" >

However, an unauthorized user in possession of this link will not be able to access the document. When he tries to open the document, he will be presented with a blank page. 

<img src="https://thijoubert.github.io/assets/img/posts/2021-08-24_InformationBarriers-OneDrive_7.png" >

We can see in the URL of the white page that access has been denied. 

<https://dwpsecuritytrial-my.sharepoint.com/personal/goblin_army1_dwpsecuritytrial_onmicrosoft_com/_layouts/15/AccessDenied.aspx?p=128&type=accessremoved&correlation=b670e39f-e0dc-1000-09e9-a3658ba6c381>

<img src="https://thijoubert.github.io/assets/img/posts/2021-08-24_InformationBarriers-OneDrive_8.png" >



## New sharing link to “specific people”

Our user then wants to share a document only to specific people. 

He can search for users belonging to segments associated with his OneDrive. 

<img src="https://thijoubert.github.io/assets/img/posts/2021-08-24_InformationBarriers-OneDrive_9.png" >

However, it cannot find users belonging to non-compatible segments (or not associated with the space), for example below for "Dwarf". 

<img src="https://thijoubert.github.io/assets/img/posts/2021-08-24_InformationBarriers-OneDrive_10.png" >

If he fills in the email address of our user directly, we can see that the names of unauthorized collaborators appear in red. 

<img src="https://thijoubert.github.io/assets/img/posts/2021-08-24_InformationBarriers-OneDrive_11.png" >

If he clicks on "Send link" or "Copy link", he will get an error message similar to those mentioned above. 

<img src="https://thijoubert.github.io/assets/img/posts/2021-08-24_InformationBarriers-OneDrive_12.png" >

It may be appropriate to point out that the user can always share it by email as a copy in an email.
{: #myid .alert .alert-info .p-3 .mx-2 mb-3}



<br/>
# What user experience for Teams a OneDrive combined with a segment and Information Barriers policy? 

The user experience is very similar between OneDrive and Teams for content sharing. 

Again, let's take a concrete example. For this, I have removed the "Goblins" segment from OneDrive from "Goblin - Army 1". Let's try to share content to "Goblin". 

If we share a document from our OneDrive, a link is automatically created (**we could even create a link "Anyone with the link"!!!**). 

However, our "Goblin" user will not be able to open the document, nor copy the link. The error message being brief, this could lead to some frustration. 

<img src="https://thijoubert.github.io/assets/img/posts/2021-08-24_InformationBarriers-OneDrive_13.png" >

<img src="https://thijoubert.github.io/assets/img/posts/2021-08-24_InformationBarriers-OneDrive_14.png" >

<img src="https://thijoubert.github.io/assets/img/posts/2021-08-24_InformationBarriers-OneDrive_15.png" >

If our user then tries to modify the link, we can see that "Goblin" appears in red because it is not allowed to access content on the OneDrive. 

Note that Goblin - Army 1 can communicate with Goblin but cannot send it any content (see screenshot below). 

<img src="https://thijoubert.github.io/assets/img/posts/2021-08-24_InformationBarriers-OneDrive_16.png" >




# Other feedback:
When applying Information Barriers policies to OneDrive: 

- Previously created share links are not removed, but a non-compatible user will no longer be able to access the shared item. 
- Some users with whom items have been shared still appear in the SharePoint cache, especially users without segments.
- Users with whom items have been shared are not removed from the library contacts.

<img src="https://thijoubert.github.io/assets/img/posts/2021-08-24_InformationBarriers-OneDrive_17.png" >

If a user's segment is removed from their OneDrive, but other segments are still associated, that user will no longer have access to their space.

<img src="https://thijoubert.github.io/assets/img/posts/2021-08-24_InformationBarriers-OneDrive_18.png" >