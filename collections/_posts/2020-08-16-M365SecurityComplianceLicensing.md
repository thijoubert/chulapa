---
title: "« Petit » guide pour se retrouver dans la jungle des licences sécurité et conformité Microsoft 365"
subtitle: "« Petit » guide pour se retrouver dans la jungle des licences sécurité et conformité Microsoft 365"
excerpt: Qui ne s’est pas déjà senti perdu en cherchant des informations sur les plans de licences autour d’Office 365 ? Quelques conseils ici pour retrouver son chemin. 
tags:
  - Microsoft 365
  - Licensing
  - Security
  - Compliance
header_img : "./assets/img/site/2020-08-13_0_Article.jpg"
---

Qui ne s’est pas déjà senti perdu en cherchant des informations sur les plans de licences autour d’Office 365 ? Avec cet article, je souhaite vous partager un décryptage de ce qui existe avec quelques conseils pour retrouver son chemin, ainsi que rappeler certaines annonces récentes de l’éditeur.

A l’heure du *modern workplace*, il est indispensable pour les équipes sécurité et conformité de connaître les capacités natives des plateformes de collaboration et de communication. Cette maîtrise permettra de **définir une stratégie cohérente prenant en compte aussi bien les besoins de protection des données que les réglementations, l’urbanisation du système d’information et l’incontournable sujet de l’expérience utilisateur**.

*Pour les entreprises bénéficiant du plan de licences le plus élevé, Microsoft 365 E5, il n’y a pas de problème : toutes les fonctionnalités sont disponibles. Pour les autres, le sujet est autrement plus complexe.*

*Cet article est orienté pour les entreprises ayant plus de 300 collaborateurs. Pour les autres organisations (éducation, associations, petites et moyennes entreprises) les plans de licences sont légèrement différents, mais les informations ci-dessous restent applicables pour la plupart.*

# 1/ Comprendre les services de sécurité et de conformité disponibles
Historiquement tournée vers des services de bureautique (avec Microsoft Office) et de collaboration (avec Exchange et SharePoint on-premise), l’offre de Microsoft a fortement évolué **en intégrant des services de développement d’applications sans code (avec la Power Platform), mais aussi des briques de sécurité et de conformité**.

On peut regrouper ces dernières en sept catégories :

- **Sécurité** : Gestion des identités et des accès, Gestion des terminaux et Gestion de la menace ;
- **Conformité** : Protection de l’information, Gouvernance, Maîtrise des services, et Maîtrise du Cloud. 

