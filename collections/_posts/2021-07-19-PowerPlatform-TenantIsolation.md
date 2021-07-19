---
title: "Power Platform & Tenant isolation: why everyone should have a look at it?"
subtitle:
excerpt: By default, a user of an organisation can create connection to business data from an external Microsoft account without being bothered by the security policies in place. 
tags:
  - Microsoft 365
  - Power Platform
  - Power Automate
  - Power Apps
  - Hardening
header_img : "./assets/img/posts/2021-07-19_PowerPlatform_0.jpg"

---

*As a follow-up to my first article on the Power Platform "How to (really!) managed the access to the Power Platform, several people have asked me about the "tenant isolation" feature. 

Malicious actions (such as extraction) or errors impacting data (email, sharing, downloading) is an existing risk for all organizations using Office 365. When it is done in a unitary way, it may only affect a limited amount of data. However, when it is done automatically (via the Graph API or Power Automate) it can take on much greater proportions. 

In order to prevent this kind of risk, it is necessary to implement hardening and supervision measures.  



# Why should all organizations consider tenant isolation?

To answer this question, let's take a concrete example. 

Let's say an organization has reached a certain level of maturity on its Office 365 tenant (a very small  small number of organizations) :
- Conditional access is deployed and requires a managed device to access Office 365 workloads.
- External shares and Azure B2B / SharePoint B2B guests are managed.
- Automatic forwarding have been disabled.
- Access to Graph / Office 365 APIs is under control.
- DLP rules have implemented in place to limit data leakage.
- A governance has been defined for the Power Platform (including Data Policies adapted to business needs).
- Etc. 

You'd think this organization would be in control of its data circulation. Well, they're going to be missing one little critical thing ... that could bypass all of the above security measures!


## A picture is worth a thousand words... 

In this organization, let's take a user who is leaving his current position. He wants to copy his data from his professional OneDrive to his personal OneDrive, to keep confidential data.  

To do this, he thinks of using a Power Automate workflow to automatically copy any new document. Except that in this case:
- He has neither a Power Automate license nor the authorization to subscribe to a trial license with his corporate account.
- His access to the Power Platform has been blocked by a conditional access policy (see screeshot below).

<img src="https://thijoubert.github.io/assets/img/posts/2021-07-19_PowerPlatform_1.png" >

He then thinks of creating a flow directly from his personal Microsoft account. 

<img src="https://thijoubert.github.io/assets/img/posts/2021-07-19_PowerPlatform_2.png" >

Our user then creates a flow in three steps, in his personal environment:  
- Trigger the flow as soon as a new file is created in the professional OneDrive.
- Retrieve the content of the file.
- Retrieve the name of the file.
To do this, he **creates a connection to his company account**. Being on his managed computer, he is authorized to connect, in full compliance with the conditional access policies. 

<img src="https://thijoubert.github.io/assets/img/posts/2021-07-19_PowerPlatform_3.png" >

