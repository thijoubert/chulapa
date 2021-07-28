---
title: "De testeur du dimanche aux projets d'ampleur : quels environnements de test pour Microsoft 365 ?"
subtitle:
excerpt: Microsoft offre plusieurs possibilités pour tester ses différentes fonctionnalités. Durées de vie, licences disponibles... petit tour d'horizon sur les avantages et inconvénients.  
tags:
  - Microsoft 365
  - Power Platform
  - Licensing
  - Test
header_img : "./assets/img/posts/2021-07-22_TestEnvironment_0.jpg"

---


Une frustration importante que je rencontre chez un grand nombre de personnes (consultants, équipes sécurité, *workplace* ou *change management…*) est l’incapacité à tester les fonctionnalités des services Office 365.

Parmi les points que l’on m’a remontés : 

- Je veux tester des fonctionnalités pour mes utilisateurs, mais les méchants de la sécurité ont bloqué ces services ;
- Je veux faire de la veille et anticiper les actions de configuration nécessaires ;
- Je veux préparer une certification (MS-900, MS-500, etc.).

Il y a déjà beaucoup de documentation en anglais sur comment créer un environnement de test. Je souhaite vous donner ici des billes afin d’appréhender ces environnements au mieux. 



<br/>
# Tenant développeur, *Trial tenant*, Tenant de pré-production : de quoi parle-t-on ?

<br/>
## Tenant développeur : késako ? 
Microsoft a remis en valeur fin 2020 – début 2021 les tenant développeurs. Ces environnements s’adressent à tous les personnes qui souhaitent tester des fonctionnalités cloud de Microsoft 365, et développer des applications (Graph API, Azure AD, Teams, SharePoint Framework, Power Platform, etc.)

Actuellement, l’ouverture d’un tenant développeur permet de bénéficier d’un plan de licences « Microsoft 365 E5 Developer (without Windows and Audio Conferencing) » pour 25 utilisateurs. Ce plan est similaire à Microsoft 365 E5, sans les fonctionnalités suivantes : 

- Windows 10 et les fonctionnalités associées : Microsoft Defender for Endpoint, Microsoft Endpoint DLP, etc.
- Information Barriers
- My Analytics
- Audio Conferencing
- Premium Encryption in Office 365
- Kaizala

Si utilisé régulièrement, un tenant développeur peut être conservé indéfiniment. Tous les 90 jours, Microsoft définit si le tenant est éligible au renouvellement automatique, via la télémétrie. J’ai mon tenant depuis 6 mois pour des tests, et il a été renouvelé automatiquement sans aucun problème. 

