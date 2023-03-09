---
title: "Why is it important to limit infobesity in Microsoft 365?"
subtitle:
excerpt: "The infobesity induced by the massive adoption of collaborative suites, raises 4 issues for each organization: eco-responsibility, economic, budgetary, performance."    
tags:
  - Eco-responsability
  - Microsoft 365
  - Office 365
  - SharePoint Online
header_img : "./assets/img/posts/2023-03-09_Why-manage-data-volume-Microsoft365_1.png"
---


The massive adoption of collaborative suites has led to an explosion of digital usage and data stored in the cloud over the past several years. This infobesity raises 4 issues for every organization:

1. **An eco-responsibility issue** 
1. **An economic issue** 
1. **A compliance issue** 
1. **A performance issue** 

The purpose of this article is to detail each of these topics and to suggest some ways to limit the volume of data in the applications of the Microsoft collaboration and communication platform. 


# A few reminders, what are we talking about? 
If we still need to remind it today, Microsoft 365 is composed of various services mainly based on Exchange Online or SharePoint Online for data storage. Some rare exceptions have their operating mode. 

In this article, I will **focus on SharePoint Online which is the most important brick for data storage** (which is not quite true for some of my customers who have a very large consumption of OneDrive, but the technical logic is similar). 

Behind SharePoint Online, we are of course talking about content storage for all applications based on the SharePoint brick (like Microsoft Teams or Yammer). 

SharePoint Online allows you to collaborate on content according to several principles: 

- Each session of modification of an item leads to the **creation of a new minor or major version.**
- Each file and each version is **deducted from the site capacity**. 
- The sum of the site capacities allows us to know the **remaining capacity** for the global environment. 
- It is possible to limit the **available quota for each site** to ensure a healthier use of the overall quota. 
- Users are generally **able to create new collaborative spaces and add content** without limitations other than the quota available for the site. 
- To protect data, Microsoft **maintains 3 copies in a primary data center and a secondary data center**. 
- Microsoft has implemented **data shredding** to improve i/o on documents, which also impacts storage (although this is not the primary benefit as far as Office 365 is concerned).

<br />
<br />

# An eco-responsibility topic

The subject that touches me the most, and one of the key subjects for all current IT sustainability programs, is the fight against infobesity. The objective is to **limit the impact on the environment** of the explosion of digital uses in companies. 

Behind the magic of the Cloud, servers need the energy to save data and ensure data protection (redundancy, backup, etc.). These servers also need to be replaced (on average every 6 years according to Microsoft). Reducing the amount of data reduces the energy impact on the one hand and the infrastructure needed on the other.

The first thing that can be said about this is tha**t it is almost impossible to find any technical or scientific literature that reaches a consensus on the subject**. 

I found only two figures that seem to be shared by all:
1. Digital uses today account for **about 4% of the world's electricity consumption** (or as much as global air traffic). This figure could rise to 8% by 2030. 
1. Data centers account for nearly **20% of the global digital share** (around 1% of the global electricity consumption). 

Estimating the global carbon footprint of digital technology **is particularly complex, as it requires taking into account the entire life cycle of the data center**. 

To assess emissions impacts, Microsoft "[segments greenhouse gas emissions into three categories consistent with the Greenhouse Gas Protocol](https://ghgprotocol.org/)”:

- *Scope 1 | Emissions that directly result from business activities, such as stationary combustion of fuels for backup power generation in our data centers.* 
- *Scope 2 | Emissions that indirectly result from producing the energy consumed in data centers, such as exhaust from an electric power plant. The Scope 2 methodology used for the Emissions Impact Dashboard for Microsoft 365 is market-based, meaning that it takes into account Microsoft’s renewable energy power purchases. Carbon offsets are not accounted for here.*
- *Scope 3 | Emissions that indirectly result from the supply chain and all other business activities, such as the embodied carbon associated with manufacturing, shipping, and recycling the servers used in our data centers. Scope 3 figures as reported in the Emissions Impact Dashboard for Microsoft 365 can be thought of as a sunk cost; they represent emissions associated with servers and other IT assets that have already been built and installed in our data centers. Those emissions are amortized over each month of the asset’s estimated lifespan (the EID uses a default value of 6 years) before then being allocated to Azure and Microsoft 365 customers and reported in the EID.* 

