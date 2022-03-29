---
title: "Identify which users accessed to external Office 365 tenants?"
subtitle:
excerpt: "With new Cross-tenant access capabilities, it is now possible to identify wich external tenants are accessed by your users."    
tags:
  - Microsoft 365
  - Monitoring
  - Office 365
  - Azure AD
  - Cross-tenant access
header_img : "./assets/img/posts/2022-03-29_Identify-who-accessed-to-external-tenants_2.png"
---


What company hasn't wondered which Office 365 tenants its users are accessing? I can think of two cases in particular: 

- Phishing: a group of users were phished and invited into a tenant with a similar name (e.g. MICR0SOFT instead of MICROSOFT). 
- Tenant restrictions: Compliance teams had requested that the external tenants that employees could access be controlled. Before implementing any hardening measures, it was essential to understand usage. 

Little reminder about the Office profile

Each user has in his Office profile, [a page regrouping his external organizations](https://myaccount.microsoft.com/organizations). 

<img src="https://thijoubert.github.io/assets/img/posts/2022-03-29_Identify-who-accessed-to-external-tenants_1.png" >

Unfortunately, to my knowledge, there is no way for an administrator to retrieve this information (without using a user's credentials ...).


<br/>
# A first answer with Azure AD Sign In Log
With the advent of cross-tenant access and new policy for collaboration with external tenants (via Inbound and Outbound settings), Microsoft has brought this issue back into the spotlight. 

In the Azure AD Sign In Logs, more information has recently appeared: 

- Resource Tenant ID: Guid of the tenant of the accessed resource
- Home tenant ID : Guid of the tenant to which the user is attached and used to validate his identity
- Cross tenant access type: B2B Collaboration or B2B Direct




<img src="https://thijoubert.github.io/assets/img/posts/2022-03-29_Identify-who-accessed-to-external-tenants_2.png" >

In the [Cross-tenant access overview page](https://docs.microsoft.com/en-us/azure/active-directory/external-identities/cross-tenant-access-overview#sign-in-logs-powershell-script), Microsoft provides a script to query user connections on an external tenant.

```powershell
#Initial connection
Connect-MgGraph -Scopes "AuditLog.Read.All"
Select-MgProfile -Name "beta"

#Get external access
$TenantId = "<replace-with-your-tenant-ID>"
Get-MgAuditLogSignIn -Filter "ResourceTenantId ne '$TenantID'" -All:$True |
Group-Object ResourceTenantId,AppDisplayName,UserPrincipalName |
Select-Object count,@{n='Ext TenantID/App User Pair';e={$\_.name}}
```

On my test tenant, this gives me : 

<img src="https://thijoubert.github.io/assets/img/posts/2022-03-29_Identify-who-accessed-to-external-tenants_3.png" >

Except that I am well advanced with the External Tenant Guid... Knowing who accesses which application is great! But it would be very useful to know the tenant behind it.


<br/>
# A new API available
While playing with the Cross-tenant access features, one window in particular interested me: the one allowing to select a third-party tenant ([Azure AD > External Identities > Cross-tenant access > Add Organization](https://portal.azure.com/#blade/Microsoft_AAD_IAM/CompanyRelationshipsMenuBlade/CrossTenantAccessSettings)). With the name of one of my test tenants, I have the following result: 

<img src="https://thijoubert.github.io/assets/img/posts/2022-03-29_Identify-who-accessed-to-external-tenants_4.png" >

Behind this result is the following query:

```
method: "GET"
url: "/tenantRelationships/findTenantInformationByDomainName(domainName='dwpsecurity.onmicrosoft.com')"
```

If I search for the tenant ID, I get the following query: 

```
method: "GET"
url: "/tenantRelationships/findTenantInformationByTenantId(tenantId='5eb0364a-1e47-4d89-8af8-ef88abfa199c')"
```

<img src="https://thijoubert.github.io/assets/img/posts/2022-03-29_Identify-who-accessed-to-external-tenants_5.png" >

This is already more than interesting. 

After several tests on Graph Explorer, I saw that the permission needed to call this API was the delegated permission "Directory.AccessAsUser". Unfortunately, I couldn't find an equivalent application permission for the API request but it's not bad.

A Proof of Concept to list the external stakeholders

The next step was to associate this request with the Microsoft mini script: for each sign-in in an external environment, I retrieve the external information. 

For this, I had to register an Azure AD application with the delegation "Directory.AccessAsUser". 

```powershell

Connect-MgGraph -Scopes "AuditLog.Read.All"
Select-MgProfile -Name "beta"

#Get external accesses

$TenantId = "Tenant ID of My tenant"
$AuditLogs = Get-MgAuditLogSignIn -Filter "ResourceTenantId ne '$TenantID'" -All:$True 

############################
# Prepare tenant info request 

$tenantName = "myTenant.onmicrosoft.com"
$clientID = "xxx"
$ClientSecret = "xxx"
$Username = "xxx"
$Password = "xxx"

$ReqTokenBody = @{
   Grant_Type    = "Password"
   client_Id     = $clientID
   Client_Secret = $clientSecret
   Username      = $Username
   Password      = $Password
   Scope         = "https://graph.microsoft.com/.default"
} 

$TokenResponse = Invoke-RestMethod -Uri "https://login.microsoftonline.com/$TenantName/oauth2/v2.0/token" -Method POST -Body $ReqTokenBody

$access_token = $TokenResponse.access_token

########################
# Request external information

$AuditLogs | Add-Member -MemberType NoteProperty "TenantName" -Value "" 

$i = 0

foreach($AuditLog in $AuditLogs){
   $tenantId = $AuditLog.ResourceTenantId
   $apiUrl = "https://graph.microsoft.com/beta/tenantRelationships/findTenantInformationByTenantId(tenantId='$tenantId')"
   $Data = Invoke-RestMethod -Headers @{Authorization = "Bearer $access_token"} -Uri $apiUrl -Method Get
   $AuditLogs[$i].TenantName = $Data.DisplayName
   $i++
}

$AuditLogs | Group-Object ResourceTenantId, TenantName, AppDisplayName, UserPrincipalName | Select-Object count,@{n='Ext TenantID/App User Pair';e={$\_.name}}

$AuditLogs | ft ResourceTenantId, TenantName, AppDisplayName, UserPrincipalName 

```

I get the following results: 

<img src="https://thijoubert.github.io/assets/img/posts/2022-03-29_Identify-who-accessed-to-external-tenants_6.png">

This fulfills my initial need to know my users' connections to external environments. 

A few things to keep in mind: 
- The */tenantRelationships/findTenantInformationByTenantId(tenantId='$tenantId')* API is only in beta today. Be careful not to use it in production. 
- The available information depends on the retention time of the Azure AD logs and therefore on the Azure AD licenses of the concerned users (7 or 30 days). 
- My script is only a POC and should be strongly optimized before being deployed in an environment of several thousand people. Occasionally, I'll create a Power BI dashboard to more easily view this information and retain it beyond native retention. 