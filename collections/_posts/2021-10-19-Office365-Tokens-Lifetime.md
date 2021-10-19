---
title: "How to manage Office 365 sessions lifetime"
subtitle:
excerpt: How long are my users logged in? How much flexibility do I have to revoke a compromised user's access? 
tags:
  - Azure AD
  - Conditional Access
  - Microsoft 365
  - Security
header_img : "./assets/img/posts/2021-10-19_Office365-Tokens-Lifetime_0.png"
---


How long are my users logged in? How much flexibility do I have to revoke a compromised user's access?  

The problem is simple, but the answer is complex: what **balance can be found between user experience and session lifetime**?
- The security answer is obviously: "you have to set the shortest possible duration" (it is not uncommon to want to set a day or even few hours)
- On the other hand, the user experience could be so poor that it would paradoxically lead to a decrease in security. The two direct consequences are that users authenticate without thinking (risk of phishing) or use weak passwords. 

In order to control the lifetime of user sessions and to manage the associated risks, Microsoft offers several options: 
- Option 1: "**Keep me signed-in**" deactivation
- Option 2 : **Conditionnal Access Policy** with "sign-in frequency" and "persistence" 
- Option 3: **Continuous Access Evaluation** (preview)
- Option 4 : Configuration of **access token lifetime** (preview)
- To go further : **Resilience defaults** (preview)

Understanding the different Microsoft options is key to finding the best balance for your organization. 


<br/>
# By default, if you lift the hood of Azure AD, what will you find?

When a user authenticates to an Azure AD application (e.g. Exchange Online), he will get a pair of tokens that will allow him to consume the services: 