While it is quite easy to estimate the impacts of scopes 1 and 2, the impacts of scope 3 are relatively undocumented. 

<br />

## What does Microsoft say about it?

Microsoft has written several very interesting documents detailing an ambition to reduce emissions from its products:

- [The Carbon Benefits of Cloud Computing: A Study on the Microsoft Cloud.](https://download.microsoft.com/download/7/3/9/739BC4AD-A855-436E-961D-9C95EB51DAF9/Microsoft_Cloud_Carbon_Study_2018.pdf)
- [Microsoft 365 Emissions Whitepaper](https://aka.ms/m365-emissions-whitepaper)
- [2021 Environmental Sustainability Report | Microsoft CSR](https://www.microsoft.com/en-us/corporate-responsibility/sustainability/report)

Numerous commitments to the environment are detailed, such as the use of renewable energy, or past achievements such as the optimization of Azure Compute and CPU usage for Teams. 

Microsoft concretely writes 3 things: 
1. For the month of July 2022, Microsoft “estimates that the volume of data center-driven emissions from providing Microsoft 365 services (Exchange Online, SharePoint Online, OneDrive for Business, Microsoft Teams, and Office Online) amounted to approximately **210 grams of carbon** dioxide.equivalent (CO2e) per active user, considering all three scopes of emissions enumerated above.”
1. **Scope 3 generates about 96% of the emissions** on average for collaborative services.
1. **Customers can only act on Scope 3 over the long term** by reducing the amount of data needed, which will limit the capacity needed for the data volumes. 

The **effort of transparency is commendable**, but I find that the **figures announced are rather weak** and lack details about the calculation method. 

In my humble opinion : 
- **210g of CO2e per year seems to me very low for all the services offered by the platform** if we compare it with the emission of one hour of video on Netflix (between "well under 100g" according to Netflix and 400g according to The Shift Project on average - [source](https://www.iea.org/commentaries/the-carbon-footprint-of-streaming-video-fact-checking-the-headlines)).
- **It is not clear if Microsoft's investments in environmental initiatives are taken into account in the calculation of carbon emissions related to collaborative uses** (theoretical compensation does not limit the environmental footprint which is real).
- The dashboard seems to promote the use of the Cloud over on-premises solutions with a local energy mix, while the Redmond firm announces partnerships with energy providers using 100% renewable energy.

For example**, my test tenant which contains a maximum of 1GB of data already represents 198g for one month for each of the 25 users** (see image below)...

<img src="https://thijoubert.github.io/assets/img/posts/2023-03-09_Why-manage-data-volume-Microsoft365_2.png">

<br />

## Let's go a little further

To satisfy my scientific curiosity, I did some research on data storage in the cloud. The numbers are probably different for SharePoint Online but the orders of magnitude should be similar. 

**I must say that I was very surprised to read in the literature CO2e estimations that had nothing to do with each other**. If we consider the storage of 1 GB over 1 year in the Cloud (not necessarily SharePoint) : 

|Source|Value (for 1 GB) |Scope|Methodology (with French energy mix: 80g CO2e per kWh)|
| :- | :- | :- | :- |
|[Stanford](https://medium.com/stanford-magazine/carbon-and-the-cloud-d6f481b79dfe) (2017)|**250** g CO2e per year|Storage only|Estimation of 3 kWh |
|[Shift Project](https://view.officeapps.live.com/op/view.aspx?src=https%3A%2F%2Ftheshiftproject.org%2Fwp-content%2Fuploads%2F2018%2F10%2FLean-ICT-Materials-QuantiLev-2018.xlsx&wdOrigin=BROWSELINK) (2020)|**80**  g CO2e per year|Storage only|Estimation of 1 kWh with French “Caisse des Dépots”|
|[Green Cloud Computing](https://myimpact.isit-europe.org/fr/) (2021)|**35g** CO2e per year|Storage and datacenter|0\.65 W per hour per TB, 6x replication|
|[Negaoctet](https://base-impacts.ademe.fr/gestdoclist) (2022)|**12g** CO2e per year|Storage and datacenter|From a Netflix storage analysis|
|[ADEME](https://impactco2.fr/usagenumerique/stockagedonnee) (?)|**0.24** gCO2 per year|Storage and datacenter|Source from Negaoctet ?|

Note: the data correspond to the French energy mix which is particularly low in carbon (e.g. United States x6).
{: #myid .alert .alert-info .p-3 .mx-2 mb-3}



To be exhaustive, it would be necessary to take into account the **replication of data** (potentially x6 on the result), **the energy necessary for the transfer to SharePoint Online** (minimal according to the various studies), as well as the **processing of data** (knowing that some of my customers have evaluated at less than 10% the volume of data used). 

Also, it should be kept in mind that Scopes 1 and 2 (storage, processing) represent **only 4% of the total energy consumption, according Microsoft (and do not include the construction and life cycle of data centers)**.

In addition to the aberrant discrepancy between the different figures (unless I am mistaken, we arrive at discrepancies between 1 and 2000), it is very complicated to compare the different studies because of the **lack of communication on the hypotheses** (energy mix used to power the servers, level of data redundancy, minimum capacity reserved for an organization, size, and technology of the hard drives, etc.). 

It is also important to keep in mind that technological advances are improving the energy efficiency of data centers and applications every year. For this reason, it is also difficult to compare studies when the figures are several years apart.
{: #myid .alert .alert-info .p-3 .mx-2 mb-3}

When we know that the largest part of the environmental footprint of digital usage is carried by the life cycle of terminals and servers, it is not the deletion of an email from one's inbox or a document from one's OneDrive that will save the planet… 

But, **each small gesture counts, and sobriety must be a value for each company or organization**. In the long run, and Microsoft makes it clear in its reports, **limiting the volume of data will limit the increase in data center capacity**. 

One point is still very obscure as far as I am concerned. I haven't found any official confirmation if each version of SharePoint Online was stored in full size or if only the shreds were kept. This would not have the same impact when cleaning up the versions of a document from an ecological point of view. 


<br />
<br />

# A compliance topic

The compliance subject is usually the second reason why it is necessary to manage the data lifecycle. Whether it's for privacy (with RGPD, CCPA) or financial regulations, IT, workplace, and compliance teams are in charge of ensuring that data is kept for the right amount of time: 
- Not too short for security reasons in case of an internal investigation.
- Not too short for certain contracts.
- Not too long to ensure that the company cannot be prosecuted for acts prescribed by the regulations.
- Not too long to avoid keeping personal data without justification.


<br />
<br />

# A budgetary topic

After 7 years after the launch of SharePoint Online, and thus the migration of data to the cloud, the storage capacity of SharePoint Online is beginning to saturate. And this, despite the increase in user quotas for SharePoint Online in 2018 (from 0.5GB to 10GB per user).

As a reminder, the [global storage capacity is 1 TB plus 10 GB per licensed user.](https://learn.microsoft.com/en-us/microsoft-365/commerce/add-storage-space?view=o365-worldwide) 

To overcome this storage limit, many companies I met logically started by buying additional storage. The public price is 0.17€ / GB per month, so the bill can be heavy for some organizations (2 040 € / TB per year). 

When we see the default capacities allocated to companies, **it is obvious that the workplace teams will have more and more pressure to control the volume of data**: 

|Number of users|Default storage capacity|
| :- | :- |
|100|2 TB|
|1 000|11 TB|
|10 000|101 TB|
|100 000|1001 TB (~1 PB)|

Reminder: Microsoft counts ALL versions in the storage capacity used. On the back end, shredding storage should be activated to store only the delta between the versions. But on the front end, the [UI displays the full size for each version](https://support.microsoft.com/en-us/kb/3038333). The issue is that the full size is counted in the site quota… and in the global SharePoint capacity used.
{: #myid .alert .alert-info .p-3 .mx-2 mb-3}

An example of one site created for the occasion with only one document and several versions: 

<img src="https://thijoubert.github.io/assets/img/posts/2023-03-09_Why-manage-data-volume-Microsoft365_3.png">

In the Storage metrics, I see that the versions are all counted in the site consumption. 

<img src="https://thijoubert.github.io/assets/img/posts/2023-03-09_Why-manage-data-volume-Microsoft365_4.png">

The same information can be found in PowerShell : 

<img src="https://thijoubert.github.io/assets/img/posts/2023-03-09_Why-manage-data-volume-Microsoft365_5.png">

Reminders for the storage capacity:
- The contents in the Recycle Bin are not counted in the site storage. 
- The contents in the Preservation Hold Library could 
{: #myid .alert .alert-info .p-3 .mx-2 mb-3}

For your information, some of my customers have several Petabytes of data in SharePoint Online. Surely significant savings can be made in this area. 

|Extra storage|Public pricing|
| :- | :- |
|1 GB|2\.04 € per year|
|1 TB|2 040 € per year|
|1 PB|2 040 000 € per year|

Companies that need several TB of data, not to mention PB, should be able to negotiate much better discounts than the public price. 


<br />
<br />

# A performance topic

This last topic may seem obvious when you put it that way, but it's rarely addressed by large companies. Infobesity has a direct impact on the use of the platform. **Users can struggle to find the right information**. The explosion of data volume makes it difficult to search, despite the use of metadata and the power of search tools like SharePoint or Delve with Graph API. 

Intelligent search tools like Delve and artificial intelligence tools (which will be integrated into Viva Topics) will help **refine search results**. But finding the right information, especially for R&D departments and consulting firms, remains the key to success. 

**The performance issue impacts directly security. The more data there is, the harder it will be to detect weak security signals.** 


<br />
<br />

# Some tips for reducing your digital footprint in Office 365

Different actions can be carried out either **by users or by administrators**, provided that the **mechanisms inherent to the platform are understood**: 

Some of the actions that can be taken by end-users include:

- Deleting unused (unmodified or unaccessed) directories and files
- Deleting versions of unused (unmodified or unaccessed) files
- Deleting or archiving unused collaboration spaces
- Deleting duplicates
- Remembering to empty the various recycle bins

On the other hand, administrators can take the following actions:

- Implementing retention policies 
- Setting up a quota policy in accordance with the uses of the employees (e.g.: the marketing department will not have the same needs as the finance department)
- Controlling the versions available for all document libraries
- Equipping users to identify obsolete data
- Raising user awareness

**To guarantee the company's information assets, it is essential to ensure that the actions implemented will not pose operational or security risks (e.g. data corruption, compliance, wiper, user error, etc.).**

**Users are the owners of their data, and IT teams cannot delete it without their permission.** 

<br />
<br />

# In conclusion
One of the most critical aspects of Microsoft 365  is data volume control, which is essential for optimizing costs, ensuring compliance compliance, ensuring system performance, and preserving the environment.

Effective Office 365 data management policies and practices are needed to address these challenges, and organizations must make it a priority to implement them. With the right policies in place, companies can reduce their data volume, control costs, ensure regulatory compliance, and enhance system performance.

On the subject of eco-responsibility, it remains complicated today to estimate the exact impacts of the consumption of Cloud services. Even without that, sobriety must be a compass. In the long run, and Microsoft makes it clear in its reports, limiting the volume of data will limit the increase in data center capacity. 

To this end, I will continue to write articles that provide concrete tips to limit infobesity. By implementing these tips, organizations can successfully manage their data on Office 365 and overcome the challenges associated with data volume control.