---
title: "How to use Power Automate and Adaptive Cards to empower end-users to check for suspicious activity, such as anonymous links"
subtitle:
excerpt: "A combination of Defender for Cloud Apps, Power Automate, Graph API, Adaptive Cards can bring huge opportunity to raise awareness about specific activity"    
tags:
  - Defender for Cloud Apps
  - Graph API
  - Microsoft 365
  - Office 365
  - Power Automate
  - Sharing
header_img : "./assets/img/posts/2022-07-29_How-to-empower-users-check-suspicious-activity_15.png"
---


As I mentioned in a [previous article, the use of anonymous links may be an unavoidable need](https://www.thijoubert.com/2022-07/How-to-track-anonymous-links/) for companies (calls for tender, public documentation, etc.). 

**Rather than blocking this possibility for all users**, whatever the need, the collaborative resource, and the level of sensitivity, **it may be interesting to set up a framework that respects the needs of users and good security practices**: 

- Limit the validity of anonymous links in time
- Limit the Sites / Teams / OneDrive concerned
- Limit the users involved
- Implement Office DLP rules to limit the sharing of extra-sensitive data externally. 

Even if all of these measures are put in place, it will be necessary to **ensure that those authorized to make anonymous links do not make a mistake that could expose sensitive data or a large number of files if a folder is shared**. 

For this, the users themselves have a fundamental role. Security teams will not be able to know if a share is legitimate, if a share is an error, etc. 

We'll look at how a combination of Defender for Cloud Apps (ex-MCAS) and Power Automate can provide more collaboration capabilities while integrating them into the company's security strategy. One of my goals was also to use **Adaptive Cards to make processing as embedded as possible in Teams**.


<br/>
# Setting up a file policy on Defender for Cloud Apps
For this example, we'll start with something simple: let's assume that the controls mentioned earlier were implemented, and **let's focus on the items exposed via an anonymous link in OneDrive**. 

The first thing to do, once [Defender for Cloud Apps is configured](https://docs.microsoft.com/en-us/defender-cloud-apps/general-setup), is to **create a File policy**. File policies do not allow you to control and block user actions in real-time (because Microsoft scans files at initialization and then once they are modified), but they do **allow you to take governance actions *a posteriori*** depending on the content and exposure of a data, which is exactly what we want to do here. 

Note: the provision of E5 Compliance / Microsoft 365 E5 / Office 365 E5 licenses to all users of the company will be a prerequisite for this use case. 
{: #myid .alert .alert-info .p-3 .mx-2 mb-3}

<img src="https://thijoubert.github.io/assets/img/posts/2022-07-29_How-to-empower-users-check-suspicious-activity_1.png" >

The second thing to do is to target the data you want to protect. Here, **we will simply take all the data exposed by an anonymous link on a OneDrive**:

- File matching all of the following : (Access level equals Public (Internet), Public) AND (App equals OneDrive for Business) 
- Apply to: All files
- Select user groups: All file owners
- Inspection method: None

Of course, to exploit the full potential of the framework Purview Information Protection, we could use many complex use cases with specific types of data and sensitive users. 

<img src="https://thijoubert.github.io/assets/img/posts/2022-07-29_How-to-empower-users-check-suspicious-activity_2.png" >

As the built-in **governance actions are intended to do remediation actions** without human intervention and are **quite limited** (e.g., it is not possible to delete only some links but to leave the others), it is necessary to go through a **Power Automate to contact the users**. 

- By clicking on "Create an alert for each matching file" > "Send alerts to Power Automate", you can select a flow in the drop-down list. 
- If no flow is proposed, you can create a new one from "Manage playbooks". 

Note: As far as I have seen, a flow must be defined in the "Default" environment of the organization to be visible in the playbooks, although I have not seen any trace in the documentation on the subject
{: #myid .alert .alert-info .p-3 .mx-2 mb-3}

<img src="https://thijoubert.github.io/assets/img/posts/2022-07-29_How-to-empower-users-check-suspicious-activity_3.png" >


<br/>
# Configuration of the Power Automate playbook
The **configuration of the Power Automate remains the most difficult part of the exercise**, even if it is accessible with a minimum of time and knowledge of Power Automate / Graph API. The complexity will depend directly on the **granularity and the possibilities you want to offer to your users**. 

My flow is divided into 4 main steps: 

- Step 1: Launching and initialization of variables
- Step 2: Retrieving file and user information
- Step 3: Contacting the user via Teams with an Adaptive Card
- Step 4: Processing the user's response (removing permissions and contacting security teams if necessary)

I present in the next paragraphs the different thoughts I had when I built the flow. 

The flow is available on my GitHub here: [https://github.com/thijoubert/Sharing/tree/main/Power Platform](https://github.com/thijoubert/Sharing/tree/main/Power%20Platform)
{: #myid .alert .alert-info .p-3 .mx-2 mb-3}


<br/>
## Focus on step 1 "Triggering and initialization of variables
I started by creating the connection for the trigger using an "**API token**" created in the "Security Extensions" tab of Defender for Cloud Apps. 

<img src="https://thijoubert.github.io/assets/img/posts/2022-07-29_How-to-empower-users-check-suspicious-activity_4.png" >

I then initialized all the variables needed for my flow:

|Variable|Type|Valeur|
| :- | :- | :- |
|TenantID|String|Id of your Azure AD Tenant|
|ClientID|String|Id of the Azure AD application used to delete unwanted links|
|ClientSecret|String|Secret of the Azure AD application used to delete unwanted links|
|myMcasTenant|String|Url of your tenant (e.g., https://\*\*\*.eu2.portal.cloudappsecurity.com)|
|myMcasToken|String|API token used for the connection|
|fileMcasId|String|*Empty*|
|filePath|String|*Empty*|
|fileName|String|*Empty*|
|fileId|String|*Empty*|
|siteCollectionId|String|*Empty*|

The ClientIdD and ClientSecret must be associated with an Azure AD application that has “File.ReadWrite.All” permissions to remediate. Since these are elevated privileges, **it is recommended to put these credentials in an Azure Key Vault**. 

<img src="https://thijoubert.github.io/assets/img/posts/2022-07-29_How-to-empower-users-check-suspicious-activity_5.png" >


<br/>
## Focus on step 2 "Retrieving file and user information
As the information contained in the alert retrieved by the trigger is quite succinct (e.g., the id of the site / OneDrive, the library, and the document are missing) it is necessary to retrieve them via a dedicated HTTP request. 

To do this, **I extract the file id according to Defender for Cloud Apps which is not the SharePoint one**, and I request the properties. The authentication is done via my variable "myMcasToken" defined previously. 

<img src="https://thijoubert.github.io/assets/img/posts/2022-07-29_How-to-empower-users-check-suspicious-activity_6.png" >

Then, I parsed the JSON received after the HTTP request, to extract the needed information and I update the corresponding variables:

- fileName: body('Parse\_file\_JSON\_-\_Get\_file\_details')?['data'][0]['name']
- filePath: body('Parse\_file\_JSON\_-\_Get\_file\_details')?['data'][0]['alternateLink']
- fileId: body('Parse\_file\_JSON\_-\_Get\_file\_details')?['data'][0]['graphId']
- siteCollectionId : body('Parse\_file\_JSON\_-\_Get\_file\_details')?['data'][0][‘sitecollectionid’]

In the schema used for the parsing of the JSON, I had to replace several times "type": "string" with "type": ["string", "null"] and remove some properties from the list of the required items of the schema as the information is not always available for the requested documents.
{: #myid .alert .alert-info .p-3 .mx-2 mb-3}

<img src="https://thijoubert.github.io/assets/img/posts/2022-07-29_How-to-empower-users-check-suspicious-activity_7.png" >

Then, I retrieve the user's information.


<br/>
## Focus on step 3 "User contact via Teams with an Adaptive Card
The next step is the key step of the process as we will build the card that will be pushed to the user in Teams.

I wanted to create a card that would allow me to solicit a user to warn him that he created an anonymous link and request him to check if it is not an error. If yes, the end-user has the opportunity to delete the link and notify the security teams.

To build this card, I used the "food order" template available on <https://adaptivecards.io>

<img src="https://thijoubert.github.io/assets/img/posts/2022-07-29_How-to-empower-users-check-suspicious-activity_8.png" >


<br/>
## Focus on step 4 "Processing the user's response”
Once the user has responded to the Adaptive Card, we will process his or her response. 

If the user responded that it was a normal situation, we simply **close the alert** in Defender for Cloud Apps. 

<img src="https://thijoubert.github.io/assets/img/posts/2022-07-29_How-to-empower-users-check-suspicious-activity_9.png" >

If the user has responded that this is an abnormal situation, **several actions are performed**: 

- **Retrieve the file permissions**
- For each share link, I **check if it is an anonymous link, and if so, I delete it**

<img src="https://thijoubert.github.io/assets/img/posts/2022-07-29_How-to-empower-users-check-suspicious-activity_10.png" >

Then, we **close the alert as a true positive** and notify the security teams based on the user's response. 

<img src="https://thijoubert.github.io/assets/img/posts/2022-07-29_How-to-empower-users-check-suspicious-activity_11.png" >



<br/>
# Test: what happens from the user's point of view
To see what it looks like from a user's point of view, I created a document "MCAS - Anonymous Sharing" that I share in 3 different ways: 

- **Anonymous link** (Anyone with the link can edit)
- **Company link** (People in my tenant can edit)
- Specific user

<img src="https://thijoubert.github.io/assets/img/posts/2022-07-29_How-to-empower-users-check-suspicious-activity_12.png" >

My test user receives within 5 minutes (the delay may be longer in a production environment with a huge volume of data) a card inviting him to answer the following question: "Was the creation of an anonymous link a voluntary action?”  

<img src="https://thijoubert.github.io/assets/img/posts/2022-07-29_How-to-empower-users-check-suspicious-activity_13.png" >

If « Yes » is chosen, the user is invited to acknowledge that he understands the risks of such a sharing. 

<img src="https://thijoubert.github.io/assets/img/posts/2022-07-29_How-to-empower-users-check-suspicious-activity_14.png" >

If « No » is chosen, the user is invited to delete the links and eventually notify directly a message to the security teams in case of a suspicious event. 

<img src="https://thijoubert.github.io/assets/img/posts/2022-07-29_How-to-empower-users-check-suspicious-activity_15.png" >

After clicking on "No" > "Remove the link and notify the security teams" > "OK", I can see that my anonymous link is no longer there. 

<img src="https://thijoubert.github.io/assets/img/posts/2022-07-29_How-to-empower-users-check-suspicious-activity_16.png" >

On their side, the security teams received a message informing them that a user reported suspicious activity. 

<img src="https://thijoubert.github.io/assets/img/posts/2022-07-29_How-to-empower-users-check-suspicious-activity_17.png" >



<br/>
# Wrap-up
We saw in this article how the combination of Defender for Cloud Apps (ex-MCAS), Power Automate, Adaptive Card, and Teams made it possible to **simply involve the end-users in the security of the O365 tenant**, by asking them to check whether sensitive actions were carried out consciously or to correct them in case of error.  

The integration of Power Automate and Defender for Cloud Apps is not new (as [Microsoft wrote a first very interesting article in 2020](https://techcommunity.microsoft.com/t5/security-compliance-and-identity/alert-new-blog-series-automation-in-cloud-app-security/ba-p/1608357)), but using **Adaptive Card in Teams is really interesting**. In addition, **Graph API allows going far further than what is possible with the built-in remediation and governance actions**. 

In terms of improvements, I'm thinking first and foremost about the various messages that are sent to users and security teams. If we want to get users on board with the company's strategy, it will be essential to have visual markers that are as consistent as possible with the company's graphic charter.

However, I see several limits: 

- **Static view at the time of creation**, which does not allow you to review the link to see if it is still useful (unless you get into complex Power Automate loops).
- **Need to have some knowledge of Graph / Power Automate**
- **Each use case** will lead to the creation of a File policy and a Power Automate
- No **centralized** view
- **Huge number of alerts**, which can pollute the flow of alerts in Defender for Cloud Apps, and more broadly in Defender and the SIEM

This approach can be adapted to multiple use cases, including enterprise-wide links or certain types of sensitive data. The concept can also be extended with "activity policies" for alerts that do not contain files.
{: #myid .alert .alert-info .p-3 .mx-2 mb-3}