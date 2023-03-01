---
title: "How to customize the branding of Teams meetings"
subtitle:
excerpt: "Thanks to the Teams meeting branding policy, companies can in particular adapt the look and feel of their meetings to align with their branding guidelines"    
tags:
  - Microsoft Teams
  - Office 365
  - PowerShell
header_img : "./assets/img/posts/2023-02-28_Teams-Meetings-Branding-Policy_2.png"
---

With the rise of remote work, companies are relying more on virtual meetings to stay connected. With the **general availability of Teams Premium**, it is now possible to customize Teams meetings based on company branding or security policies. 

Thanks to the Teams meeting branding policy, companies can in particular adapt the **look and feel of their meetings to align with their branding guidelines**. In this article, I'll walk you through everything you need to know about Teams meeting branding policies (management in the admin interface or PowerShell, appearance, limits, and licensing).

<br />
<br />

# What are the Teams meeting branding policies? 

Teams meeting branding policies is a feature introduced by Microsoft in 2022 in public preview, and in general availability since February 2023. 

These policies allow you to **configure the logo, background image, and button color for Teams meetings and Live events**. Webinars are not yet supported: the registration page experience remains in the event configuration, but the lobby and meeting should join the new experience. 

In summary, the branding policies allow you to go from this: 

<img src="https://thijoubert.github.io/assets/img/posts/2023-02-28_Teams-Meetings-Branding-Policy_1.png">

To this: 

<img src="https://thijoubert.github.io/assets/img/posts/2023-02-28_Teams-Meetings-Branding-Policy_2.png">

Of course, when we talk about Office 365 it is necessary to have a word about licensing. In this case, licensing is simple: the meeting creator must have a "**Microsoft Teams Premium**" or "**Teams Advanced Communications**" license. Other users, internal or external will see the branding whatever their licenses. 

Recently, the Teams meetings branding policies also allow for the management of meeting backgrounds that will be pre-loaded for targeted users. This implies that deploying meeting backgrounds for an entire organization via Teams will require advanced Teams licenses for all users. In this specific case, it will still be possible to do it manually.

<br />
<br />

# How to configure a Teams meeting branding policy via the UI

As for other Teams policies: a user is either in the global policy or in a custom one. Only licensed users will receive the branding policies. 

Note that these policies have different names in the UI “Customization policies”, “meetings branding policies”… 

To configure a new policy go to: <https://admin.teams.microsoft.com/policies/branding/> et cliquer sur « Add ». Ce bouton sera désactivé si l’organisation n’a pas de licence Premium. 

<img src="https://thijoubert.github.io/assets/img/posts/2023-02-28_Teams-Meetings-Branding-Policy_3.png">

After entering the name of the policy - which cannot be changed after - you can create a theme for the meetings, define custom meeting backgrounds that can be selected by the users, and define whether the user can disable the theme in the meeting options.

The theme can include the following elements: 
- Logo
- Image for the lobby
- Color for the different graphical elements 

<img src="https://thijoubert.github.io/assets/img/posts/2023-02-28_Teams-Meetings-Branding-Policy_4.png">

Once the theme is defined, it is possible to **preview the lobby with the different settings defined**. You can see that the color of the graphic elements (like the join meeting button) is slightly different from the one I had asked for. 

<img src="https://thijoubert.github.io/assets/img/posts/2023-02-28_Teams-Meetings-Branding-Policy_5.png">

All that remains is to assign the policy to the desired users. The policy takes effect within **2-3 minutes**. 

