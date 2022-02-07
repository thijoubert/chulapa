---
title: "How to keep track of Office 365 news? An example with Power Automate (1/2)"
subtitle:
excerpt: "Following Office 365 evolutions can be very tricky. I present here how I organized my watch: Microsoft blogs and Message Center."  
tags:
  - Microsoft 365
  - Monitoring
  - Office 365
  - Power Automate
  - Power Platform
  - Watch
header_img : "./assets/img/posts/2022-01-21_How-to-watch-Office-365_2.png"
---



A new year means new resolutions. A bit cliché, but the end of the year 2021 was the occasion for me to focus on a recurring question: "how to improve technical watch ". 

In my former life as a consultant, I met a lot of organizations, during my audits or assignments, that were facing difficulties to set up an efficient watch to (try to) follow the numerous evolutions related to Office 365. 

With the core services of Office 365 (SharePoint Online, Exchange Online, etc.), the Power Platform, Azure Active Directory, security services... there are so many things that move every month or every day that it is impossible to keep up with everything. Regardless of the size of the organization (150 users or +150k people), it is still necessary to keep up to date with the evolutions that could affect the **user experience** or the **security of the platform**, not to mention the **new benefits brought**. 

Among the official Microsoft sources (described in the article "[Staying on top of Office 365 updates](https://techcommunity.microsoft.com/t5/core-infrastructure-and-security/staying-on-top-of-office-365-updates/ba-p/1201118)"), we find 

