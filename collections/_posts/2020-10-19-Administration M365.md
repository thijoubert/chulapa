---
title: "Quelques pistes pour maîtriser l'administration de Microsoft 365"
subtitle:
excerpt: Quelles fonctionnalités natives dans la plateforme de Microsoft ? Comment gérer la délégation ? Quelles mesures de protection pour ces comptes sensibles ?
tags:
  - Administration 
  - Microsoft 365
  - Security
header_img : "./assets/img/posts/2020-10-19_Administration M365.jpg"
---

Au sein de toute infrastructure ou application, les comptes à privilèges sont des comptes particulièrement sensibles. Leur sécurisation est un sujet clé. Cela est d’autant plus vrai pour les services SaaS, pour lesquels le **modèle de responsabilité partagé impose à une organisation de protéger ses données et ses identités**, et la suite Microsoft 365 ne déroge pas à la règle.

D’ailleurs, s’il y a bien une chose que vous devez protéger, ce sont bien vos administrateurs !
{: #myid .alert .alert-info .p-3 .mx-2 mb-3}

Qu’elles concernent les méthodes d’authentification, les permissions des applications tierces via les API (en autorisant une application tierce à synchroniser des données avec un service de stockage externe par exemple) ou encore la [**modification des politiques de rétention**](https://www.theregister.com/2020/08/24/kpmg_microsoft_teams/), Une action d’administration peut considérablement affecter les données et la sécurité du tenant à plus large échelle. S’il est nécessaire d’expliciter encore ce point, un Administrateur général (ou _Global Administrator_ en anglais) a la capacité d’accéder à l’ensemble des données ou de gérer tous les paramétrages d’Office 365, Windows 10, d’Azure AD… mais également d’Azure ! 

# 1/ Quelles fonctionnalités natives dans la plateforme de Microsoft ?

## Quels modèles de droits au sein de Microsoft 365 ?

A ce jour, Microsoft 365 comporte deux niveaux de droits principaux. Ces deux niveaux permettent schématiquement de déléguer des droits d’administration en s’adaptant aux différents modèles d’organisations (petites / moyennes / grandes, centralisées / décentralisées) :

- **Rôles Azure AD** : Administration des services Azure AD et Microsoft 365 ;
- **Rôles RBAC** : Administration des objets au sein des services.

### Premier niveau : Utilisation des rôles Azure AD pour gérer les services

La personne à l’origine de l’ouverture du tenant récupère automatiquement le rôle d’Administrateur Général. Il peut alors nommer d’autres administrateurs pour l’accompagner dans ses tâches. Dans la mesure du possible, les droits de Global Admin ne doivent pas être utilisés afin de limiter une surexposition des comptes d’administration. Une bonne pratique, consiste à limiter ce rôle général à un maximum de 3-4 comptes. De plus, pour la quasi-totalité des actions, il existe un rôle d’administration de service équivalent (ex : SharePoint Administrator, User Administrator, etc.).

Ces rôles d’administration de services sont également appelés [**rôles Azure AD**](https://docs.microsoft.com/fr-fr/microsoft-365/admin/add-users/azure-ad-roles-in-the-mac?view=o365-worldwide). En effet, chaque service peut être vu comme une application Azure AD. Un administrateur serait ainsi équivalent au propriétaire du service en question. A l’heure de l’écriture de cet article, Microsoft propose 59 rôles différents, ce qui permet d’avoir **un bon niveau de ségrégation des droits** dans la plupart des cas.

Cependant, les rôles proposés par défaut donnent accès à l’intégralité du service administré pour l’ensemble du tenant et peut donner certains cas donner accès aux données sous-jacentes (notamment pour SharePoint Administrator, Exchange Administrator et User Administrator). 

<img src="https://thijoubert.github.io/assets/img/posts/2020-10-19_Administration_1_Roles.png" >

Dans le **cas d’une maturité avancée,** il est possible d’aller plus loin dans la ségrégation des droits en créant des **rôles Azure AD personnalisés**. Concrètement, cela revient à décider de quelles permissions bénéficie ce rôle (ex : « microsoft.directory/applications/create » permet de créer des applications dans Azure Active Directory).

Le revers de la médaille sera qu’il sera plus compliqué d’auditer l’administration et qu’il sera nécessaire de veiller à l’évolution des services afin de s’assurer que les permissions restent cohérentes avec les besoins des administrateurs.

### Second niveau : Utilisation du modèle RBAC pour gérer les objets

Certains services tels que Exchange Online, Intune, les Centres de Securité et de Conformité ou encore Cloud App Security proposent des [**modèles de droits RBAC spécifiques**](https://docs.microsoft.com/fr-fr/microsoft-365/security/office-365-security/permissions-microsoft-365-compliance-security?view=o365-worldwide).

Comme son nom l’indique, le *Role Based Access Control* (RBAC)*,* permet d’implémenter une gestion des permissions plus fine ; avec la capacité de définir des rôles pour des périmètres définis (exemple sur certains groupes d’utilisateurs). Par exemple, il sera possible de créer « Helpdesk A » et « Helpdesk B » dans Exchange Online pour donner des droits de support à deux équipes distinctes sur un périmètre A et un périmètre B.



## Comment provisionner les comptes d’administrations ?

La première question est de savoir comment créer l’identité d’un administrateur. Deux stratégies sont possibles :

- La **création d’un compte dans le référentiel d’identité de l’organisation**, qui sera ensuite synchronisé avec Azure AD (ex : wavestone.com) ;
- La **création du compte directement dans Azure AD**. Ce** compte sera alors dit « cloud-only » (exemple : wavestone.onmicrosoft.com).

Quel que soit le rôle d’administration, il est recommandé pour un service SaaS comme Microsoft 365 que le **compte se situe au plus près de la ressource administrée**. Ici, cela revient à **utiliser des comptes cloud-only**. L’objectif est double : se prémunir d’une éventuelle indisponibilité du d’une compromission du référentiel d’identité de l’organisation.



## Comment attribuer des permissions ?

La deuxième question est de savoir comment attribuer les bons privilèges aux rôles d’administration créés.

### Dans le cas de l’administration des services

Afin d’attribuer un rôle AAD, il est possible d’utiliser 3 méthodes (via le portail ou la commande PowerShell correspondante) :

- **Le portail Azure** (portal.azure.com)** : c’est **la méthode** qui doit être privilégiée, car elle permet l’association de droits au plus près des ressources et l’utilisation de PIM, dont nous parlerons dans la suite de l’article ;
- **Le portail Microsoft 365** (admin.microsoft.com) : il est possible de réaliser l’attribution des rôles directement à travers le portail principal d’administration. Cependant cette méthode n’est pas compatible avec PIM ;
- **L’utilisation d’outils IAM tiers** : ces solutions présentent aujourd’hui des connecteurs avec Office 365 pour réaliser le provisioning des identités et des privilèges. Ces solutions offrent une granularité moindre, ne sont pas compatibles avec PIM et sont source d’erreurs courantes. Par exemple, la synchronisation est généralement en sens unique, ce qui entraîne la réapparition du compte d’administration si celui-ci n’est supprimé que dans Azure AD.

A noter, il est également désormais possible d’assigner un rôle Azure AD à un groupe de sécurité (Cloud uniquement) via [**une fonctionnalité en preview**](https://docs.microsoft.com/fr-fr/azure/active-directory/users-groups-roles/roles-groups-concept). Cela pourrait simplifier certains modèles d’administration, dans lesquels l’équipe Communications Unifiées doit par exemple bénéficier du rôle SharePoint Administrator et de Teams Administrator. Attention cependant à la gestion de ce groupe.

### Dans le cas de l’administration des objets

Pour les rôles RBAC, la définition des rôles se fait directement dans la plateforme d’administration du service concerné. Il est alors possible d’assigner le rôle en question manuellement ou à un groupe de sécurité, dans le portail ou via une solution IAM. 

On pourrait également envisager une combinaison, avec l’élevation d’un rôle RBAC via l’activation de l’appartenance à un groupe de sécurité privilégié via Azure AD PIM. 

<img src="https://thijoubert.github.io/assets/img/posts/2020-10-19_Administration_2_Differences.png" >


# 2/ Comment bâtir et implémenter son modèle d’administration ?

## Quelle stratégie pour définir son modèle de droits ?

La construction d’un modèle de délégation doit se faire sur le **principe du moindre privilège**. Le cœur du travail est faire l’inventaire des cas d’usages d’administration d’Office 365 et de faire la **correspondance entre vos équipes et les droits disponibles**.

Cela peut être l’occasion de remettre à plat l’organisation des équipes traitant de l’environnement de travail. Trois constats sont assez significatifs :

- Les terminaux mobiles et les postes de travail sont destinés à être gérés par des solutions unifiées (UEM) comme Intune, Workspace One ou MobileIron, et donc par la même équipe.
- Les outils de sécurité et de conformité sont de plus en plus intégrés nativement dans Office 365. Il est donc nécessaire de faire tomber le mur qui existait entre le monde *workplace* et le monde sécurité, afin de créer une équipe ayant la même ambition : créer et maintenir une plateforme maîtrisée et sécurisée.
- De nouveaux métiers apparaissent : le « compliance officer » est un nouveau rôle que doivent identifier les entreprises afin de gérer la conformité liée au donnée, et qui ne peut pas être portée par les équipes de sécurité classiques. 

Office 365 a pour particularité de rassembler une multitude de services différents, tels que le stockage de fichier ou d’informations (SharePoint, OneDrive), des outils de communication (Exchange, Teams) mais aussi de sécurité (Defender, Information Protection, etc.). Il est alors indispensable de regrouper les services en catégorie et de définir une **matrice de correspondance** entre équipe et rôles d’administration.

Concrètement, il est recommandé dans un premier temps d’**utiliser les rôles Azure AD par défaut pour l’administration des services, et ensuite de définir des rôles plus granulaires** avec RBAC et les rôles personnalisés.

Il est également intéressant **d’identifier** **les rôles les plus sensibles**, comme ceux permettant l’accès aux données ou paramètres de sécurité (par exemple : Global Admin, Exchange Admin, Security Administrator et Application Administration) afin de pouvoir adapter la sécurisation de ces rôles.



## Comment déléguer des droits d’administration sur les objets dans un contexte multi-entité ?

Avant de parler sécurisation à proprement parler, se pose encore une autre question. Bien que **la configuration des services et des paramètres de sécurité ne puisse se faire que centralement, les équipes locales ont besoin de faire actions de support** : création ou modification d’un compte interne ou invité, réinitialisation des authentifiants, création de groupe Microsoft 365 ou d’une liste de distribution, etc.

Les rôles d’administration de services, les rôles Azure AD, n’offrent **pas de ségrégation de privilège par périmètre** ; un administrateur Exchange Online sera ainsi en mesure de manipuler l’ensemble des boîtes mails. Il ne sera pas envisageable de donner des dans des organisations complexes ou encore dans des contextes réglementés. Plusieurs stratégies sont disponibles, en fonction de la maturité et de la complexité de l’organisation.

Dans le cas de petites structures, le plus simple reste d’utiliser les fonctionnalités natives :

- **Rôles RBAC** : les rôles RBAC Exchange et Intune permettent généralement d’avoir le bon niveau de granularité pour gérer les objets dans les portails natifs ;
- ***Administrative Units*** : les Unités administratives, [**enfin en GA**](https://docs.microsoft.com/fr-fr/azure/active-directory/users-groups-roles/directory-administrative-units) depuis fin Septembre, sont l’équivalent du RBAC pour Azure Active Directory. Elles prennent la forme de conteneurs dans lesquels un administrateur a la possibilité de créer ou de modifier des objets, ce qui trouve tout son sens pour les activités de support.

Dans le cas de structures plus importantes, la bonne pratique est de ne pas gérer les objets (utilisateurs, boites mail, groupes, sites SharePoint, etc.) directement dans les portails natifs. Il faut une **interface permettant de gérer l’ensemble de ces objets, tout en tenant compte des logiques métiers et du modèle d’administration cible**. Ci-dessous, trois exemples d’interface :

- **Développement maison d’un « Custom Automation Engine** » : cette interface sera décorrélée de de l’IAM et bien souvent une grosse machine à powershell / Graph API ;
- **Intégration d’un connecteur à la solution IAM** en vigueur afin de présenter une gestion complète des objets en faisant abstraction de leur hébergement direct ;
- **Investissement dans une SaaS Management Plateform** **(SMP)** : des éditeurs se sont spécialisés dans la création d’outil de gestion d’Office 365, mêlant fonctionnalités d’administration des objets, de gestion de licences ou encore de supervision sécurité et opérationnelle. Parmi ces solutions, encore peu connues, on retrouve notamment ManageEngine, CoreView et Quadrotech.

A noter : cette interface, dédiée aux équipes de supports, sera distincte d’une interface ouverte à tous les utilisateurs leurs permettant de créer centralement des utilisateurs invités, et des sites SharePoint, des Teams, etc. Concrètement, cette deuxième interface pourra être intégré aux outils de ITSM, à la SMP ou encore être développée à base de Power Apps et de Graph API.

` `*Une bonne pratique est de ne pas gérer les objets directement dans les portails natifs*
{: #myid .alert .alert-info .p-3 .mx-2 mb-3}

# 3/ 10 mesures pour sécuriser vos comptes d’administrations

En [**fonction des licences de sécurité, principalement du bundle EMS**](https://www.linkedin.com/pulse/petit-guide-pour-se-retrouver-dans-la-jungle-des-licences-joubert/), Microsoft fournit un certain nombre de contrôles pour sécuriser les comptes d’administration.

La plupart de ces mesures pourraient également être obtenues via des outils tierces.

<img src="https://thijoubert.github.io/assets/img/posts/2020-10-19_Administration_3_BestPractices.png" >


## Les mesures basiques de la sécurisation du compte d’administration

### 1. Un compte d’administrateur dédié

Un administrateur doit posséder un compte dédié à l’administration, différent du compte bureautique. Ce devrait être cloud-only dans la mesure du possible (ex : wavestone.onmicrosoft.com).

### 2. Authentification Multi-Facteur

L’authentification à plusieurs facteurs n’est plus une option aujourd’hui, et ce encore moins pour les administrateurs.

Cette mesure est disponible pour tous, pour toutes les licences :

- Via MFA pour Office 365 (également appelé MFA avec héritage par personne) qui force un challenge à chaque connexion ;
- Via les Security Defaults qui impose l’enregistrement d’un facteur additionnel pour tous les utilisateurs et impose le MFA pour les administrateurs à chaque connexion ;

Il est également important également de veiller à la [**désactivation des protocoles d’authentification héritée**](https://docs.microsoft.com/fr-fr/azure/active-directory/conditional-access/block-legacy-authentication) qui ne prennent pas en charge le MFA. Ces derniers permettraient en effet de passer outre l’authentification simple.

Il sera également pertinent de limiter les types de facteurs supplémentaires disponibles ; à quoi bon sécuriser les comptes d’administration si le second facteur est l’adresse Gmail de l’administrateur.

## Des mesures de sécurisations fortement recommandées

### 3. Compte sans licence Office 365

Sans licence, il se sera pas possible pour un administrateur d’accéder aux différents services et données de la plateforme, ou encore d’avoir une boite mail.

A noter, certains services, comme Power Apps ou Power BI, requièrent une licence pour accéder au portail d’administration. En pratique, il peut être intéressant de créer un groupe de sécurité attribuant les licences nécessaires pour les administrateurs.

### 4. Conditional Access (avec Azure AD P1)

[**L’accès conditionnel**](https://docs.microsoft.com/fr-fr/azure/active-directory/conditional-access/overview) permet d’évaluer le contexte lors de l’accès à un service Office 365, et d’autoriser en fonction l’accès. Il permet par exemple de bloquer l’accès en fonction du type de poste utilisé (géré par l’entreprise ou non), du réseau sur lequel l’utilisateur est connecté, de l’application en question ou encore de son rôle d’administration.

Dans une logique Zero Trust, il ne faudrait pas différencier le réseau interne et le réseau externe, en particulier pour les administrateurs, mais plutôt se concentrer sur l’état du poste de travail et le risque de la connexion.

### 5. Protection de mot de passe (avec Azure AD P1)

[**Aure AD Password Protection**](https://docs.microsoft.com/fr-fr/azure/active-directory/authentication/concept-password-ban-bad-on-premises) apporte des contrôles sur les mots de passe. Il sera ainsi possible d’interdire l’utilisation d’un mot de passe courant ou d’un dérivé (avec une liste prédéfinie par Microsoft ou maintenue par l’organisation).

Une bonne pratique consiste à appliquer à minima cette protection sur l’ensemble des comptes d’administration Cloud-only.

### 6. Azure AD Identity Protection (avec Azure AD P2)

[**Azure AD Identity Protection**](https://docs.microsoft.com/fr-fr/azure/active-directory/identity-protection/overview-identity-protection) permet d’ajouter une notion de risque dans l’évaluation des accès et des comportements des utilisateurs. Concrètement, il sera opportun des définir les politiques suivantes ;

- Risky users : Forcer le changement de mot de passe pour un administrateur susceptible d’être compromis (avec un risque Medium ou High) ;
- Risky sign-in : Forcer un challenge MFA lors d’un accès à risque (ex : IP anonyme ou inhabituelle).

### 7. Azure AD Privileged Identity Management (avec Azure AD P2)

[**Azure AD Privileged Identity Management**](https://docs.microsoft.com/fr-fr/azure/active-directory/privileged-identity-management/) est un service permettant de contrôler l’attribution et l’utilisation des rôles d’administration :

- Attribuer de droits « just-in-time » en donnant un rôle éligible au lieu de permanent ;
- Soumettre l’activation d’un rôle à une validation d’une tierce partie ;
- Mettre en place une date de fin de droit pour un rôle d’administration ;
- Forcer les recertifications des administrateurs.

Il sera pertinent de **distinguer les rôles dits sensibles des autres**, lors de l’implémentation.

Le suivi des administrateurs éligibles permet en bonus, de prendre conscience de l’utilisation réelle des droits d’administration et donc de nettoyer plus facilement la liste des administrateurs.

A noter, les fonctionnalités de PIM ont récemment été [**étendus aux différents groupes**](https://docs.microsoft.com/en-us/azure/active-directory/privileged-identity-management/groups-features), ce qui permet de mettre en place du « Just-in-time » pour [**des cas plus exotiques comme les Super-Users RMS / AIP**](https://techcommunity.microsoft.com/t5/microsoft-security-and/using-azure-pim-for-the-aip-super-user-feature-management/ba-p/1587690).

## Pour aller encore plus loin

### 8. Supervision des action administrateurs pour détecter les comportements anormaux**

Une fois toutes ces mesures de sécurisation en place, il ne vous reste plus qu’à implémenter de la supervision afin de détecter les non-conformités aux règles précédentes et les comportements anormaux.


### 9. Mettre en place une Privileged Access Workstation**

L’administration étant une action par définition critique. Il est nécessaire qu’elle soit réalisée dans un périmètre de confiance. La mise à disposition de [**PAW, ou poste d’administration**](https://docs.microsoft.com/fr-fr/windows-server/identity/securing-privileged-access/privileged-access-workstations), permettra d’aller dans cet objectif.

La configuration du poste d’administration devrait être simple (pas de droits d’administration local, navigation Internet restreinte, ports USB bloqués, modules PowerShell préinstallés, etc.). Mais le fait de restreindre la connexion d’un administrateur Office 365 depuis ce poste peut poser plus de soucis. Pour cela, plusieurs possibilités existent :

- Dans un contexte moderne, une réponse simple est de s’appuyer sur les outils Microsoft : définir un profil de poste d’administration dans Intune et l’assigner aux administrateurs. Avec une règle d’accès conditionnel, il est possible d’exiger un poste conforme lors de la connexion.
- Dans un modèle plus classique, il est possible de mettre en place un [**silo d’authentification**](https://docs.microsoft.com/fr-fr/windows-server/security/credentials-protection-and-management/authentication-policies-and-authentication-policy-silos) avec les administrateurs et les postes associés. On aurait ainsi un modèle se rapprochant du modèle en tiers bien connu des équipes AD.
- D’autre pistes sont également possibles, même si plus complexes : association d’un certificat et d’un reverse proxy ou encore d’un bastion.

### 10. Rester informé des bonnes pratiques et des nouveautés

On ne rappellera jamais assez qu’Office 365 étant une plateforme Cloud, elle est en évolution constante. Se tenir au courant permettra de continuer à augmenter son niveau de sécurisation au fil du temps.


# Focus sur les comptes bris de glace

Une bonne pratique est la **mise en place de comptes d’administrateur "bris de glace".** Ces comptes ont pour objectif de conserver un accès d'administration à la plateforme lors d'un incident (dans la chaîne d'authentification ou dans les configurations de sécurité). Le rôle de Global Administrator leur est donc attribué.

Ces comptes doivent évidemment être sécurisé, cependant il ne faut pas oublier leur spécificité qui consiste à les utiliser en cas d’incident. Ainsi **la sécurisation imposée à ces comptes doit rester compatible avec le caractère urgent de leur utilisation**. Ces comptes doivent donc répondre aux recommandations suivantes :

- Être des comptes cloud-only
- Pas de MFA configuré (ou du moins un MFA tiers à celui utilisé)
- Stockage du mot de passe dans un coffre auquel seulement des personnes identifiées de l’équipe sécurité ou Office 365 peuvent accéder
- Mise en place d’alertes afin de vérifier que ces comptes ne soient pas utilisés (avec alertes SMS et mail dès accès au compte).

Il est également recommandé de ne pas utiliser de convention de nommage spécifique pour ces comptes, ils ne doivent pas attirer l’œil d’un possible attaquant !



**En conclusion**

La sécurité sur Office 365 repose sur des mesures techniques de protection des comptes administrateurs, ainsi que l’implémentation d’un modèle d’administration cible, ce qui comprend une gouvernance et des processus clairs, des outils pour mettre en place cette délégation de droits, et des dispositifs pour le maintenir dans le temps.

Mais quelles que soient les mesures de protection implémentées, la **sécurité repose avant tout sur les administrateurs de la solution**. S**ensibilisation des administrateurs et contrôles seront indispensables**.

Les administrateurs doivent garder à l’esprit que leurs comptes donnent accès à des informations et des actions extrêmement sensibles : ils sont donc la cible privilégiée des pirates informatiques !

O365 étant en constante évolution, chaque nouveauté introduite par Microsoft pourra amener également son lot de problèmes de sécurité qu’il faudra étudier et prendre en compte. Profitez-en pour mettre à jour vos documentations : analyses de risques O365, configuration des services, modèle de délégation…toujours **sans oublier de permettre à vos administrateurs de se former !**

Merci à [Alexandre MAZARS](https://www.linkedin.com/in/alexandre-mazars/) et [Raymond CHAN](https://www.linkedin.com/in/raymond-chan-fr/) pour la co-rédaction. 
