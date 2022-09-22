---
title: "Did you know that a document could have 13 different share links?"
subtitle:
excerpt: "Microsoft recently updated the UI of the Sharing for Word Online. It is the occasion to review some mechanichs regarding share links."    
tags:
  - Microsoft 365
  - Office 365
  - OneDrive for Business
  - SharePoint Online
  - Sharing
header_img : "./assets/img/posts/2022-08-29_Did-you-know-a-document-could-have-13-share-links_1.png"
---

 

Microsoft has introduced a change to the Share Dialog UI through the Message Center (MC402119, Published Jul 19, 2022). This evolution is linked to item [93400](https://www.microsoft.com/microsoft-365/roadmap?filters=&searchterms=93400) of the roadmap

**Permissions management has been simplified**, as Microsoft has integrated the "Read - Open in review mode" in the permissions settings. **Three permissions are now available for the end-users: Can Edit, Can Review, Can Edit**. 

<img src="https://thijoubert.github.io/assets/img/posts/2022-08-29_Did-you-know-a-document-could-have-13-share-links_1.png" >

<p align=center>
*New UI for Word Online*
</p>

<img src="https://thijoubert.github.io/assets/img/posts/2022-08-29_Did-you-know-a-document-could-have-13-share-links_2.png" >

<p align=center>
*UI for other appications (including Word in Teams and Word Desktop)* 
</p>

This change seems **mainly aesthetic** as it does not affect the technical properties of the share link created (example below for an anonymous share link in review mode with a password)

```powershell
{
    "id": "e51f844d-a0ec-4baf-a5df-71d8dfd83cad",
    "roles": [
      "read"
    ],
    "hasPassword": true,
    "grantedToIdentitiesV2": [],
    "grantedToIdentities": [],
    "link": {
        "scope": "anonymous",
    "type": "review",
    "webUrl": "https://thijoubert-my.sharepoint.com/:w:/p/dwarf/ETwGgus3rGtNntzrWs6HcGgBHUU79IOQBOn5lN0QPP9Q2A",
    "preventsDownload": false
    }
}
```

<br/>
# Some useful reminders about the share links

This evolution is also an opportunity to (re)discover the mechanics behind the share links: 
- A user (internal or external) **can access a shared content only through the share link**, with the relevant restrictions (and not through the URL)
- A document shared via a share link is **not indexed in Microsoft / SharePoint searches**
- **Multiple share links can be created** with different settings

During my tests, I was surprised to see the following behavior: if a user accesses a document through a share link once, any other access done through another link will keep at least the permissions of the previous link (he will even be able to access the document with the direct URL). In short, If I access a document with “Can Edit” permissions once, even if I access the document with “Can View” permissions I will avec “Edit” permissions. 
{: #myid .alert .alert-info .p-3 .mx-2 mb-3}

These mechanics raised an interesting question: **How many share links can be created for the same item**? 

**The answer seemed high to me: up to 13** (depending on the sharing settings of the OneDrive or SharePoint site concerned). 



<br/>
# For "Anyone with the link": up to 8 links

Depending on the sharing policy that applies to the personal or the collaborative space, a user will be able to create or not links accessible by unauthenticated persons. It will also be possible to force a share link expiration time (after which the link will simply be revoked). 

<img src="https://thijoubert.github.io/assets/img/posts/2022-08-29_Did-you-know-a-document-could-have-13-share-links_3.png" >

There are up to 16 possible combinations to create an anonymous link depending on : 
- **Permission level**: Can Edit, Can Review, Can View
- **Expiration date**
- **Password**
- **Block download**: Yes / True (only for View mode)

Each combination will result in the creation of a new link. A new link with a new password and/or a new expiration time will overwrite the previous settings. 

The notion of expiration time is not a distinguishing feature for a link. This means that if I have an anonymous link in Edit mode with an expiration time of 10 days and I add an anonymous link in Edit mode with no expiration time, the first link will be deleted (note that tenants for which a maximum expiration time is not forced are not very common)
{: #myid .alert .alert-info .p-3 .mx-2 mb-3}

So there are 8 link possibilities: 

|Use case|Scope|Permissions|Password|Expiration date|Block download|Result|
| :-: | :-: | :-: | :-: | :-: | :-: | :-: |
|1|**Anyone with the link**|**Can edit**|No|No|-|Only 1 link for 1 and 2|
|2|**Anyone with the link**|**Can edit**|No|**Yes**|-|Only 1 link for 1 and 2|
|3|**Anyone with the link**|**Can edit**|**Yes**|No|-|Only 1 link for 3 and 4|
|4|**Anyone with the link**|**Can edit**|**Yes**|**Yes**|-|Only 1 link for 3 and 4|
|5|**Anyone with the link**|**Can review**|No|No|-|Only 1 link for 5 and 6|
|6|**Anyone with the link**|**Can review**|No|**Yes**|-|Only 1 link for 5 and 6|
|7|**Anyone with the link**|**Can review**|**Yes**|No|-|Only 1 link for 7 and 8|
|8|**Anyone with the link**|**Can review**|**Yes**|**Yes**|**-**|Only 1 link for 7 and 8|
|9|**Anyone with the link**|**Can view**|No|No|No|Only 1 link for 9 and 10|
|10|**Anyone with the link**|**Can view**|No|**Yes**|No|Only 1 link for 9 and 10|
|11|**Anyone with the link**|**Can view**|**Yes**|No|No|Only 1 link for 11 and 12|
|12|**Anyone with the link**|**Can view**|**Yes**|**Yes**|No|Only 1 link for 11 and 12|
|13|**Anyone with the link**|**Can view**|No|No|**Yes**|Only 1 link for 13 and 13|
|14|**Anyone with the link**|**Can view**|No|**Yes**|**Yes**|Only 1 link for 13 and 14|
|15|**Anyone with the link**|**Can view**|**Yes**|No|**Yes**|Only 1 link for 15 and 16|
|16|**Anyone with the link**|**Can view**|**Yes**|**Yes**|**Yes**|Only 1 link for 15 and 16|

<img src="https://thijoubert.github.io/assets/img/posts/2022-08-29_Did-you-know-a-document-could-have-13-share-links_4.png" >


<br/>
# For the links "People in ORGANIZATION with the link": 4 links

Organization links are **always accessible and cannot be hidden**. The notions of expiration date and block download do not exist.

<img src="https://thijoubert.github.io/assets/img/posts/2022-08-29_Did-you-know-a-document-could-have-13-share-links_5.png" >

So there are simply 4 combinations and 4 possible share links.

|Use case|Scope|Permissions|Password|Block download|Expiration date|Result|
| :-: | :-: | :-: | :-: | :-: | :-: | :-: |
|17|**People in ORGANIZATION with the link**|**Can edit**|-|-|-|1 link|
|18|**People in ORGANIZATION with the link**|**Can review**|-|-|-|1 link|
|19|**People in ORGANIZATION with the link**|**Can view**|**-**|**Yes**|-|1 link|
|20|**People in ORGANIZATION with the link**|**Can view**|-|No|-|1 link|



<br/>
# For "People with existing access" links: 1 link

The link "People with existing access" is the most explicit. It allows you to simply give the **location to all the users who already have access to the document**. 

<img src="https://thijoubert.github.io/assets/img/posts/2022-08-29_Did-you-know-a-document-could-have-13-share-links_6.png" >

As its name indicates, there is only 1 combination and therefore only 1 possible share link:

|Use case|Scope|Permissions|Password|Block download|Expiration date|Result|
| :-: | :-: | :-: | :-: | :-: | :-: | :-: |
|21|**People with the link**|**Can edit**|-|-|-|1 link|



<br/>
# For "Specific people" links: 4 links

Finally, the last link intended to give rights by name has always seemed to me **the most complex to use**. It provides rights similar to those you could give via historical SharePoint permissions (with less settings). In general, I consider it as a simple functionality to **share data temporarily**. 

<img src="https://thijoubert.github.io/assets/img/posts/2022-08-29_Did-you-know-a-document-could-have-13-share-links_7.png" >

As for the organization links, there are 4 possibilities with 4 possible share links.

|Use case|Scope|Permissions|Password|Block download|Expiration date|Result|
| :-: | :-: | :-: | :-: | :-: | :-: | :-: |
|22|**Specific user**|**Can edit**|-|-|-|1 link|
|23|**Specific user**|**Can review**|-|-|-|1 link|
|24|**Specific user**|**Can view**|-|**Yes**|-|1 link|
|25|**Specific user**|**Can view**|-|No|-|1 link|

<br/>
*Thus, depending on the sharing policy of the shared collaborative space it will be possible to have between 9 and 17 share links for the same document.*

More information on the different settings on Microsoft support page: [Share SharePoint files or folders (microsoft.com)](https://support.microsoft.com/en-us/office/share-sharepoint-files-or-folders-1fe37332-0f9a-4719-970e-d2578da4941c#:~:text=People%20with%20existing%20access%20can,somebody%20who%20already%20has%20access.)
