---
title: "Continuous Access Evaluation, what changed for you organisation on October 31?"
subtitle:
excerpt: Microsoft just released the Continuous Access Evaluation for main Office 365 workloads. How does it affect you tenant?  
tags:
  - Azure AD
  - Conditional Access
  - Microsoft 365
  - Security
header_img : "./assets/img/posts/2021-10-19_Continuous-Access-Evaluation_6.png"
---


I already briefly presented the Continuous Access Evaluation in a previous article "[How to manage Office 365 sessions lifetime](https://www.thijoubert.com/2021-10/Office365-Tokens-Lifetime/)". I come back in this article on the changes pushed by Microsoft on October 31


# Reminder about Continuous Access Evaluation

In order to limit the risk related to the 1-hour lifetime for the access tokens, Microsoft is currently deploying the “[Continuous Access Evaluation](https://docs.microsoft.com/en-us/azure/active-directory/conditional-access/concept-continuous-access-evaluation)”. This new feature will allow to revoke an access token and to force a re-authentication of the user in case of an event or a context change for compatible clients.

**In the case of an event occurrence**: Services are now informed in case of a critical event:
- User Account is deleted or disabled
- Password for a user is changed or reset
- Multi-factor authentication is enabled for the user
- Administrator explicitly revokes all refresh tokens for a user
- High user risk detected by Azure AD Identity Protection
If such an event occurs, the service will force the user to re-authenticate.

<img src="https://thijoubert.github.io/assets/img/posts/2021-10-19_Continuous-Access-Evaluation_1.png" >


**In case of a context change:** Exchange Online, SharePoint Online, Teams and Graph services retrieve a copy of the conditional access policies. In case of a context change detected by the service, it will force the user to re-authenticate.
This functionality is currently only available for the “location” condition, and for IP-based named locations. Locations defined in the Azure MFA legacy portal or via countries are not supported today.

<img src="https://thijoubert.github.io/assets/img/posts/2021-10-19_Continuous-Access-Evaluation_2.png" >

In parallel to this improvement of the security posture, **Microsoft has changed the default values of the access token lifetimes**:
- For a CAE-compatible client: **Between 20 and 28 hours depending on the service**
- For a non CAE-compatible client: **About 1h**

The increase of the access tokens lifetimes **can pose a security problem because the extraction of an access token with a long lifetime would make it exploitable on a long duration** (except on a terminal present in the named locations) which is not really in the logic of the Zero Trust. Let’s bet that Microsoft will soon make the approach evolve.

If the new token lifetime is not convenient for you, [you can modify it](https://www.thijoubert.com/2021-10/Office365-Tokens-Lifetime/) 

In my tenant, the lifetime of the access token is always one hour. ☹

It is interesting to note that Microsoft has added a field in the Azure AD sign-in logs to indicate a sign-in with Continuous Access Evaluation.

<img src="https://thijoubert.github.io/assets/img/posts/2021-10-19_Continuous-Access-Evaluation_3.png" >

Note that Microsoft Admin Center now precises that you can refresh at once all the tokens (and not only the refresh tokens): 

<img src="https://thijoubert.github.io/assets/img/posts/2021-10-19_Continuous-Access-Evaluation_9.png" >



<\br>
# What happened on October 31, 2021?

At the Ignite Fall 2021, Microsoft announced that the CAE would be GA at the end of 2021, but it seems that this is already the case since October 31 (as announced during the preview). 

Indeed, on October 31st, a change has taken place in Azure AD. 2 cases are possible: 

- Case 1: Either you had activated the preview or did not touch anything
- Case 2: Either you had activated the preview on some users



## Case 1: If you had activated the preview or did not touch anything

The page showing the preview in the "[Security](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/)" blade is no longer available.

<img src="https://thijoubert.github.io/assets/img/posts/2021-10-19_Continuous-Access-Evaluation_4.png" >

**Continuous Access Evaluation has been enabled by Microsoft for all CAE-compatible clients by default.** It is still possible to change the behavior in the Conditional Access policies. 


## Case 2: If you activated the preview

If, like me, you had activated the preview on a reduced perimeter (on my side on two users: Dwarf and Elf), [the page linked to the preview in Security](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/ContinuousAccessEvaluation) is still accessible, but a message indicates that the functionality has been migrated to the Conditional Access interface.

<img src="https://thijoubert.github.io/assets/img/posts/2021-10-19_Continuous-Access-Evaluation_5.png" >

It is required to click on "Migrate" to activate the new interface.

<img src="https://thijoubert.github.io/assets/img/posts/2021-10-19_Continuous-Access-Evaluation_6.png" >

After migrating the CAE to the Conditionnal Access, a new conditional access policy is observed: "**CA policy created from CAE settings**". 

**This new policy disables the Continuous Access Evaluation for all users, except for the users included in the preview.** Here are my two users Dwarf and Elf.

<img src="https://thijoubert.github.io/assets/img/posts/2021-10-19_Continuous-Access-Evaluation_7.png" >

Note that if you had activated the preview for all users, the behavior is identical to case 1.


<\br>
# How to modify the CAE after October 31? 

From the two previous cases we understand that the CAE is activated by default for all users for whom it is not explicitly deactivated, via the Conditional Access.

The Conditional Access offers two possibilities in the session controls: 
- Disable: This setting is only effective if CAE is disabled for all applications. 
- Strict Enforcement: CAE is enforced for all applications. A user will not be able to use a non CAE-compatible application.

As CAE is currently only applied to SharePoint Online, OneDrive for Business, Exchange Online and Teams, the side effect is limited to Office web apps for the time being. It is still possible to edit documents in Office via the online interfaces of the services mentioned above.  
{: #myid .alert .alert-info .p-3 .mx-2 mb-3}

<img src="https://thijoubert.github.io/assets/img/posts/2021-10-19_Continuous-Access-Evaluation_8.png" >

It is interesting to note that for two concurrent policies, **deactivation takes precedence over "Strict Enforcement"**.