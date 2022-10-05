---
title: "Sensitivity labels for Teams (Teams, Shared channels and meetings) are coming? "
subtitle:
excerpt: "I just noticed 15 new properties of sensitivity labels related to teams, shared channels and meetings. What do they suggest for the future?"    
tags:
  - Microsoft 365
  - Office 365
  - Compliance
  - Sensitivity labels
  - Teams
header_img : "./assets/img/posts/2022-09-22_Teams-and-sensitivity-labels_1.png"
---


While playing around with sensitivity labels and PowerShell to see how we can best protect access to a confidential collaborative space, I discovered **15 new properties I didn't know about regarding the actions of sensitivity labels**. 

As a reminder, **sensitivity labels can be used to protect access to data, Microsoft 365 groups or sensitive Power BI reports**. It seems that **Microsoft wants to add a new string to its bow by adding more actions concerning Teams**. 

Among the 15 new actions I saw: 
- 1 of them about private Teams
- 3 of them about Teams with shared channels 
- 11 of them about Teams meetings

<img src="https://thijoubert.github.io/assets/img/posts/2022-09-22_Teams-and-sensitivity-labels_1.png" >



# 1 new action to display non-sensitive private Teams within the search

Since 2020, **Microsoft has made the choice to hide private Microsoft 365 Groups** (and therefore the adjoining Teams and SharePoint Sites) in the various user interfaces and searches. 

Note: a property “ShowInTeamsSearchAndSuggestions” is still available for a Teams (through PowerShell or Graph API), but it is always set up to false, and not modifiable. [This property was deprecated two years ago](https://github.com/MicrosoftDocs/office-docs-powershell/issues/9040), even if the documentation is not up to date. 
{: #myid .alert .alert-info .p-3 .mx-2 mb-3}

With the improvement, It may be possible to search in the future for some private non-sensitive Teams via the :  
- **TeamsAllowPrivateTeamsToBeDiscoverableUsingSearch**



# 3 new actions to restrict the sharing of shared channels 

Shared channels are one of the major features brought by Microsoft lately, and one of the most expected. 

A Teams owner, if authorized via a "Teams policy", can create shared channels and invite people from inside or outside the Teams to communicate and collaborate in this channel (without accessing the rest of the Teams). Can be invited: an internal user, an external person, an internal Teams and soon an external Teams. 

Shared channels have been very well received, but still pose **new challenges in the management of Teams and data** (for example, I can share a private channel with a team but if everyone can join this team then how to limit the distribution of the channel? How to be consistent between strict conditional access policies and the fact that external people can be technically invited in a sensitive channel). 

With these 3 new actions, Microsoft brings first elements of answer: 
- **TeamsChannelSharedWithExternalTenants**: this setting should allow to block the addition of external users or Teams in a shared channel. 
- **TeamsChannelSharedWithPrivateTeamsOnly**: this setting should allow to restrict the channel sharing to private Teams and block the sharing to public groups
- **TeamsChannelSharedWithSameLabelOnly** : this setting should allow to share a private channel only with Teams of the same sensitivity level. An interesting question would be if more sensitive labels could be allowed or if it is necessary to create a dedicated level for collaboration within a scope. 



# 11 new actions to set Teams meeting templates

As for many topics within Microsoft 365, Teams Administrators can create [policies to set up a framework of collaboration](https://learn.microsoft.com/en-us/microsoftteams/meeting-policies-participants-and-guests). From this framework, Teams meetings organizers can harden the settings if needed (but not loosen them).

Historically, [the settings for Teams meetings were managed once the meeting had started](https://support.microsoft.com/en-us/office/change-participant-settings-for-a-teams-meeting-53261366-dbd5-45f9-aae9-a70e6354f88e). This could cause problems in the animation. 

<img src="https://thijoubert.github.io/assets/img/posts/2022-09-22_Teams-and-sensitivity-labels_2.png" >


Late last year, Microsoft introduced the ability to manage Teams meeting options directly from the Outlook invitation (via Meeting Options). More convenient than managing after the meeting has started, but cumbersome if it is necessary to set up each meeting according to the level of confidentiality of the session. 

<img src="https://thijoubert.github.io/assets/img/posts/2022-09-22_Teams-and-sensitivity-labels_3.png" >


With the addition of the 12 new actions for sensitivity labels, it should be possible to predefine meeting templates in advance:  
- **TeamsLobbyRestrictionEnforced**: This setting should define if **TeamsBypassLobbyForDialInUsers** and **TeamsLobbyBypassScope** settings are activated or not for targeted Teams;
- **TeamsBypassLobbyForDialInUsers**: This setting should define if Dial-in users by-pass the lobby automatically. It is recommended to block it for all use cases; 
- **TeamsLobbyBypassScope**: This setting should define who gets into the meeting directly, and who should wait in the lobby;
- **TeamsPresentersRestrictionEnforced**: This setting should define  if “**TeamsAllowedPresenters**” is activated or not; 
- **TeamsAllowedPresenters**: This setting should define who is allowed to share his / her screen (Everyone, Everyone in the company, Specific people, Organizers only);
- **TeamsCopyRestrictionEnforced**: I am not sure to understand what this setting will do… 
- **TeamsAllowMeetingChat.** This setting should define if the chat is enabled, disabled or enable only during the meeting;
- **TeamsRecordAutomatically**: This setting should define if the meeting is automatically recorded. 
- **TeamsWhoCanRecord**: This setting should define who can record (however I don’t see the link with an existing setting);
- **TeamsVideoWatermark**: This setting should define the watermark to add the Teams recording meeting (but I have not heard of such a feature);
- **TeamsEndToEndEncryptionEnabled**: This setting should define if End-to-end encryption (E2EE) is enabled or not for this meeting. 

**These properties seems interesting on the paper, but I ask to see in real life how it will be possible to define templates to manage presenters and automatic recording.** 

We can also see that some of the Teams meetings properties are not on the list (e.g. allow mic and camera for attendees, provide CART captions, enable language interpretation).  



# A preview feature, soon available? 

Unfortunately, this feature still seems to be limited to public or private preview or progressive roll-out as I didn't see how to modify these properties in PowerShell. 

<img src="https://thijoubert.github.io/assets/img/posts/2022-09-22_Teams-and-sensitivity-labels_4.png" >

Nothing appears either in a Microsoft documentation or in the Compliance Center: it is only possible to configure actions for items (emails, Office documents or Power BI reports). 

<img src="https://thijoubert.github.io/assets/img/posts/2022-09-22_Teams-and-sensitivity-labels_5.png" >



*Although the settings to manage shared channels seem to me to be essential, in order to bring consistency to the most sensitive Teams, I am not yet convinced by the use of labels to bring protection actions to Teams meetings. But I remember that this feature was important for several of my former customers to define Teams meeting policies and help users who are not familiar with the different Outlook and Teams interfaces.*