![2020-08-13_Licensing M365_1_Matrice.png](https://github.com/thijoubert/thijoubert.github.io/blob/c2fcb1046d654c44702c3b0a310402181f485077/assets/img/site/2020-08-13_Licensing%20M365_1_Matrice.png)

## **Gestion des identités et des accès**
[**Azure Active Directory**](https://docs.microsoft.com/fr-fr/azure/active-directory/fundamentals/active-directory-whatis) est la brique fondamentale des services Cloud Microsoft (Office 365 mais aussi Azure IaaS et PaaS). Il ne s’agit pas d’un simple contrôleur de domaine de la source d’identité on-premise dans le Cloud, mais bien d’un service IAM à part entière. Plusieurs plans de licences sont disponibles dans le cadre d’une utilisation Microsoft 365, dont les principales fonctionnalités sont rappelées ci-dessous :

- **Azure Active Directory Basic** pour Office 365 : Single Sign On, Gestion manuelles des utilisateurs, des groupes et des applications, Enregistrement des terminaux, Security Defaults (politiques de sécurité basiques pour les utilisateurs et les administrateurs) ;
- **Azure Active Directory Premium Plan 1** (ou AAD P1) : Azure MFA, Accès Conditionnel, Application proxy (exposition d’applications on-premise sur Internet), Cycle de vie des groupes (expiration, groupes dynamiques, classification), Protection avancée des mots de passe (Cloud et on-premise), Intégration avec une solution de MFA ou de gouvernance des identités tierce ;
- **Azure Active Directory Premium Plan 2** (ou AAD P2) : Azure AD Identity Protection (évaluation des connexions et des comptes à risques), Accès conditionnel basé sur le risque, Azure PIM (Gestion des comptes à privilèges avec Accès *Just-in-Time*), Revue des accès, Entitlement Management (attribution de droits prédéfinis sur des espaces de collaboration à des utilisateurs internes ou externes).

Par expérience, la licence Azure AD Premium P1 revêt aujourd’hui un caractère indispensable pour un certain nombre d’entreprises, à minima pour les deux fonctionnalités suivantes : accès conditionnel et classification des groupes. Azure AD Premium P2 sera plutôt destinée à des populations d’administration dans un premier temps.

Pour rappel, les fonctionnalités disponibles quant à l’ajout ou la modification d’objets (groupes, utilisateurs ou terminaux) varient en fonction du mode d’implémentation choisi : Fédération d’identités, Password Hash Sync (PHS) et Pass Through Authentication (PTA). 
## **Gestion des terminaux**
[**Intune**](https://docs.microsoft.com/fr-fr/azure/active-directory/fundamentals/active-directory-whatis) est la solution de Mobile Device Management (MDM) et Mobile Access Management (MAM) proposée par Microsoft. 

La partie **Intune** **MDM** est historiquement une solution de gestion des terminaux mobiles : déploiement d’applications ou de certificats sur les terminaux enrôlés, durcissement des paramètres, gestion de parc, etc.

La partie **Intune** **MAM** représente les fonctionnalités permettant de maîtriser les données au sein d’applications, via des *apps protection policies*. Le MAM peut être utilisé même dans un contexte BYOD. Il est important de noter que des solutions MDM tierces peuvent s’intégrer avec les Intune MAM pour maîtriser les applications Microsoft 365 Apps (comme Office pour iOS ou Android), mais la licence sera tout de même requise pour utiliser les fonctionnalités du SDK.  

Dans le cadre du *modern management*, la partie MDM d’Intune se positionne comme une solution d’Enpoint Unified Management (ou UEM) pour gérer l’ensemble des terminaux (mobiles ou non) de façon unifiée. L’objectif est à terme de remplacer l’outil SCCM, également appelé Configuration Manager, en se positionnant en concurrence directe avec d’autres solution de MDM déjà bien en place au sein des entreprises.
## **Gestion de la menace**
La suite [**Microsoft Defender**](https://docs.microsoft.com/fr-fr/microsoft-365/security/defender/microsoft-365-defender?view=o365-worldwide) regroupe l’ensemble des fonctionnalités avancées de lutte contre les menaces (prévention, détection, investigation et réaction) de l’environnement Microsoft 365 : messageries, espaces de collaboration, terminaux et identités.

Bien que les différents composants de la suite soient historiquement considérés comme moins performants que d’autres “*pure players”* sur leurs segments respectifs*,* ils ont l’avantage indéniable de proposer une gestion unifiée, ainsi qu’une corrélation des indicateurs. Cet écart tend toutefois à s’estomper depuis deux ans, Gartner ayant même reconnu [**plusieurs briques de la suite Defender (ex-ATP Advanced Threat Protection) comme leaders sur leurs segments**](https://www.microsoft.com/security/blog/2019/12/03/microsoft-security-leader-5-gartner-magic-quadrants/) fin 2019.

On y retrouve trois composants :

- **MDO - Defender for Office 365 (ex-Office ATP)** : Solution de lutte contre les menaces liées aux documents, aux mails et aux liens malveillants. Alors qu’il est possible d’ajouter une passerelle de messagerie tierce, Office ATP est la seule possibilité de protection avancée pour les espaces collaboratifs (SharePoint, OneDrive et Teams) ;
- **MDE - Defender for Endpoints (ex-Windows Defender ATP)** : Solution EDR de l’éditeur de Redmond ;
- **MDI - Defender for Identities (ex-Azure ATP)** : Solution de détection et d’investigation contre la compromission des identités, via l’analyse des signaux de l’Active Directory local. Microsoft a annoncé en février dernier la [**fin du support de la solution historique**](https://techcommunity.microsoft.com/t5/microsoft-security-and/end-of-mainstream-support-for-advanced-threat-analytics-january/ba-p/1539181), Microsoft Advanced Threat Analytics (ATA), pour Janvier 2021.
  *EDIT (Février 2021) : Le support ATA standard a pris fin le 12 janvier 2021. [Le support étendu se poursuivra jusqu’en Janvier 2026](https://techcommunity.microsoft.com/t5/security-compliance-and-identity/end-of-mainstream-support-for-advanced-threat-analytics-january/ba-p/1539181).*
## **Protection de l’information**
Microsoft a récemment regroupé l’ensemble des fonctionnalités de découverte, de classification et de protection des données sous le framework [**Microsoft Information Protection**](https://docs.microsoft.com/fr-fr/microsoft-365/compliance/protect-information?view=o365-worldwide).

A la base, se trouve le moteur d’identification des données sensibles. Le moteur de Microsoft se base sur deux éléments :

- Les **Types d’informations sensibles** (Sensitive Information Type ou SIT en anglais) : Expressions régulières prédéfinies (ex : numéro de sécurité sociale ou carte bancaire) combinées avec des mots clés, empreintes de documents (ex : brevet ou formulaire) ou dictionnaires de mots clés ;
- Les **Classifieurs d’informations** : Algorithmes de *Machine learning*, avec des modèles prédéfinis ou construits. Introduits cette année et encore en préversion, les classifieurs ne peuvent être utilisés qu’avec les licences Microsoft E5.

La tendance actuelle est de classifier les données d’Office 365 (mails, documents et maintenant Power BI) à l’aide d’**Azure Information Protection** (ou AIP). Le choix du niveau de classification peut se faire soit manuellement, soit automatiquement avec le moteur présenté ci-dessus. AIP a été intégré progressivement dans l’ensemble services Office 365, sous le nom d’**Information Protection for Office 365** (ou classification unifiée). Bien que moins nécessaire aujourd’hui, AIP apporte en plus de la nouvelle solution la couverture de documents non-Office 365 et la barre de classification dans les applications bureautiques.

Il est également possible de classifier un espace partagé (site SharePoint, Teams ou Groups Office 365), mais la classification des données et de l’espace est encore décorrélée.

La protection des informations à proprement parler est constituée du chiffrement des données et de la restriction des droits (DRM en anglais). Le protocole propriétaire de Microsoft est **Azure RMS**, ou Rights Management. Les clés peuvent être gérées par Microsoft, en BYOK ou Chiffrement à Double Clé (DKE en anglais, équivalent HYOK pour le Unified Labeling présenté en Juillet 2020)

Azure RMS peut être appliqué à une donnée manuellement ou par héritage du niveau de classification. L’implémentation peut avoir un nom différent selon le cas d’usage concerné, mais les mécanismes sont identiques :

- **AIP** ou **RMS** tout simplement pour les documents ;
- **Information Rights Management** (ou IRM) pour SharePoint : une donnée téléchargée d’une liste ou d’une bibliothèque hérite d’une protection cohérente avec les droits de l’utilisateur ;
- **Office Message Encryption** (ou OME) pour la messagerie électronique.

En plus de la protection ci-dessus, il est également possible d’appliquer une protection des espaces partagés afin de durcir les accès en fonction du niveau de classification choisi, par exemple la restriction des terminaux ou des utilisateurs invités par exemple.

En sus des mécanismes attachés aux données (la protection reste même en cas de partage ou de copie), il est possible de contrôler la diffusion des données via les outils suivants :

- **Office DLP** : contrôle de la diffusion des mails et des documents ;
- **Communications DLP** : contrôle de la messagerie instantanée ;
- **Cloud App Security** : extension des capacités d’Office DLP aux applications SaaS intégrées ;
- **Windows Information Protection :** équivalent d’Intune MAM pour Windows 10, visant à séparer les données professionnelles des données personnelles ;
- **Windows Endpoint DLP** : nouvelle solution DLP pour Windows 10 présentée en Juillet 2020.

Enfin, une découverte des informations peut être réalisée *à posteriori* pour localiser et corriger le niveau de protection si besoin. Là encore, une solution différente doit être utilisée en fonction du cas d’usage : 

- **AIP Scanner** : recherche et classification des données sur des répertoires on-premises ;
- **Cloud App Security** : recherche et classification des données sur les espaces Cloud ;
- **Defender for Endpoints** : recherche et classification des données sur Windows 10 ;
- **eDiscovery** : recherche des données Cloud (par détournement de la fonctionnalité originelle).

Le [**SDK Microsoft Information Protection**](https://docs.microsoft.com/fr-fr/information-protection/develop/overview) peut être utilisé par des applications tierces pour appliquer de la classification ou de la protection sur des données, ou tout simplement consommer des données protégées.

Comme vous pouvez le voir, il y a un certain nombre d’outils avec des noms différents pour protéger les données des organisations. A retenir, les utilisateurs ne seront confrontés directement qu’à la classification et à la protection. 
## **Gouvernance**
L’année 2020 pourra être retenue comme l’année de la conformité pour Office 365. Microsoft a en effet réorganisé les produits existants et en a présenté des nouveaux pour apporter des réponses à différents risques RH et réglementaires. Tous ces produits sont regroupés dans le nouveau **Centre de Conformité**, qui remplace la partie équivalente dans le Security & Compliance Center.

Le premier groupe de ces produits est lié à la rétention des informations. Des **politiques de rétention** (conservation, enregistrement légal, suppression, etc.) sont définies via des étiquettes de rétention pour être appliquées à une donnée ou à un espace partagé. L’étiquetage peut se faire manuellement, par défaut sur des conteneurs (ex : boîtes mails utilisateurs ou sites SharePoint), ou automatiquement, de la même façon que pour les étiquettes de confidentialité.

On retrouve ensuite les produits liés à la [**traçabilité et à l’audit du tenant**](https://www.riskinsight-wavestone.com/2020/03/journalisation-doffice-365-un-cas-concret-avec-les-administrateurs/). Par construction, les **journaux unifiés** (ou Unified audit logs en anglais) permettent de tracer les actions des utilisateurs ou des administrateurs. Ces logs, bien que très complets, ne sont pas exhaustifs et sont régulièrement complétés. Afin d’augmenter les 90 jours de conservation des journaux unifiés, Microsoft a présenté l’année dernière la **journalisation avancée** (ou Advanced auditing), qui propose une conservation jusqu’à un an et des journaux plus complets (comme l’ensemble des accès à une boîte mail).

En parallèle de la journalisation, quatre produits offrent des possibilités d’investigation :

- **Core eDiscovery** permet d’extraire du contenu selon une requête (ex : messages envoyés par un utilisateur contenant telle ou telle information) ;
- **Advanced eDiscovery** est une fonctionnalité avancée pour filtrer les contenus les plus pertinents et apporter des possibilités de visualisation sur les résultats ;
- **Microsoft Data Investigations**, encore en préversion,** est un clone d’Advanced eDiscovery permettant de retracer le contexte ayant pu conduire à une fuite de données ;
- **Data Subject Request** a été introduit lors de l’entrée en vigueur du GDPR, afin d’identifier et exporter des données liées à une personne physique. Il s’agit, là encore, d’un clone de Core eDiscovery permettant d’effectuer des recherches dans ce contexte précis.

A noter les fonctionnalités d’eDiscovery d’Exchange Online (Hold, search, etc.) vont être progressivement dépréciées, au profit de celles du Compliance Center.

Enfin, Microsoft a récemment présenté tout un [**panel de produits pour lutter contre les risques internes**](https://docs.microsoft.com/en-us/microsoft-365/compliance/insider-risk-management?view=o365-worldwide) (délit d’initié, fuite de données par des utilisateurs sur le départ, discrimination, accès illégitime à la donnée, etc). Concrètement, ces produits ont vocation à implémenter et automatiser des principes déjà existants dans les politiques RH, légales et métiers existantes des organisations :

- **Insider Risk Management** est une fonctionnalité permettant de lever des alertes en cas d’actions suspectes réalisées par un utilisateur interne (ex : téléchargement massif réalisé par un utilisateur sur le départ, violation de stratégie de sécurité). Le produit est centré autour des axes suivants : alerte, investigation, remédiation automatique ou manuelle ;
- **Information Barriers** permet de réguler les échanges (OneDrive, SharePoint et Teams) entre des personnes internes, afin de forcer techniquement des interdictions d’échanges de contenus entre des entités en raison d’impératifs réglementaires ;
- **Communications Compliance** étend les fonctionnalités d’Office DLP en permettant de lever des alertes lorsqu’une communication inadéquate est détectée (Teams, Mail ou Yammer), comme une non-conformité réglementaire, un non-respect d’une politique interne (ex : harcèlement) ou des échanges autour d’un projet spécifique ;
- **Privileged Access Management** (ou PAM) est le pendant d’Azure PIM pour les tâches opérationnelles d’administration. Afin d’exécuter une tâche, une personne devra demander une élevation de privilège pour un périmètre et un temps défini ;
- **Customer Lockbox** : Lockbox est le nom du processus interne Microsoft permettant à une personne du support d’accéder à des données d’une organisation. Customer Lockbox ajoute une étape de validation par le client concerné. En pratique, ce produit permet de s’assurer qu’un employé Microsoft ne modifie pas des données par inadvertance ; mais ne permet pas de se prémunir des [**requêtes gouvernementales**](https://docs.microsoft.com/en-us/microsoft-365/compliance/customer-lockbox-requests?view=o365-worldwide#frequently-asked-questions). Sur ce dernier sujet, Microsoft publie [**régulièrement des statistiques**](https://www.microsoft.com/en-us/corporate-responsibility/law-enforcement-requests-report?rtc=1).

La majeure partie de ces produits sont encore en préversion. Il n’y a encore que très peu de retours terrain sur ces solutions appelées à gagner en maturité.
## **Maîtrise des services**
Outre les produits décrits dans le chapitre précédent, Microsoft propose aux organisations deux outils supplémentaires pour se mettre en conformité avec les réglementations locales.

Tout d’abord, **Customer Key** permet à une organisation d’ajouter une surcouche de chiffrement au niveau applicatif (Exchange Online, OneDrive, SharePoint Online et Teams), en gérant le cycle de vie des clés utilisées. Cette surcouche s’ajoute au chiffrement appliqué par construction aux données au repos dans les serveurs Microsoft. Attention toutefois à ne pas perdre les clés, ce qui conduirait à une perte totale des données.

Ensuite, les fonctionnalités de **Multi-géo** garantissent que les données soient conservées au repos dans une zone géographique donnée. L’enjeu avec cette fonctionnalité est d’être capable de différencier les espaces personnels et partagés en fonction de la localisation cible.
## **Maîtrise du Cloud**
Avec [**Cloud App Security**](https://docs.microsoft.com/fr-fr/cloud-app-security/what-is-cloud-app-security), Microsoft a son Cloud Access Security Broker (CASB) : lutte contre le Shadow IT (utilisation des API des solutions supervisées ou des applications SaaS non gérées via l’analyse des logs du proxy), Protection des données, Détection des comportements anormaux et Analyse de la conformité des applications SaaS.

Encore une fois, trois niveaux de fonctionnalités sont disponibles :

- **Cloud App Discovery** : Accessible avec Azure AD P1, ce niveau permet de bénéficier des fonctionnalités de lutte contre le Shadow IT ;
- **Office 365 Cloud App Security** : Accessible avec une licence Office E5, il s’agit d’un niveau intermédiaire permettant de bénéficier de fonctionnalités dégradées limitées à Office 365 ;
- **Microsoft Cloud App Security** : Niveau le plus élevé l’ensemble des fonctionnalités du CASB.

Il est importun de rappeler ici qu’Azure AD P1 sera nécessaire, si l’on souhaite mettre en place des politiques d’accès conditionnels pour les applications connectées (incluant Office 365).

Avec les fonctionnalités de Gouvernance présentées précédemment, Cloud App Security est la brique la moins exploitée aujourd’hui, principalement en raison du niveau trop élevé des licences.

# 2/ S'approprier la logique de *licensing*
Pour les personnes profanes au *licensing* Microsoft, trois principes régissent l’attribution de licences en fonction de la population concernée :

- Un utilisateur interne d’un service ou bénéficiant indirectement dudit produit (ex : groupe dynamique, classification d’un site SharePoint, partage de tableaux de bords Power BI) doit avoir la licence requise ;
- La plupart des rôles d’administrations nécessitent la licence du service administré pour accéder au portail d’administration ou aux commandes PowerShell associées ;
- Les utilisateurs externes ou les utilisateurs invités (*guest* en anglais) [**n’ont pas besoin de licence spécifique**](https://docs.microsoft.com/en-us/sharepoint/external-sharing-overview) pour collaborer sur du contenu Office 365. Cela est permis grâce aux capacités gratuites d’Azure AD. Néanmoins, si utilisateur invité est soumis à des fonctionnalités d’Azure AD Premium (P1 ou P2), un [**nombre suffisant de licences doit être disponible**](https://docs.microsoft.com/en-us/azure/active-directory/external-identities/licensing-guidance) (à raison de 1 licence achetée pour 5 utilisateurs invités).
  *EDIT (Décembre 2020) : un nouveau modèle de facturation des utilisateurs invités est rentré en vigueur début 2021, [MAU. Les 50 000 premiers Azure AD P1 et P2 seront gratuites](https://docs.microsoft.com/en-us/azure/active-directory/external-identities/external-identities-pricing).Les prochaines seront facturées sur base des utilisateurs invités actifs.*

Les licences sont nominatives et s’entendent par utilisateur et par mois.

A noter qu’un même produit peut être disponible avec des **fonctionnalités plus ou moins avancées en fonction du niveau des licences choisi**. Un exemple récurrent concerne les journaux d’audits unifiés : ces logs sont conservés 90 jours avec des licences Office E1 ou E3 tandis qu’avec des licences Office E5 la durée est de 365 jours.  

# 3/ Percer le mystère des plans de licences
Pour rappel, le modèle des licences Microsoft est constitué des éléments suivants :

- **Plan de licences** : Un plan définit les services souscrits à l’éditeur qui sont disponibles dans le tenant. La plupart du temps, un plan de licences sera un bundle collaboratif (Office 365), un bundle de sécurité (EMS) ou un package (Microsoft 365) ;
- **Licence** : Pour être considéré comme actif, et donc pouvoir se connecter au tenant, un utilisateur doit au moins posséder une licence ;
- **Service** : Un service est un produit, une fonctionnalité ou une capacité de Microsoft 365 nécessitant une licence. Cette licence peut provenir de plusieurs plans de licences différents : par exemple Office 365 E1 octroie SharePoint Online Plan 1 tandis qu’Ofice 365 E3 et E5 apportent SharePoint Online Plan 2 ;
- **SKU** : En langage Microsoft, ce terme tiré de la gestion des stocks désigne l’implémentation d’une licence pouvant être assignée à un utilisateur.
## **Les bundles collaboratifs Office 365 : des fonctionnalités de protection de la donnée et conformité incluses nativement**
Les plans de licences collaboratifs, également appelés bundles Office 365, sont à la base du *licensing* Microsoft 365. Ces plans intègrent nativement des fonctionnalités croissantes de conformité. Les options de sécurité sont quant à elles assez limitées et doivent être souscrites indépendamment.

Le premier plan est **Office 365 E1**. Ce plan intègre l’ensemble des services bureautiques en mode web uniquement. Les produits de conformité et de sécurité correspondent au minimum vital de ce que l’on peut attendre d’un service SaaS d'entreprise aujourd’hui : Security Defaults (MFA basique), Journaux d’audits, Recherche de contenus et Etiquettes de rétention.

**Office 365 E3** ajoute à E1 les clients lourds de la suite bureautique (désormais appelée Microsoft 365 Apps), ainsi que des fonctionnalités de protection de la donnée (Information Protection for Office 365 et Office DLP), Core eDiscovery et des politiques de rétention par défaut. Ce plan de licences est celui préféré par les entreprises aujourd’hui pour des utilisateurs standards.

Enfin, **Office 365 E5** s’adresse plutôt à des populations particulières sur le plan bureautique avec la téléphonie, Power BI Pro et des statistiques sur l’utilisation de la suite Office 365. Ce plan intègre également la classification automatique (hors machine learning), des options de conformité pour les populations soumises à des réglementations (Records Management, Customer Key, Customer Lockbox, Information Barriers, Communications Compliance) et des options d’investigations avancées (Advanced eDiscovery et Data Investigations), ainsi que MDO ou Office CAS.

Deux points importants à noter :

- Office DLP et AIP P1 peuvent être souscrits comme licences additionnelles pour des utilisateurs Office E1, afin d’avoir des fonctionnalités de protection de la donnée similaires à Office E3 ;
- L’option de Multi-Géo est une licence additionnelle, quel que soit le plan de licences choisi. 
## **Les bundles de sécurité : des fonctionnalités de sécurité complémentaires**
Introduit dès 2014, le bundle de sécurité EMS (Enterprise Mobility Suite, puis Enterprise Mobility + Security) intègre divers produits de sécurité. Ces produits ont pour vocation à maîtriser les identités, les terminaux en situation de mobilité et les applications accédant aux données Office 365. 

- **EMS E3** : Intune, Azure AD P1, AIP 1, Advanced Threat Analytics ;
- **EMS E5** : Azure AD P2, AIP P2, MDI et Microsoft Cloud App Security.

Aujourd’hui, EMS E3 se révèle indispensable pour des organisations faisant le choix de partir sur une stratégie « Full Microsoft ». En effet, Intune et Azure AD P1 offrent une stratégie cohérente pour gérer les accès à la plateforme Office 365. En revanche, peu d’organisations ont fait le choix de généraliser EMS E5, un bundle plutôt orienté pour des populations sensibles ou administrateurs, en raison d’un manque de cohérence entre les différents produits de sécurité inclus.
## **Les packages Microsoft 365 : une offre complète mais onéreuse**
Annoncé en 2017, Microsoft 365 est désormais le produit phare de l’éditeur de Redmond. Ce plan de licences combine les fonctionnalités d’Office 365, de la suite EMS et de Windows 10 :

- **Microsoft 365 E3** = Office 365 E3 + EMS E3 + Windows 10 E3 ;
- **Microsoft 365 E5** = Office 365 E5 + EMS E5 + Windows 10 E5.

Contrairement à une idée répandue, et malgré les divers changements de noms introduits en 2020 (Office 365 Groups en Microsoft 365 Groups, Office Pro Plus en Microsoft 365 Apps), la marque Office 365 n’a pas disparu. 

Il peut être opportun de noter, que **Microsoft 365 E5 est le seul abonnement bureautique incluant les Trainable Classifiers** (classification via Machine Learning), **Insider Risk Management ou Safe Documents** (extension de MDE pour scanner les documents ouverts en mode protégé).
## **Microsoft 365 E5 Compliance et Sécurité : un tournant dans la gestion des licences sécurité et conformité**
Microsoft 365 E5 Compliance et Microsoft 365 Sécurité ont été introduits début 2020, afin de simplifier le *licensing* sécurité et conformité en regroupant les produits sous des plans de licences cohérents. Une bonne nouvelle, car la situation était devenue complexe entre les produits EMS et les produits de conformité historiques (ex : Advanced Data Governance et Advanced Data Compliance).

**Microsoft 365 E5 Compliance** combine l’ensemble des fonctionnalités de protection de l’information, de gouvernance et d’investigation. En fonction des besoins, trois sous-bundles peuvent être envisagés :

- **Microsoft 365 E5 Information Protection & Governance** : AIP P2, Microsoft Cloud App Security, Advanced Retention Policies, Records Management, Advanced Office DLP et Advanced OME, Customer Key et Trainable Classifiers ;
- **Microsoft 365 E5 Insider Risk Management** : Insider Risk Management, Communications Compliance, Information Barriers, Customer Lockbox et PAM ;
- **Microsoft 365 E5** **eDiscovery & Audit** : Advanced eDiscovery, Advanced Auditing et Data Investigations.

Présentées officiellement comme des extensions de Microsoft 365 E3, la documentation laisse entendre que les prérequis en termes de licences seraient moindres. L’extension Information Protection & Governance ne nécessiterait « que » AIP P1 et les Plans 2 d’Exchange Online et SharePoint Online (soit Office 365 E3).

**Microsoft 365 E5 Sécurité** combine Azure AD P2, la suite Defender (Defender for Office 365, Defender for Endpoints, Defender for Identies) et Cloud App Security. Ce bundle sera intéressant pour les organisations peu dimensionnées pour gérer de nombreux outils de sécurité (MFA, EDR, AD Monitoring, Passerelle mail, CASB) ou celles qui souhaitent basculer dans une approche plateforme. 

![2020-08-13_Licensing M365_2_Microsoft E5.png](https://github.com/thijoubert/thijoubert.github.io/blob/c2fcb1046d654c44702c3b0a310402181f485077/assets/img/site/2020-08-13_Licensing%20M365_2_Microsoft%20E5.png)
## **Focus sur les Firstline Workers**
Les plans de licences Office 365 F3 et Microsoft 365 F1 et F3 sont destinés aux [**Firstline Workers**](https://www.microsoft.com/en-us/licensing/news/m365-firstline-workers) :

- **Microsoft 365 F1** est un plan de licences qui regroupe EMS E3, Teams et SharePoint (uniquement en partage et consommation de contenu) ;
- **Microsoft 365 F3** combine EMS E3, Windows 10 E3 et Office 365 F3 ;
- **Office 365 F3** est une version allégée de Office 365 E1, avec des fonctionnalités similaires (Exchange, SharePoint, OneDrive, Teams et Power Platform principalement) mais du stockage nettement plus limité pour OneDrive et Exchange.

Microsoft définit cette population par « utilisateur sans terminal dédié, avec un usage occasionnel. » Concrètement, un terminal dédié est un matériel informatique ayant un écran de plus de 10,1 pouces, utilisé par un collaborateur à hauteur de plus de 60% de son temps de travail. Des exemples peuvent être des populations médicales, des vendeurs dans un magasin ou des ouvriers dans une usine.

Les licences Fx ne peuvent donc pas être utilisées pour optimiser les coûts de *licensing* pour des populations n’ayant pas de besoins avancés. 

# 4/ Se doter des bons outils pour trouver les informations pertinentes 
Il n’existe pas de cartographie officielle permettant de se retrouver facilement entre les produits et les niveaux de licences (E1, E3, E5, F1, F3, etc.), à croire que tout semble fait pour orienter les entreprises vers les licences les plus onéreuses. Il n’est pas donc étonnant de voir que des entreprises se soient spécialisées sur le segment très spécifique du *licensing* Microsoft (conseil en optimisation ou éditeurs de solution de gestion).
## **Comment se renseigner sur ce qui existe (sources officielles)**
Concernant les licences liées aux produits conformité et sécurité, la référence la plus complète est la documentation "[**Conseils de licence Microsoft 365 pour la conformité & la sécurité**](https://docs.microsoft.com/en-us/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-tenantlevel-services-licensing-guidance/microsoft-365-security-compliance-licensing-guidance)". Malheureusement, cette documentation officielle n’est pas exhaustive, il y manque par exemple :

- Les produits concernés par les préversions privées ou publiques. Par exemple, le nouvel Endpoint DLP nécessite par exemple une licence Microsoft 365 E5 Compliance ou Microsoft 365 E5 Information Protection & Governance ;
- Des détails concernant certains produits conformité. Par exemple, Office DLP est disponible avec une licence additionnelle ;
- Les informations liées aux fonctionnalités Azure Active Directory Premium P1 ou P2 et celles liées à Intune.

A noter, un **tableau assez complet**, disponible en .pdf et en .xlsx, propose un **recoupage de cas d’usages et des licences conformité**. Attention, ce tableau peut faire peur !

Concernant les licences sécurité, il n’existe pas encore de synthèse officielle équivalente. La documentation des produits (ex : [**Intune**](https://docs.microsoft.com/en-us/mem/intune/fundamentals/licenses)) et les pages d’informations sur le *licensing* (ex : [**Azure Active Directory**](https://azure.microsoft.com/en-us/pricing/details/active-directory/) restent les meilleures sources d’information.

Point important : la plupart des sources officielles précisent qu’elles ne constituent pas un engagement contractuel suffisant. Seul un échange avec le TAM Microsoft permettra de confirmer la disponibilité d’une licence spécifique et le prix associé. 
## **Comment se renseigner sur ce qui existe (sources non-officielles)**
En dehors de la documentation officielle, j’utilise deux sources assez intéressantes lorsque l’on évoque le sujet du *licensing* Microsoft 365 :

- [**Cartographie non-officielle des produits Microsoft 365**](https://github.com/AaronDinnage/Licensing), faite par Aaron Dinnage (Microsoft) : il s’agit du document disponible le plus complet sur le sujet ;
- [**Détails et prix publics**](https://www.infusedinnovations.com/blog/secure-modern-workplace/complete-office-365-and-microsoft-365-licensing-comparison) (en dollars) des différents plans de licences Microsoft 365, faite par Dan Chemistruck (Infused Innovation).
## **Comment se renseigner sur ce qui est disponible dans le tenant**
Il existe trois possibilités pour maîtriser les licences (unitaires, *bundles* ou *packages*) et les produits acquis dans un tenant Office 365.

La première et la plus simple consiste tout simplement à utiliser les **informations disponibles dans les portails d’administration Office 365 ou Azure**. Cependant, ces portails ne proposent que des fonctionnalités assez basiques : pas d’actions pour un grand nombre d’utilisateurs, un tableau de bord (licences acquises, utilisées et non conformes) global sans granularité par pays ou par entité, etc.

La deuxième option est d’acquérir un **outil de gestion ou d’optimisation des licences** (ex : ManageEngine, QuadroTech, CoreView). Ce type de solutions s’adresse plus au PME qu’aux grands groupes qui préfèrent la troisième option en raison d’économies d’échelle.

Cette dernière option consiste à développer un outil (à base de scripts PowerShell et d’API Microsoft Graph) d’attribution des licences et un tableau de bord (généralement sur Power BI). Ce choix permettra de palier les limites des outils natifs, mais également de déléguer la maîtrise des licences aux différents Local IT dans un contexte décentralisé.
## **Focus sur le développement : comment se retrouver parmi les noms**
Le développement en lui-même ne présente pas de complexité particulière. En revanche, un problème courant apparaît très rapidement. Les noms des services obtenus par PowerShell et Graph API sont tout simplement incompréhensibles. Ces noms sont souvent issus de rachats ou de noms de projets internes Microsoft (ex : ADALLOM pour MCAS, RIGHTSMANAGEMENT pour AIP P1 ou encore SPE\_E3 pour Microsoft 365 E3).

Par expérience, il est alors **indispensable de garder à jour une liste de correspondances entre les noms des SKUs obtenus par scripting et les noms officiels** :

- La [**liste officielle Microsoft**](https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/licensing-service-plan-reference) est malheureusement loin d’être exhaustive et n’est pas mise à jour régulièrement ;
- Plusieurs listes non officielles sont maintenues tant bien que mal et disponibles sur Internet.

# 5/ Sept conseils pour définir sa stratégie de *licensing* sécurité et conformité
![2020-08-13_Licensing M365_3_Bonnes pratiques.png](https://github.com/thijoubert/thijoubert.github.io/blob/c2fcb1046d654c44702c3b0a310402181f485077/assets/img/site/2020-08-13_Licensing%20M365_3_Bonnes%20pratiques.png)

1. **Identifier ses besoins** en termes de sécurité (identité, menaces, terminaux, etc.) et conformité (protection de la donnée, conformité réglementaire, etc.) pour Office 365 ;
1. **Formaliser l’inventaire** de l’ensemble des outils de sécurité et de conformité liés au *Digital Workplace* disponibles dans l’entreprise (incluant passerelle mail, EDR, classification, DLP, etc.) ;
1. **Formaliser une feuille de route pour l’outillage sécurité et conformité**, dans une logique cohérente avec le modern workplace (rationalisation, sécurité native sans agents, zéro trust) ;
1. **Définir un modèle de licences avec différents profils utilisateurs**, conjointement avec les équipes architecte et workplace. Il peut être intéressant de privilégier des bundles en prenant en considération les besoins moyens et long terme. L’acquisition de licences unitaires (ou add-on) hors négociation globale se fera au prix fort ;
1. **Anticiper les capacités de ciblage des produits**. Certains produits (comme les fonctionnalités d’Azure Active Directory ou MCAS) s’adaptent difficilement à un modèle de licences compliqué dans un contexte multi-entités international ;
1. **Activer ce qui est disponible sur opportunité** dans les bundles acquis, en évitant les doublons avec les outils existants afin de ne pas brouiller les signaux ;
1. **Faire de la veille**. Les fonctionnalités associées à une licence peuvent évoluer à la suite d’un développement ou d’un rachat de solution tierce. Plus rare, Microsoft peut embarquer des fonctionnalités premium dans des plans basiques. Le centre de messages du portail d’administration et les [**blogs Sécurité et Conformité**](https://techcommunity.microsoft.com/t5/microsoft-security-and/ct-p/MicrosoftSecurityandCompliance) sont ici indispensables. 



Pour aller plus loin, retrouvez dans cet article les [**différents sujets à traiter lors de la préparation de l’aventure Microsoft 365**](https://www.riskinsight-wavestone.com/2020/07/comment-migrer-son-environnement-de-travail-sereinement-vers-office-365/).