From that moment on, he can use the connection from another device than his professional one.
{: #myid .alert .alert-info .p-3 .mx-2 mb-3}

The flow is then activated. As soon as a new document is created in the professional OneDrive, the flow is executed and retrieves the document. 

<img src="https://thijoubert.github.io/assets/img/posts/2021-07-19_PowerPlatform_4.png" >

In summary, the user was able to create a connection to his corporate OneDrive, to retrieve data and export content to his personal OneDrive, via a flow running from his personal account. He was not bothered by any of the hardening or supervision measures in place.

When testing further, we can see that the **data policies defined in the organization's Power Platform environment do not apply**. This is logical, as the flow is executed in the user's personal environment.


## Under the hood, what happened? 

My first thought was that the user should have been blocked by the conditional access policy when the connection was created. 

Going back to the [definition of a connector](https://docs.microsoft.com/en-us/connectors/connectors) ("proxy or a wrapper around an API that allows the underlying service to talk to Microsoft Power Automate, Microsoft Power Apps, and Azure Logic Apps"), the behavior actually looks quite normal. The connection is not made to the Power Automate service, but to the API behind the connector. 

We can see in the sign-in logs that the connection to the : 

- OneDrive For Business is done with "App Service", an HTTP service allowing to interface with web applications or REST APIs.
- Teams is done with "Power Automate AAD App for Teams connector".

<img src="https://thijoubert.github.io/assets/img/posts/2021-07-19_PowerPlatform_5.png" >

Note that it is not possible to block these applications via a conditional access policy, as this would mean blocking all connections (not just incoming and outgoing ones).

My second thought was about the ability of an organization to detect or investigate such a case. 

For the connection:
- In the Office 365 unified audit logs or Azure AD sign logs, I could only detect the sign-in when connecting to Azure App Service (see above).
- The Power Platform Administration Center does not report any information about the connection to the administrators.
- On the other hand, a Power Platform admin can retrieve the existence of the connection via the PowerShell command "Get-AdminPowerAppConnection" (see screenshot below). But I did not see any information about the connection to my personal environment. On the contrary, the environment that appears is the default of my organization…

<img src="https://thijoubert.github.io/assets/img/posts/2021-07-19_PowerPlatform_6.png" >

- On his side, the user can see from his professional account (Data > Connections), the existence of the connection, but will not have any information about the flow used in the personal account either:

<img src="https://thijoubert.github.io/assets/img/posts/2021-07-19_PowerPlatform_7.png" >

<img src="https://thijoubert.github.io/assets/img/posts/2021-07-19_PowerPlatform_8.png" >

<img src="https://thijoubert.github.io/assets/img/posts/2021-07-19_PowerPlatform_9.png" >


For the run of the flow: 
- In "Monitor", the user will not see any trace of the flow run;
- In the Office 365 unified audit logs, accessible by the admins, the retrieval of the document by the flow only appears in a "Downloaded file" log;
- The only interesting information I could find in this log was that the action was performed from a Microsoft IP, linked to the Power Automate service: "40.83.137.154". 

<img src="https://thijoubert.github.io/assets/img/posts/2021-07-19_PowerPlatform_10.png" >


Finally, I wondered how to remove this connection: 

- The user can remove the connection from their professional or personal account by going to Data > Connections ;
- A PowerPlatform administrator in the organization could remove the connection with the "Remove- AdminPowerAppConnection" PowerShell command ... as long as he knows which one is relevant.



# What should we learn from the above example? 

Our scenario thus highlighted the fact that, without additional hardening, everything that had been done to secure data in an Office 365 tenant could be bypassed.

A user could indeed create a connection to business data in a Power Automate flow running in his personal environment. To note: 
- The **connection (and therefore the organization's data) can be exploited from an unsecured context** (network, terminal, etc.).
- The organization's Power Platform **data policies do not apply** to the flow's execution environment.
- The organization can **identify connections, but with will not have any visibility** into the flow and therefore the use of the data.
- The organization will be able to **delete connections for a user, as long as it knows which connections to delete**. 
{: #myid .alert .alert-info .p-3 .mx-2 mb-3}

The worst case scenario would be a hacker **compromising an account** and creating a connection in order to maintain **persistent access** to the user's account. 

The **only solution is to activate the unfortunately unknown "tenant isolation" feature** to block incoming and outgoing connections to company data.



# The solution : turn on "tenant isolation" 
Currently, the [documentation states that it is necessary to make a ticket to the Microsoft support via the Power Platform Admin Center](https://docs.microsoft.com/en-us/power-platform/admin/cross-tenant-restrictions) (PPAC), in order to activate the functionality. 

In my case, support replied within 24 hours. 

**I STRONGLY ENCOURAGE YOU TO PERFORM THIS ACTION ON YOUR TENANT, IN ORDER TO COVER THIS HUGE HOLE IN THE PROTECTION AND SECURITY OF THE TENANT.** 
{: #myid .alert .alert-info .p-3 .mx-2 mb-3}

In the future, it will be possible to activate this feature in **self-service from the PPAC**. The self-service is currently in private preview (although the [documentation specifies a Public Preview since June 2021 and a GA for next September](https://docs.microsoft.com/en-us/power-platform-release-plan/2021wave1/power-platform-governance-administration/planned-features)).

Switching the feature to "On" should automatically make it impossible to create inbound and outbound connections. A Power Platform administrator will however have the possibility to define a whitelist by declaring the domains or GUIDs of the tenants in question. This whitelist will be able to take into account inbound, outbound or both connections. 

Below is a [screenshot shared by Microsoft](https://cloudblogs.microsoft.com/powerplatform/2021/03/02/solve-it-governance-and-admin-challenges-with-new-features-for-power-platform/) for the self-service page:

<img src="https://thijoubert.github.io/assets/img/posts/2021-07-19_PowerPlatform_11.png" >

Note that Microsoft specifies in its documentation a [current limitation](https://docs.microsoft.com/en-us/power-platform/admin/cross-tenant-restrictions#known-issue) (under investigation): tenant isolation does not work for the moment for incoming connections between two different clouds (e.g. a Microsoft commercial subscription and a Microsoft US Governments subscription). 


# What is the user experience?

## In the case of a new workflow : 

When tenant isolation is in place, a user from the organization will not be able to **create a connection** between his personal account (or another organization) and his corporate account. 

When attempting to do so, he will see an **error message explaining that a technical blocking measure has been put in place within the target tenant**. He will also be invited to contact his administrator to whitelist the tenant, if relevant (see screenshot below).  

<img src="https://thijoubert.github.io/assets/img/posts/2021-07-19_PowerPlatform_12.png" >



## In the case of an existing worfklow :

Similarly, when isolation tenant is in place, the impacted flows will appear to fail and therefore be stopped. 

<img src="https://thijoubert.github.io/assets/img/posts/2021-07-19_PowerPlatform_13.png" >

When looking at the details of the "Flow checker", a message is displayed to the user, with the same details as for the new connection.

<img src="https://thijoubert.github.io/assets/img/posts/2021-07-19_PowerPlatform_14.png" >

I observed a duration of one hour, before the appearance of failures for the connections. This must correspond to the lifetime of the Oauth access token linked to the connection.



# Enabled or not by default?

A question to be checked with Microsoft: will tenant isolation be activated by default or not? If the answer is the same as for the Graph APIs ("Users can consent to apps accessing company data on their behalf"), the answer will probably be no...  