Access tokens: 
- An **access token is used to authenticate to a protected resource**. It contains several information such as its expiration date and its permissions (ex: Mail.ReadWrite). An access token can be used only for a specific combination of user (ex : Dwarf@xxxx.onmicrosoft.com), client (ex : Outlook), and resource (ex : Exchange Online).
- An access token is always signed, which avoids forging a fake one. 
- As the [access token is a bearer token](https://auth0.com/blog/refresh-tokens-what-are-they-and-when-to-use-them/#Token-Types), any person in possession of an access token can authenticate with the defined permissions
- Access tokens have a lifetime of about 1 hour in the case of a non CAE compatible client or 20-28 hours in the case of a CAE compatible client. We will see in the following article how to modify this value.

Refresh tokens : 
- By default, access tokens are valid for one hour, when they expire the client is redirected to Azure AD to refresh them. That refresh period provides an opportunity to re-evaluate policies for user access.
- If the access request does not violate the policies, then the **refresh token is used to regenerate a new access token / refresh token pair** without having to ask the user to re-authenticate. 
- The refresh tokens have **rolling windows of 90 days.** This means that in case of **continuous use, it never expires**.
- Since January 30, 2021, this lifetime is no longer modifiable.

It is interesting to keep in mind that the **Refresh token is specific to a client but can be used for several resources**. In other words, the refresh token allows you to authenticate to different Azure AD applications (e.g., Exchange Online or Teams) without having to explicitly re-authenticate each time:
- Within a web browser.
- Within mobile applications on a mobile device with Microsoft Authenticator installed.
- On a registered workstation, Hybrid Azure AD Join or Azure AD Join.

In the case of an endpoint known by Azure AD (last two cases above), a Primary Refresh Token will be generated at each connection on the user's workstation (with or without multi-factor claim). It will be reused within the different applications for authentication.
{: #myid .alert .alert-info .p-3 .mx-2 mb-3}


<br/>
## What about persistence in a browser?

After logging into a web browser, the user will be presented with the option "Stay signed in"? If he clicks on "Yes", a persistent browser session is created in order to allow the user to remain signed in after closing and reopening their browser window (via the deposit of a cookie in the browser).

<img src="https://thijoubert.github.io/assets/img/posts/2021-10-19_Office365-Tokens-Lifetime_1.png" >

The default settings thus pose a **significant security problem: a user can remain logged in ad vitam eternam on any terminal in case of continuous use**.


<br/>
## In which cases are the tokens revoked? 

Historically, **access tokens cannot be revoked** (see option 3 : Continuous Access Evaluation for the updates).

In most cases, **refresh tokens are revoked by the server because of a change in credentials, user action, or admin action**.

Microsoft details in its [documentation the different conditions for revoking a refresh token](https://docs.microsoft.com/en-us/azure/active-directory/develop/refresh-tokens): 
- Password expires
- Password changed by user	
- User does SSPR		
- Admin resets password	
- User revokes their refresh tokens via PowerShell
- Admin revokes all refresh tokens for a user via PowerShell
- Single sign-out on web	

**Note that only explicit revocations of refresh tokens via PowerShell can guarantee the revocation of ALL refresh tokens**, regardless of the type of authentication (password or cookies) or client (public or confidential).



<br/>
# What are the options for controlling session lifetimes? 

The possibilities to manage session lifetimes will mainly depend on the presence of Azure AD P1 licenses for the organization's users. Without a Premium license, the functionalities are extremely limited.


<br/>
## Option 1: Disabling "Keep me signed-in"? 

It is possible to hide the keep me signed-in feature in the organization's branding settings. 

[If the feature is disabled,](https://docs.microsoft.com/en-us/azure/active-directory/fundamentals/keep-me-signed-in) users will no longer be able to choose to have a persistent session (i.e. stay logged in when the browser is closed).

<img src="https://thijoubert.github.io/assets/img/posts/2021-10-19_Office365-Tokens-Lifetime_2.png" >

It is important to note that even if an organization decides to create multiple company branding for the different languages of the organization, there can only be one Keep me signed-in configuration.


<br/>
## Option 2 : AAD P1 - Politique d'accès conditionnel avec "sign-in frequency" et "persistence". 

Conditional access policies allow you to impose [a "sign-in frequency", and "persistence" behavior for web sessions](https://docs.microsoft.com/en-us/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime). This is **THE method to control when users should re-authenticate to the platform**.

Note that if a third party Identity Provider (or IdP) is in charge of user authentication, it can only allow access to the platform. On the other hand, once inside, the default session durations apply and cannot be managed without one of the options below. 
{: #myid .alert .alert-info .p-3 .mx-2 mb-3}

This feature replaces the ability to modify refresh token lifetimes which has been removed as of January 31, 2021.

<img src="https://thijoubert.github.io/assets/img/posts/2021-10-19_Office365-Tokens-Lifetime_3.png" >

**If the "sign-in frequency" parameter is active:** a refresh token older than the set "sign-in frequency" will be invalidated during its evaluation. The user will have to re-authenticate and will see the following message: 

<img src="https://thijoubert.github.io/assets/img/posts/2021-10-19_Office365-Tokens-Lifetime_4.png" > 

The sign-in frequency works for clients that are compatible with OAuth2 and OpenID Connect. Microsoft web or thick client applications are thus compatible if modern authentication is enabled. 

However, Microsoft puts two points of attention forward:
- If you have Azure AD joined, hybrid Azure AD joined, or Azure AD registered devices, when a user unlocks their device or signs in interactively, this event will satisfy the sign-in frequency policy as well, as a new Primarity Refresh Token will be created. 
- If "Remember MFA on trusted devices" is enabled, be sure to disable it before using Sign-in frequency, as using these two settings together may lead to prompting users unexpectedly.

**If the "Persitent browser session" setting is enabled**: the conditional access policy overrides the Keep me signed-in option chosen by the user.


<br/>
## Option 3: Continuous Access Evaluation (preview)

In order to limit the risk related to the 1 hour window for access tokens, Microsoft is currently deploying the "[Continuous Access Evaluation](https://docs.microsoft.com/en-us/azure/active-directory/conditional-access/concept-continuous-access-evaluation)". 

**On October 31st, the feature will be activated by default for all tenants. It is still possible to disable it via :** 

<img src="https://thijoubert.github.io/assets/img/posts/2021-10-19_Office365-Tokens-Lifetime_5.png" >

This new feature will allow to revoke an access token and to force a re-authentication of the user in case of an event or a context change for compatible clients. 

**In the case of an event occurrence**: Services are now informed in case of a critical event: 
- User Account is deleted or disabled
- Password for a user is changed or reset
- Multi-factor authentication is enabled for the user
- Administrator explicitly revokes all refresh tokens for a user
- High user risk detected by Azure AD Identity Protection

If such an event occurs, the service will force the user to re-authenticate.

<img src="https://thijoubert.github.io/assets/img/posts/2021-10-19_Office365-Tokens-Lifetime_6.png" >

**In case of a context change:** Exchange Online, SharePoint Online, Teams and Graph services retrieve a copy of the conditional access policies. In case of a context change detected by the service, it will force the user to re-authenticate.

This functionality is currently only available for the "location" condition, and for IP-based named locations. Locations defined in the Azure MFA legacy portal or via countries are not supported today. 

<img src="https://thijoubert.github.io/assets/img/posts/2021-10-19_Office365-Tokens-Lifetime_7.png" >

In parallel to this improvement of the security posture, **Microsoft has changed the default values of the access token lifetimes**:
- For a CAE-compatible client: **About 1h** 
- For a non CAE-compatible client: **Between 20 and 28 hours depending on the service**

The increase of the access tokens lifetimes **can pose a security problem because the extraction of an access token with a long lifetime would make it exploitable on a long duration** (except on a terminal present in the named locations) which is not really in the logic of the Zero Trust. Let's bet that Microsoft will soon make the approach evolve. 

In my tenant, the lifetime of the access token is always one hour. I suppose that this evolution will take place after the General Availability on October 31st. At that time I will test the behavior of the 20-28 hours against the sign-in frequency. But I am willing to bet that the sign-in frequency will not be honored if it is shorter. 
{: #myid .alert .alert-info .p-3 .mx-2 mb-3}

It is interesting to note that Microsoft has added a field in the Azure AD sign-in logs to indicate a sign-in with Continuous Access Evaluation. 

<img src="https://thijoubert.github.io/assets/img/posts/2021-10-19_Office365-Tokens-Lifetime_8.png" >


<br/>
## Option 4 : AAL P1 - Configuration of the access token lifetimes (preview)

With the introduction of Continuous Access Evaluation, it may be interesting to think about the lifetimes of access tokens, if the default values defined by Microsoft do not suit you (1h for non-CAE client compatible and up to 28h for CAE client compatible). 

To modify them, it is necessary to define a token policy and to apply it : 
- Either by default to all applications
- Or to a specific application

This [creation is done via PowerShell today](https://docs.microsoft.com/en-us/azure/active-directory/develop/configure-token-lifetimes#create-a-policy-for-web-sign-in). An example below: 

```powershell
# Creation of the policy: 15 min
$policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"00:15:00"}}') -DisplayName "TokenLifetimePolicy-15min" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"

# Collect of the Azure AD Policy
Get-AzureADPolicy | Select *

# Application of the token policy to Exchange Online
$sp = Get-AzureADServicePrincipal -Filter "DisplayName eq 'Office 365 Exchange Online'"
Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id 
```

However, it is important to note that **ALL new access tokens will respect this new value**. It is not possible for the moment to set a new value for clients for non-CAE compatible clients and for CAE compatible clients on the other hand.

In my tenant, the lifetime of the access token is always one hour, even when applying a policy to an application. I assume that this evolution will take place after the General Availability on October 31.
{: #myid .alert .alert-info .p-3 .mx-2 mb-3}


<br/>
## To go further : Resilience defaults (preview)

Microsoft introduced in late 2020, [Azure AD Authentication Backup](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/99-99-uptime-for-azure-active-directory/ba-p/1999628). This feature allows the renewal of current sessions in the event of an outage of the primary authentication service (<0.01% of the time according to Microsoft SLAs), with the known context of the previous authentication. 

During such an event, compatible conditional access policies are re-evaluated before regenerating the token. The following conditions are not supported today: 
- Group membership
- Role membership
- Sign-in risk
- User risk
- Country location (resolving new IP or GPS coordinates)

Microsoft has recently introduced [resilience defaults](https://docs.microsoft.com/en-us/azure/active-directory/conditional-access/resilience-defaults) to control unsupported conditional access policies that can be bypassed as part of an outage. If resilience defaults are disabled, and any of the above conditions cannot be evaluated, access will be denied.

<img src="https://thijoubert.github.io/assets/img/posts/2021-10-19_Office365-Tokens-Lifetime_9.png" >

**Resilience defaults are enabled automatically and can be disabled in conditional access policies.**

<img src="https://thijoubert.github.io/assets/img/posts/2021-10-19_Office365-Tokens-Lifetime_10.png" >

Note that Azure AD Authentication Backup is compatible with Continuous Access Evaluation. An access token that should be revoked under normal circumstances will also be revoked if the primary authentication service is outaged.



<br/>
# Which best practices to control session lifetimes? 

1. Use conditional access policies to impose a sign-in frequency based on populations (users vs. admins), services and context
1. Impose a default sign-in frequency 
1. Leave the Keep me sign-in option available for users (but make users aware)
1. Enable Continuous Access Evaluation (use IP named locations and not Trusted IP)
1. Disable resilience defaults durations for the most sensitive accesses

<img src="https://thijoubert.github.io/assets/img/posts/2021-10-19_Office365-Tokens-Lifetime_0.png" >