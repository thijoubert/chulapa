---
title: "A first look at the new Conditional Access condition : Filter for applications"
subtitle:
excerpt: "The end of the year 2022 puts Application protection in the spotlight, with the GA of Entra Workload Identity Protection scheduled for November 2022."    
tags:
  - Azure AD
  - Conditional Access
  - Microsoft 365
  - Security
header_img : "./assets/img/posts/2022-11-04_Conditionnal-Access-Filter-Applications_11.png"
---

The end of the year 2022 puts **Application protection in the spotlight**, with the **GA of Entra Workload Identity Protection** scheduled for November 2022 (including conditional access, risky policies, and access reviews). 

Last Wednesday, the **announcement of the Public Preview of a filter for applications** made the news. After the Filter for devices and the dynamic groups for users, it's now the turn of applications to have their own filter in the conditional access policies. 

The principle is simple: it will be possible to **target conditional access policies on applications based on custom security attributes**. This will **avoid having to manually add a new application to policies** and **simplify the reviews**. 



# First step: configure custom security attributes

The implementation of filters for applications will be straightforward for an organization custom security attributes are already used to categorize the enterprise applications.  

For the others, it will be necessary to **set up the tagging strategy for the applications**.

By default, the definition and assignment of custom security attributes are disabled, as it is necessary to have **one of the four roles** below to read or manage them. A Global Administrator will thus have to assign himself/herself an additional role if he wishes to implement the custom attributes. 


|**Role name**|**Description**|
| :- | :- |
|**Attribute assignment administrator**|Assign custom security attribute keys and values to supported Azure AD objects.|
|**Attribute assignment reader**|Read custom security attribute keys and values for supported Azure AD objects.|
|**Attribute definition administrator**|Define and manage the definition of custom security attributes.|
|**Attribute definition reader**|Read the definition of custom security attributes.|

<img src="https://thijoubert.github.io/assets/img/posts/2022-11-04_Conditionnal-Access-Filter-Applications_1.png" >

<img src="https://thijoubert.github.io/assets/img/posts/2022-11-04_Conditionnal-Access-Filter-Applications_2.png" >


Once the role has been assigned, the granted user can now configure the custom security attributes. To do this, you must first create an "attribute set" (i.e. a collection of custom security attributes serving the same purpose). 

Using different sets can be helpful to delegate their visualization, edition, and assignment to different people if you don't want to give directory-wide rights on all the custom security attributes. 
{: #myid .alert .alert-info .p-3 .mx-2 mb-3}

<img src="https://thijoubert.github.io/assets/img/posts/2022-11-04_Conditionnal-Access-Filter-Applications_3.png" >

For each attribute set, you can set a name (which cannot be modified afterward), a description, and a maximum number of attributes. 

<img src="https://thijoubert.github.io/assets/img/posts/2022-11-04_Conditionnal-Access-Filter-Applications_4.png" >


<img src="https://thijoubert.github.io/assets/img/posts/2022-11-04_Conditionnal-Access-Filter-Applications_5.png" >


Once the attribute set is created, you can create a new custom security attribute. Each attribute has: 
- A name
- A description
- A data type: String, Integer, Boolean (Note: only the String type can be used for now for to filter the applications)
- Single or multiple values
- Predefined value 

For this article, I create two sets of attributes to describe two scenarios. Please note that the two described scenarios are totally fictive, and are used for the demonstration as they do not represent a real security posture. 

The first set “AttributeSet1” will be used to tag my service principals with two different attributes: 
- Environment: Production, PreProduction, QA
- Country of the entity: France, US, UK, Russia

<img src="https://thijoubert.github.io/assets/img/posts/2022-11-04_Conditionnal-Access-Filter-Applications_6.png" >

<img src="https://thijoubert.github.io/assets/img/posts/2022-11-04_Conditionnal-Access-Filter-Applications_7.png" >


After the precedent steps, my attribute set contains the two custom security attributes “Country” and “Environment”. 

<img src="https://thijoubert.github.io/assets/img/posts/2022-11-04_Conditionnal-Access-Filter-Applications_8.png" >


I also create a second attribute set to tag my Enterprise Applications to precise if an external user is allowed to access or not. 

<img src="https://thijoubert.github.io/assets/img/posts/2022-11-04_Conditionnal-Access-Filter-Applications_9.png" >



# Second step: assign the attributes to the applications

For the demonstration, I register a new application “SP\_ConditionalAccess” and I assign the two attributes “Country: France” and “Environment: Production”. 

<img src="https://thijoubert.github.io/assets/img/posts/2022-11-04_Conditionnal-Access-Filter-Applications_10.png" >

Note: custom security attributes can be **assigned only to Enterprise Applications**, and not to Registered Applications.



# Third step: configure the conditional access policy

Once the relevant tagging strategy has been defined and implemented, creating a conditional access policy is very easy to target all the tagged applications. 

Filter for applications are available in two scenarios: 
1. To filter the workload identities authenticating to cloud applications;
1. To filter the cloud applications to which the user authenticates.


## Example 1: To filter the workload identities authenticating to cloud applications

Here, I want to limit the use of workload identities tagged as “Environment: Production” and “Country: France” from the France. 

To do this, I create a new Conditional Access policy and I target the relevant applications. 

<img src="https://thijoubert.github.io/assets/img/posts/2022-11-04_Conditionnal-Access-Filter-Applications_11.png" >


And I target all the accesses performed outside of France. 

<img src="https://thijoubert.github.io/assets/img/posts/2022-11-04_Conditionnal-Access-Filter-Applications_12.png" >


I then try to use my application to get an access token from the Graph API with Postman. As Postman servers are located in the US, I get an error message as expected. 

<img src="https://thijoubert.github.io/assets/img/posts/2022-11-04_Conditionnal-Access-Filter-Applications_13.png" >


## Example 2: To filter the cloud applications to which the user authenticates.

In this second example, I want to limit the application a B2B user can access.

Once my B2B population is targeted, I just have to filter the application concerned by my conditional access policy. 

<img src="https://thijoubert.github.io/assets/img/posts/2022-11-04_Conditionnal-Access-Filter-Applications_14.png" >

It set the Grant action to “Block access” and it's done. My B2B guest users will have only access to well-known identified applications. 

Afin d'aller plus loin, je peux également utiliser un groupe dynamique ciblant un domaine en particulier, ou utiliser les nouveaux contrôles granulaires pour les externes.  
{: #myid .alert .alert-info .p-3 .mx-2 mb-3}


# To conclude
*The new filter for applications does not bring new capabilities for Azure AD Conditional Access. It remains very interesting for organizations with a lot of Enterprise Applications targeted by conditional access policies.*

*Two main scenarios can be set up:* 
1. *Filter the workload identities authenticating to cloud applications;*
1. *Filter the cloud applications to which the user authenticates.*

For more information: 
- [Filter for applications in Conditional Access policy (Preview)](https://learn.microsoft.com/en-us/azure/active-directory/conditional-access/concept-filter-for-applications) 
- [Add or deactivate custom security attributes in Azure AD (Preview)](https://learn.microsoft.com/en-us/azure/active-directory/fundamentals/custom-security-attributes-add)

