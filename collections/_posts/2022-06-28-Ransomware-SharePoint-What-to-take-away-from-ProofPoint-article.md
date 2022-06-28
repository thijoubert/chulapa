---
title: "Ransomware and SharePoint Online: What to take away from the ProofPoint article?"
subtitle:
excerpt: "SharePoint versioning can be used to ransom files and items, if specific requirements are met."    
tags:
  - Microsoft 365
  - Monitoring
  - Office 365
  - Ranswomare
  - Security
header_img : "./assets/img/posts/2022-06-28_Ransomware-SharePoint_1.png"
---

The subject of ransomware always arouses great passion. This is completely understandable given the damage caused by certain attacks (e.g. NotPetya, Wannacry). 

This risk must also be taken into account for Microsoft's collaborative suite, Office 365, although it is less likely than for historical on-premises services. Indeed, the interest of attackers can only increase because on-premises services are more and more secure and sensitive data have been massively migrated to cloud services for several years. 

A “ransomware” can affect SharePoint Online in two ways: 
- **Either by encrypting Microsoft servers**: this scenario seems highly unlikely, and the world would have other problems than "simple" company data in my humble opinion;
- Or by “**encrypting**” **the data**: this scenario, which is the subject of this article, is not really encryption but rather "version skipping".  

For the record, The Collective Consulting has published in 2019 a [very interesting article on the Exchange Online part](https://www.thecollective.eu/en/insights/exchange-online-ransomware-attacks-is-it-a-real-threat). 



# A reminder of the ransomware scenario presented by ProofPoint

Proofpoint recently wrote a blog and communicated about the "discovery of a way to ransom OneDrive for Business and SharePoint Online files." This attack would take place in 5 steps: 
- Compromising the user's identity to access data
- Exfiltration of data to keep a copy of the files
- Modification of the library parameters to keep only one version
- Modification of data to "encrypt it" by modifying the version history
- Extortion of the victim to ransom the disclosure or recovery of data



# Analysis of the scenario presented

## What should we think about the scenario? 

**It is always good to put the subject back on top of the pile because it is often forgotten even though** no flaw has been discovered (functional or technical). The described procedure exploits the native versioning functionality of SharePoint Online: 
- **If a document library is configured to keep the last n versions, the writing of the n+1st version will delete the 1st one definitively** (without making it go through the various recycle garbage can mechanisms)
- **When the number of versions is modified, the next modification of an item will lead to the deletion of the N-M oldest versions** (N being the number of current versions and M the new number of configured versions).
- To be noted: SharePoint Online will keep at least one version.  

I had already at least mentioned this scenario twice, in 2021: 
- In an article about [Office 365 backup](https://www.thijoubert.com/2021-06/Backup/)
- In a conference about the [necessary hardening measures for the Power Platform](https://github.com/thijoubert/Sharing/blob/main/Presentations/2021-12-15_Durcissement%20Power%20Platform%20tous%20concern%C3%A9s_FR.pdf) 
{: #myid .alert .alert-info .p-3 .mx-2 mb-3}

Although this problem is not new, the approach of the article remains interesting because it continues to raise awareness of the functional limits of Saas. 

The Cloud is not a magical place. It is not because we migrate to Office 365 that Microsoft will secure everything for us. It's important to remember that Microsoft, like all SaaS vendors, has a duty to provide a secure solution; but it's up to its customers to manage the configuration of the service, protect identities and data, and detect abnormal behavior. This is commonly referred to as the "shared responsibility model".


## What about the "encryption" of files? 

To scroll through the versions, an attacker will have several possibilities depending on the compromise: 

**Direct compromise when the user's identity is usurped, following a phishing or a brute force attack:**
- **Modification in graphical interfaces**: very unlikely to modify many files a large number of times.
- **Modification through PowerShell, Graph API, or SharePoint CSOM API**

**Indirect compromise when part of the user's rights are granted to another user or an application:**
- **Modification via Microsoft Graph API** (with a delegation to a third-party application): the most likely scenario, however, modifying a large number of files a large number of times would quickly lead to reaching the Graph API's Throttling limit. This limit would lead to a cap on the number of "encrypted" files per hour, so the damage could be “contained” (limit could be x00 000 modifications per day).
- **Modification via PowerShell CSOM** (with a delegation to a third-party application): similar to the previous scenario, although less practical.
- **Modification via Power Automate** (with a new connection to a third-party application): the tests I did last year led me to believe that a ransomware scenario would be less effective than in Graph because of the latencies of the solution.


## What about the efficiency of this attack?  

### Either the attacker knows what he is looking for, or he will focus on the sensitive data...

This first case will be very complicated to prevent: 
- The attacker will not be subject to technical limitations like throttling. 
- It will also be complicated to detect and block the identity used before the encryption of some sensitive files. Indeed, detection tools (Microsoft or third parties) generally rely on logs and API Management events that are generated a few minutes after the action is performed, i.e. after the modification of some files. 

To note: if retention is enabled for the item or the SharePoint site (thanks to Purview Information Governance), a copy of all versions will be kept in the Preservation Hold Library of the SharePoint site. Retention will force the preservation of information, and therefore protect against data loss. On the other hand, it is better to keep in mind restoring information from the PHL is not easy, and to avoid relying only on this feature.  
{: #myid .alert .alert-info .p-3 .mx-2 mb-3}

### Or he doesn't know what it is looking for and it will scroll through the versions randomly or linearly site by site, library by the library, and so on.

This second case will require reducing the number of versions kept to be efficient and maximize the success of such an attack. 

However, this modification requires two prerequisites: 
- The compromised user must have Full Control rights on the document libraries to be able to modify their parameters;
- The attacker must be able to access the library settings: 
  - Via the graphical interfaces, a user can set the limit of the versions between 100 and 50 000 (in case of a direct compromise)...

<img src="https://thijoubert.github.io/assets/img/posts/2022-06-28_Ransomware-SharePoint_1.png" >

   - However, there is no lesser limit for a modification through SharePoint PNP PowerShell (in case of a direct compromise) or via the SharePoint API (in case of an indirect compromise with the delegate consent All.SitesManage which does not require an admin consent): 

<img src="https://thijoubert.github.io/assets/img/posts/2022-06-28_Ransomware-SharePoint_2.png" >


The **implementation of best security practices** (see next section) should therefore greatly reduce the probability of this risk.



# What measures should be put in place to protect against this type of risk?

### Mastering the configuration: to avoid errors 

As usual, the first thing to do will be to master the configuration of the Office 365 tenant, via an initial configuration and a follow-up over time of the evolutions. The objective will be to ensure that users or applications cannot too easily make errors or malicious actions. We will thus speak of : 
- **Azure Active Directory configuration** (including delegation of consent to third-party applications)
- **Power Platform configuration** (including isolation of tenant to third-party applications or workflows)
- **SharePoint configuration** (including site and library templates) 
- **Data retention** (including minimum data retention via Purview Information Governance and tenant cleansing)

## Securing identities: always a must-have

The second step to master, which should already be mastered by everyone (unfortunately this is not the case ...), is to protect users to avoid compromise:
- Direct compromise: **Password strength and other authentication factors**, **Multi-factor authentication**, **Conditional access**, etc.
- Indirect compromise: **Control of settings**, etc.

## Backing up data: the eternal debate

As I mentioned earlier, it is the responsibility of Microsoft's customers to ensure that their data is well protected. Backup vendors will legitimately take the opportunity to remind you that it is necessary to keep a copy of the data outside the platform.

Beyond the recurrent debate on this subject, on which I have already written, it will be necessary to define the scope and the number of copies that must be kept: 
- **Keeping the last copy will be useless**: if the attackers are clever, they will only have to modify the document twice.
- On the other hand, **keeping a copy of all 500 N native versions on the whole tenant will be extremely expensive in terms of storage**, and will be very complicated to restore... not to mention the ecological impact of such an approach. 

In short, we will come back to the best cost/risk/benefit balance to define the most appropriate strategy for each organization. 

## Detecting abnormal behavior: an interesting compromise

On the question of data backup, **a compromise could be to target only the most critical scopes, to make a regular backup on a limited number of versions** (e.g. daily and not all versions continuously), and to detect abnormal behavior (e.g. changes in the parameters of a site or a list, and actions performed by a user or application identity). 

## Raising user awareness: to get them on board with the company's strategy

**Security is everyone's business**. Whether it is to counter external attacks or to avoid mistakes, it is necessary to make users aware of collaborative tools, good practices, and mistakes to avoid. For example, and as ProofPoint rightly reminds it, communicating to users when changing site settings or a document library can be interesting to limit the impact of a future attack. 

Users are on the **front line and must become an asset**, and not a burden anymore in cybersecurity.