Note: the appearance of the custom policy takes about 2 to 3 seconds to appear. During this time, we see the default theme of Microsoft (black background and purple button). 
{: #myid .alert .alert-info .p-3 .mx-2 mb-3}


<br />

## Different errors in the configuration of a policy

I got several error messages while configuring a new policy: 
- We can't create your images. Try again. If you continue to have problems, contact Microsoft customer support.
- We can't get any policies right now. Try again. If you continue to have problems, contact Microsoft customer support.
- We can’t get the policy assignments. Try again. If you continue to have problems, contact Microsoft customer support.

The first error is quite annoying because I could not save a policy. Moreover, it is complicated to reproduce because for an identical configuration, the creation works, and several minutes later not.

<br />
<br />

# Configuring Teams Meeting Branding Policies with PowerShell 

For more advanced users, PowerShell enables to manage Teams meeting branding policy. As always, this will be mandatory for large organizations that want to configure multiple policies for their different entities. I have seen that this allows greater customization over the branding elements. 

The management of Teams meetings branding policies will PowerShell is not yet documented. However, ChatGPT kindly assisted me in these steps. I am not aware of any support for this feature by Graph API.
{: #myid .alert .alert-info .p-3 .mx-2 mb-3}


<br />

## Creation of a new policy in PowerShell 

**1. Creation of new theme**

The parameters available to configure a new theme (New-CsTeamsMeetingBrandingTheme) are:
- **Identity**: Specifies the name of the branding theme.
- **DisplayName**: Specifies the display name of the branding theme.
- **LogoImageLightUri**: Specifies the URL for the light version of the logo image.
- **LogoImageDarkUri**: Specifies the URL for the dark version of the logo image.
- **BackgroundImageLightUri**: Specifies the URL for the light version of the background image.
- **BackgroundImageDarkUri**: Specifies the URL for the dark version of the background image.
- **BrandAccentColor**: Specifies the brand accent color in hexadecimal format.
- **Enabled:** Specifies whether the branding theme is enabled or not.

Unlike the GUI, PowerShell requires URLs to fetch images.  

```powershell
$imageURL = "https://raw.githubusercontent.com/thijoubert/test/main/2020-08-11\_La%20Garde\_004.jpg"
$LogoURL  = "https://raw.githubusercontent.com/thijoubert/test/main/Icone\_wikipedia\_hobbit\_m.svg%20(1).png"
$Color = "#B5DFB1"

$MyBrandingTheme\_PS = New-CsTeamsMeetingBrandingTheme -Identity "MyBrandingTheme\_PS" -DisplayName "MyBrandingTheme\_PS" -LogoImageLightUri $LogoURL -LogoImageDarkUri $LogoURL -BackgroundImageLightUri $imageURL -BackgroundImageDarkUri  $imageURL -BrandAccentColor $Color -Enabled $true
```

Only “Identity” and “Enabled” parameters are mandatory. If they are absent from the Theme, the creation of the policy will fail. 

**2. Creation of new branding policy**

Once the theme is created, you can create a new branding policy:

```powershell
New-CsTeamsMeetingBrandingPolicy -Identity "MyBrandingPolicy\_PS" -MeetingBrandingThemes $MyBrandingTheme\_PS -DefaultTheme "MyBrandingTheme\_PS" -EnableMeetingOptionsThemeOverride $false 
```

The theme could be created after the policy and added after with Set-CsTeamsMeetingBrandingPolicy.

<img src="https://thijoubert.github.io/assets/img/posts/2023-02-28_Teams-Meetings-Branding-Policy_6.png">

Here is the result in the admin UI: 

<img src="https://thijoubert.github.io/assets/img/posts/2023-02-28_Teams-Meetings-Branding-Policy_7.png">

And here is the result for my user: 

<img src="https://thijoubert.github.io/assets/img/posts/2023-02-28_Teams-Meetings-Branding-Policy_8.png">

Note that policies managed in PowerShell cannot be viewed, modified, or deleted in the UI at this time.

<img src="https://thijoubert.github.io/assets/img/posts/2023-02-28_Teams-Meetings-Branding-Policy_9.png">


**3. Addition of other themes**

By manipulating the policy settings, I saw also that I should be able to add several themes to the same policy. The option "EnableMeetingOptionsThemeOverride" suggests that the user could choose to keep the default theme or one of the other secondary themes.

<img src="https://thijoubert.github.io/assets/img/posts/2023-02-28_Teams-Meetings-Branding-Policy_10.png">

The policy is well-modified. But I could not see how to use the different themes. I would have expected the meeting organizer to have the ability to override the default theme in the meeting options. But I don't see anything so far in the interface. This is surely a feature that will come later.

<br />
<br />

# In conclusion 

*From my point of view, the customization of the meeting themes will be mostly useful for Live Events or webinars with external people (can't wait for this to be released).* 

*The few errors I encountered show that Microsoft's copy is not yet completely dry, but the feature is largely usable as is for all organizations that can afford Teams Premium or Advanced Communications (reminder: Teams has a 30% discount on the public price before June 30, 2023). It will be hard to justify the $10 per user month just to customize the lobby, but the other compliance features and intelligent recap are more than promising.*