- [**Microsoft 365 Roadmap**](https://techcommunity.microsoft.com/t5/core-infrastructure-and-security/staying-on-top-of-office-365-updates/ba-p/1201118#Microsoft365Roadmap): Official site of the Microsoft 365 roadmap with the future evolutions and their status (private preview, public preview, rolling out, launched...) 
- [**Office 365 Messages Center**](https://techcommunity.microsoft.com/t5/core-infrastructure-and-security/staying-on-top-of-office-365-updates/ba-p/1201118#Office365MessageCenter): Messages center with key information for administrators ("Stay Informed” or "Plan For Action"). It is possible to receive a [weekly digest by email](https://techcommunity.microsoft.com/t5/core-infrastructure-and-security/staying-on-top-of-office-365-updates/ba-p/1201118#Office365MessageCenter), to [export them via APIs](https://techcommunity.microsoft.com/t5/core-infrastructure-and-security/staying-on-top-of-office-365-updates/ba-p/1201118#Office365ServiceCommunicationsAPI), or to [synchronize the messages with a Planner](https://techcommunity.microsoft.com/t5/core-infrastructure-and-security/staying-on-top-of-office-365-updates/ba-p/1201118#Office365MessageCenterPlannerSyncing). 
- [**Monthly Technical Update Briefing / Evergreen Programm**](https://techcommunity.microsoft.com/t5/core-infrastructure-and-security/staying-on-top-of-office-365-updates/ba-p/1201118#MonthlyTechnicalUpdateBriefing): Summary of new features for customers with a Premier / Unified Supports contract
- [**Microsoft Tech Community Blogs**](https://techcommunity.microsoft.com/t5/core-infrastructure-and-security/staying-on-top-of-office-365-updates/ba-p/1201118#Office365Blog): Blogs on the different workoads of the platform 
- [**Microsoft 365 Youtube Channel**](https://www.youtube.com/c/microsoft365/featured): Official Youtube channel with recordings of the main conferences or the monthly summary "What's new in Microsoft 365" (which unfortunately seems to be stopped). 

To this, we must add the **many unofficial sources**: blogs or Youtube channels of MVPs or various Communities (eg PNP or aMS), community blogs (eg Office 365 for IT Pros, Regarding 365), Twitter, Linkedin ... 

**That's a lot of pages to follow... and often, even the best will in the world is not enough to stay motivated.**



# What I wanted to do

Since I don't have the time to follow all the evolutions, I looked for a solution allowing me to centralize all the most important information in one place. My goal was to be able to limit my monitoring efforts to **15-30 min per day** (apart from attending physical or virtual events). 

There are several specialized solutions (e.g. Feedly or Inoreader) allowing to aggregate news feeds, Youtube channels, RSS feeds... but none of them met my expectations: 

- **Integrated in my work tools**
- **Fully customizable** 
- **Aggregation of Microsoft proprietary sources (e.g. Message Center or Roadmap)**
- **Free**
- **Reproducible by others**



# What I did

The natural solution was to use a combination of Microsoft Teams and Power Automate. I thus created a Team with channels according to the sources and reading priorities: 

1. **Microsoft - Blogs**: My favorite official TechCommunity blogs on Collaboration, Power Platform, Security, Compliance, and Identity, as well as a selection of official Youtube channels
1. **Microsoft - Messages Center**: Elements of the Messages Center
1. **Microsoft - Roadmap**: What's new in the Microsoft 365 Roadmap
1. **MVPBuzz - Blogs**: A selection of the many MVP blogs
1. **Solutions - Blogs**: A selection of blogs from solution providers around Office 365
1. **Twitter**: A selection of well-chosen Twitter accounts to see what I might have missed on the above sources

As far as I know, Linkedin does not yet offer connectors to retrieve Pulse articles or posts automatically. This will certainly come one day. 

The final solution in Teams look like that
<img src="https://thijoubert.github.io/assets/img/posts/2022-01-21_How-to-watch-Office-365_1.png" >

An on my phone: 
<img src="https://thijoubert.github.io/assets/img/posts/2022-01-21_How-to-watch-Office-365_2.png" >



To keep track of these news, **I also store them in the SharePoint site associated with my Teams**, but I'll come back to that below. 

To build this Office 365 watch center, I relied on **several Power Automate flows.** These flows run at midnight every day. Every morning, I can see at a glance the main news or blog appeared the day before!

I could also decide to run my feeds every hour or every minute, but I wanted to **limit the notifications**. 


<br/>

# Let’s deep dive 

Each flow is based on a **combination of RSS, HTTP, Teams, SharePoint connectors**. It's really not rocket science, but really efficient!

These flows were created in my Developer environment by habit, but you can also associate them directly to a Team (via Dataverse for Teams) or in another environment.  

<br/>

## Microsoft – Blogs

To follow the Microsoft blogs, I created a dedicated flow "Watch - Microsoft - Blogs" to retrieve new articles every day based on the RSS feeds: 

**Step 1:** Triggering the flow every day at midnight.

**Step 2:** Initialization of an array variable "vRSS" to store the different articles.

**Step 3:** Initialization of a string variable "vSource" to store the name of the blog of an article.

**Step 4:** Initialization of a string variable "vTitle" to store the title of an article.

**Step 5:** Initialization of a string variable " vPublicationDate " to store the publication date of an article.

**Step 6:** Initialization of string variable "vUpdateDate" to store the date of update of an article.

**Step 7:** Initialization of a string variable "vDates" to store the dates that I will display in Teams.

**Step 8:** Initialization of a string variable "vURL" to store the URL of an article.

**Step 9:** Initialization of a string variable "vSummary" to store the summary of an article (depending on the RSS feed, it can be a summary or the whole content).

<img src="https://thijoubert.github.io/assets/img/posts/2022-01-21_How-to-watch-Office-365_3.png" >

**Step 10:** For the [Office 365 blog](https://techcommunity.microsoft.com/t5/office-365-blog/bg-p/Office365Blog), I get the RSS feeds of the previous day : 
 ```
 addDays(utcnow(),-1)
 ```

**Step 11:** For each item of the feed, I get the title, the URL, the publication date, the update date, the summary; and I add a line in my vRSS array variable. 

<img src="https://thijoubert.github.io/assets/img/posts/2022-01-21_How-to-watch-Office-365_4.png" >


**Step 12:** I repeat the same actions for each of the blogs that interest me: "Security and Compliance blog", "Microsoft 365 blog", "Azure AD blog", "Microsoft Teams blog", "Microsoft Teams Community blog", "Power Automate blog", "and Power BI blog".

<img src="https://thijoubert.github.io/assets/img/posts/2022-01-21_How-to-watch-Office-365_5.png" >

A little tip here: instead of manually redoing each action, I can directly copy a sample action (see the two images below).

<img src="https://thijoubert.github.io/assets/img/posts/2022-01-21_How-to-watch-Office-365_6.png" >

<img src="https://thijoubert.github.io/assets/img/posts/2022-01-21_How-to-watch-Office-365_7.png" >

**Step 13:** I get an @mention to notify my account. 

Note that a user does not receive a notification if he notifies himself in Teams. So I use a dedicated account for my watch (Palantir).
{: #myid .alert .alert-info .p-3 .mx-2 mb-3}

**Step 14:** Then, I go through the “vRSS” variable to process each of the messages.

**Step 15:** For a given element, I start by filling my variables with the elements of my line (e.g.: "item()?['source']" for "vSource").

<img src="https://thijoubert.github.io/assets/img/posts/2022-01-21_How-to-watch-Office-365_8.png" >

**Step 16:** To simplify the display of the dates, add a step with a condition to look if the update date is empty or equal to the publication date.

<img src="https://thijoubert.github.io/assets/img/posts/2022-01-21_How-to-watch-Office-365_9.png" >

**Step 17:** Sharing of the article in the channel "Microsoft – Blogs" of the Teams "MG – Technical Watch" and here we go:

<img src="https://thijoubert.github.io/assets/img/posts/2022-01-21_How-to-watch-Office-365_10.png" >

**Step 18:** Sharing of the article in a list of the site "MG – Technical Watch" for a future usage.

<img src="https://thijoubert.github.io/assets/img/posts/2022-01-21_How-to-watch-Office-365_11.png" >

Another little tip: I activated "Use enhanced rich text" for the summary column which is in HTML by default.  
{: #myid .alert .alert-info .p-3 .mx-2 mb-3}

<img src="https://thijoubert.github.io/assets/img/posts/2022-01-21_How-to-watch-Office-365_12.png" >

<img src="https://thijoubert.github.io/assets/img/posts/2022-01-21_How-to-watch-Office-365_13.png" >

**So, from now on, I can follow every day the new articles of the Microsoft blogs and keep a history of them in my SharePoint list. I can also add comments or a tag indicating if the article is read or not.** 

You can find the flow source on my [Github](https://github.com/thijoubert/Sharing/tree/main/Power%20Platform). 



<br/>

## Microsoft – Messages Center
To keep track of the Messages Center information, I regularly export it via the Graph API. 

If you want to know more about this export feature, Tony Redmond wrote an [interesting article last July about the new queries available in the Graph API](https://practical365.com/moving-office365-service-communications-api-graph/). 

**Step 1:** Triggering the feed every day at midnight.

**Step 2:** Initialization of an array variable "vArrayMessageTagsName" to store the tags of a message (ex: "User Impact", "Admin Impact")

**Step 3:** Initialization of a string variable " vCategory " to store the category of a message.

**Step 4:** Initialization of a string variable " vTitle " to store the title of an article.

**Step 5:** Initialization of authentication variables ("TenantID", "ClientID", "ClientSecret").

**Step 6:** Definition of the URI of the graph request: 
 ```
https://graph.microsoft.com/beta/admin/serviceAnnouncement/messages?$filter=lastModifiedDateTime ge @{addDays(utcnow(),-1)}
 ```

<img src="https://thijoubert.github.io/assets/img/posts/2022-01-21_How-to-watch-Office-365_14.png" >

**Step 7:** Get all the messages of the past day

<img src="https://thijoubert.github.io/assets/img/posts/2022-01-21_How-to-watch-Office-365_15.png" >

**Step 8:** Parsing the messages with the following body: 

 ```json
 {
    "type": "object",
    "properties": {
        "@@odata.context": {
            "type": "string"
        },
        "value": {
            "type": "array",
            "items": {
                "type": "object",
                "properties": {
                    "startDateTime": {
                        "type": "string"
                    },
                    "endDateTime": {
                        "type": "string"
                    },
                    "lastModifiedDateTime": {
                        "type": "string"
                    },
                    "title": {
                        "type": "string"
                    },
                    "id": {
                        "type": "string"
                    },
                    "category": {
                        "type": "string"
                    },
                    "severity": {
                        "type": "string"
                    },
                    "tags": {
                        "type": "array",
                        "items": {
                            "type": "string"
                        }
                    },
                    "isMajorChange": {
                        "type": "boolean"
                    },
                    "actionRequiredByDateTime": {},
                    "services": {
                        "type": "array",
                        "items": {
                            "type": "string"
                        }
                    },
                    "expiryDateTime": {},
                    "hasAttachments": {
                        "type": "boolean"
                    },
                    "viewPoint": {},
                    "details": {
                        "type": "array",
                        "items": {
                            "type": "object",
                            "properties": {
                                "name": {
                                    "type": "string"
                                },
                                "value": {
                                    "type": "string"
                                }
                            },
                            "required": [
                                "name",
                                "value"
                            ]
                        }
                    },
                    "body": {
                        "type": "object",
                        "properties": {
                            "contentType": {
                                "type": "string"
                            },
                            "content": {
                                "type": "string"
                            }
                        }
                    }
                },
                "required": [
                    "startDateTime",
                    "endDateTime",
                    "lastModifiedDateTime",
                    "title",
                    "id",
                    "category",
                    "severity",
                    "tags",
                    "isMajorChange",
                    "actionRequiredByDateTime",
                    "services",
                    "expiryDateTime",
                    "hasAttachments",
                    "viewPoint",
                    "details",
                    "body"
                ]
            }
        }
    }
 }
 ```

For each message, update the defined variables (step 9 to 17):

**Step 9:** Reset of the variable "vArrayMessagesTagNames”.

**Step 10:** Join the messages tags of the table "tags" with ",".

**Step 11:** Split the messages tags names with "split(body('MessageTagNames\_-\_Join'),',')".

**Step 12:** Create a JSON with the tags (for SharePoint).

<img src="https://thijoubert.github.io/assets/img/posts/2022-01-21_How-to-watch-Office-365_16.png" >

**Step 13:** Re-write the categories of the messages (“Plan For Change” or “Stay Informed”).

<img src="https://thijoubert.github.io/assets/img/posts/2022-01-21_How-to-watch-Office-365_17.png" >

**Step 14:** Join the affected workload display names (for SharePoint).

**Step 15:** Set the value of “vURL”.

**Step 16:** Create an hyperlink (for Teams and SharePoint messages) : “concat('<a href="https://admin.microsoft.com/#/MessageCenter/:/messages/',outputs('Change\_Id\_-\_Get\_Value'),'"> See in Message Center</a>')”.

<img src="https://thijoubert.github.io/assets/img/posts/2022-01-21_How-to-watch-Office-365_18.png" >

**Step 17:** I get an @mention to notify my account (by my monitoring account “Palantir”).

**Step 18:** Sharing of the article in the channel "Microsoft – Messages Center" of the Teams "MG – Technical Watch" and here we go:

<img src="https://thijoubert.github.io/assets/img/posts/2022-01-21_How-to-watch-Office-365_19.png" >

**Step 19:** Sharing of the article in a list of the site "MG – Technical Watch" for a future usage.

<img src="https://thijoubert.github.io/assets/img/posts/2022-01-21_How-to-watch-Office-365_20.png" >

And hop:

<img src="https://thijoubert.github.io/assets/img/posts/2022-01-21_How-to-watch-Office-365_21.png" >

While I am it, I can also display my SharePoint list within my Teams: 

<img src="https://thijoubert.github.io/assets/img/posts/2022-01-21_How-to-watch-Office-365_22.png" >

**I can then follow every day the new Messages in the admin Center and keep a history of them in my SharePoint list. The synchronization with the Planner could be useful for an admin team, but I don’t see a concrete usage as far as I am concerned.**

You can find the flow source on my [Github](https://github.com/thijoubert/Sharing/tree/main/Power%20Platform). 


<br/>
*In a next article, I will cover how to follow the Microsoft 365 roadmap, other blogs, and Twitter.*
*If you have any comments or improvements idea, I would be very pleased to discuss about it.*