Ouvrir un tenant développeur est très simple et c’est gratuit ! N’importe qui peut en obtenir un en allant sur la [page du programme Développeur](https://developer.microsoft.com/en-us/microsoft-365/dev-program). 

<img src="https://thijoubert.github.io/assets/img/posts/2021-07-22_TestEnvironment_1.png" >

En cliquant sur « Join Now », il nous est demandé de préciser notre région et le nom de notre organisation.  

<img src="https://thijoubert.github.io/assets/img/posts/2021-07-22_TestEnvironment_2.png" >

En cliquant sur « **Set up E5 subscription** », il vous sera demandé de renseigner le nom de votre tenant (ex : **mydevtenant**.onmicrosoft.com) et les **authentifiants de votre futur compte Global admin**. 

<img src="https://thijoubert.github.io/assets/img/posts/2021-07-22_TestEnvironment_3.png" >

**Bravo** ! Vous avez réussi à créer votre environnement développeur. Vous aurez ainsi 25 licences « Microsoft 365 E5 Developer (without Windows and Audio Conferencing) » pour faire vos tests. 

Pour aller encore plus loin, vous aurez également la **possibilité de [rajouter des données de tests**](https://docs.microsoft.com/en-us/office/developer-program/install-sample-packs)** (16 utilisateurs, mails, sites sharepoint, données, signaux).

<img src="https://thijoubert.github.io/assets/img/posts/2021-07-22_TestEnvironment_4.png" >

Il est également possible de souscrire à des licences d’essai dans un environnement développeur (cf. ci-dessous). 


<br/>
## *Trial tenant* : késako ? 

Un tenant de test (ou *trial tenant*) est un environnement avec des licences Office 365 gratuites pendant 30 jours. 

Pour obtenir un tenant de test, c’est également tout simple et gratuit. Il suffit d’aller sur la [page de comparaison des plans Office 365](https://www.microsoft.com/en-us/microsoft-365/enterprise/compare-office-365-plans) et de sélectionner « Try for free » pour une licence E3 ou E5. 

<img src="https://thijoubert.github.io/assets/img/posts/2021-07-22_TestEnvironment_5.png" >

Après avoir complété les informations nécessaires, vous **bénéficierez de 25 licences E3 ou E5**, en fonction de l’option choisie. Ces licences seront valides pendant **30 jours**. 

En parallèle, il vous sera également **possible de souscrire à d’autres licences d’essai**, afin de tester des **fonctionnalités complémentaires** dans le centre d’administration ([Microsoft 365 admin center > Billing > Purchase services](https://admin.microsoft.com/Adminportal/Home#/catalog)). 

<img src="https://thijoubert.github.io/assets/img/posts/2021-07-22_TestEnvironment_6.png" >

A l’issue de ces 30 jours, vous aurez le choix entre supprimer le tenant, souscrire à un autre plan de licences ou conserver le tenant mais rien en faire. 


<br/>
## Tenant de préproduction : késako ? 

Un tenant de pré-production est, comme son nom l’indique, un environnement similaire à la pro-production dans lequel seront testées les nouvelles applications ou fonctionnalités, avant déploiement. 

Il peut s’agir d’un tenant trial converti en tenant payant. 


<br/>
## Tenant de démonstration : késako ? 

Les tenants de démonstration font partie du [CDX (Customer Digital expérience)](https://cdx.transform.microsoft.com/), une plateforme accessible aux **employés de Microsoft, aux employés d’un Partenaire de Microsoft ou à un MVP**. Il s’agit de tenants destinés, comme le nom l’indique, à **faire des démonstrations à des clients sur les fonctionnalités des services Microsoft 365**. 

<img src="https://thijoubert.github.io/assets/img/posts/2021-07-22_TestEnvironment_9.png" >

Une autre particularité de ces tenants est qu’ils viennent avec des scénarios et sont pré-alimentés de données. On y retrouve par exemple : 

- **Des scénarios par cas d’usage** : Compliance, Teams, Defender for Endpoint, First Line Workers, etc. 
- **Des scénarios par secteur d’activité** : financier, santé, etc.  

Il existe une vingtaine de scénarios différents. Les scénarios pourront différer selon la plaque géographique considérée et auront une durée de vie de 90 jours ou 1 an (extensibles pour certains). 

Même si ce n’est pas l’objet de cet article, il peut être intéressant d’évoquer les autres fonctionnalités du CDX : 

- Guide interactif
- Platforme de Lab
- Scénario de démonstration (également appelé *Customer Immersion Expérience)*




<br/>
# Quel est l’environnement de test qui vous convient le mieux ? 

<img src="https://thijoubert.github.io/assets/img/posts/2021-07-22_TestEnvironment_7.png" >

En bonus, vous trouverez ci-dessous un comparatif détaillé des licences Microsoft 365 E3, Microsoft 365 E5 et Microsoft E5 Developer. 

||**Microsoft E3**|**Microsoft E5**|**Microsoft E5 <br>(Developer)**|
| :-: | :-: | :-: | :-: |
|Azure Active Directory Premium P1|x|x|x|
|Azure Active Directory Premium P2||x|x|
|Azure Information Protection Premium P1|x|x|x|
|Azure Information Protection Premium P2||x|x|
|Azure Rights Management|x|x|x|
|Common Data Service|x|x|x|
|Common Data Service for Teams|x|x|x|
|Customer Lockbox||x|x|
|Data Classification in Microsoft 365||x|x|
|Exchange Online (Plan 2)|x|x|x|
|Graph Connectors Search with Index||x|x|
|Information Barriers||x||
|Information Protection for Office 365 - Premium||x|x|
|Information Protection for Office 365 - Standard|x|x|x|
|Insights by MyAnalytics|x|x||
|M365 Communication Compliance||x|x|
|Microsoft 365 Advanced Auditing||x|x|
|Microsoft 365 Apps for enterprise|x|x|x|
|Microsoft 365 Audio Conferencing||x||
|Microsoft 365 Defender||x|x|
|Microsoft 365 Phone System||x|x|
|Microsoft Azure Multi-Factor Authentication|x|x|x|
|Microsoft Bookings|x|x|x|
|Microsoft Cloud App Security||x|x|
|Microsoft Communications DLP||x|x|
|Microsoft Customer Key||x|x|
|Microsoft Data Investigations||x|x|
|Microsoft Defender For Endpoint||x||
|Microsoft Defender for Identity||x|x|
|Microsoft Defender for Office 365 (Plan 2)||x|x|
|Microsoft Endpoint DLP||x||
|Microsoft Excel Advanced Analytics||x|x|
|Microsoft Forms (Plan E5)||x|x|
|Microsoft Information Governance||x|x|
|Microsoft Insider Risk Management||x|x|
|Microsoft Intune|x|x|x|
|Microsoft Kaizala Pro|x|x||
|Microsoft ML-based classification||x|x|
|Microsoft MyAnalytics (Full)||x|x|
|Microsoft Planner|x|x|x|
|Microsoft Records Management||x|x|
|Microsoft StaffHub|x|x|x|
|Microsoft Stream for O365 E5 SKU||x|x|
|Microsoft Teams|x|x|x|
|Office 365 Advanced eDiscovery||x|x|
|Office 365 Cloud App Security||x|x|
|Office 365 Privileged Access Management||x|x|
|Office 365 SafeDocs||x|x|
|Office for the web|x|x|x|
|Power Apps for Office 365 Plan 3||x|x|
|Power Automate for Office 365|x|x|x|
|Power BI Pro||x|x|
|Power Virtual Agents for Office 365|x|x|x|
|Premium Encryption in Office 365||x||
|Project for Office (Plan E5)||x|x|
|SharePoint (Plan 2)|x|x|x|
|Skype for Business Online (Plan 2)|x|x|x|
|Sway|x|x|x|
|To-Do (Plan 3)||x|x|
|Universal Print|x|x||
|Whiteboard (Plan 3)||x|x|
|Windows 10 Enterprise|x|x||
|Windows Update for Business Deployment Service|x|x||
|Yammer Enterprise|x|x|x|



# Pour ceux qui veulent tester Azure, je ne vous oublie pas

Les services Azure sont régis par une logique différente que celle de Microsoft 365. Il n’y a pas de licence par utilisateur, mais de la facturation à la consommation. 

Dans le [portail Azure](https://portal.azure.com/#home), il vous est possible de souscrire une souscription gratuite avec un crédit de 180€ (ou 200$).

<img src="https://thijoubert.github.io/assets/img/posts/2021-07-22_TestEnvironment_8.png" >

Il est important de noter que Microsoft demande de vérifier votre identité via un numéro de téléphone et une carte bancaire. Rassurez-vous, rien de vous sera facturé si vous n’avez pas explicitement validé la conversion d’une souscription gratuite en souscription payante. 

En bonus, je vous invite à regarder le poste de Lauro Kokkarinen sur « [comment transférer des crédits Azure vers un tenant développeur](https://laurakokkarinen.com/how-to-use-the-complimentary-azure-credits-in-a-microsoft-365-developer-tenant-step-by-step/) » ? 



# Pour ceux qui veulent tester Power Platform, je ne vous oublie pas non plus

Si vous souhaitez tester la Power Platform (Power App, Power Automate ou Power BI) ou développer des applications, vous avez plusieurs choix. 

Dans le tenant de votre organisation ([selon que votre organisation ait ou non bloqué les licences d’essai ou développeur](https://www.thijoubert.com/2021-07/Control-PowerPlatform-Access/)) :

- Souscrire à des licences d’essai premium (pendant 90 jours)
- Souscrire à des licences d’essai gratuites (sans limite de temps)
- Acheter des licences premium avec votre propre carte bancaire
- Ouvrir un [environnement Power Platform développeur](https://docs.microsoft.com/en-us/powerapps/maker/developer-plan) avec des fonctionnalités premium

Dans un tenant développeur, *trial tenant* ou tenant production, vous pouvez faire ce que vous voulez. 



# Quelques bonnes pratiques sur les environnements de tests

- L’activation des mesures de sécurité basiques (désactivation des protocoles legacy et authentification forte) ne concernent pas que les tenants de production … surtout si les environnements ne sont pas étanches ;
- Pensez à supprimer vos informations de paiements (si vous les avez renseignés pour Azure ou pour un autre service) ;
- Pensez à supprimer votre tenant à la fin de vos tests. 
