---
title: "Jamf Pro 11.22: DDM Software Update Settings, Blueprints, and Deprecations"
date: 2025-10-28 10:50:00 -0400
description: "Risks, timelines, and a recommended path from legacy profiles to declarative management before the 2026 cutoff following Jamf Pro v.11.22.0"
categories: [Mac Management]
tags: [Jamf, Jamf Pro, MDM, DDM, macOS, Software Updates, super]
image:
  path: /assets/img/postimages/Jamf_Pro_11.22_Softwre_Update_Changes_You_Need_to_Plan_For.png
  lqip: /assets/img/postimages/Jamf_Pro_11.22_Softwre_Update_Changes_You_Need_to_Plan_For.png
  alt: Jamf Pro 11.22 DDM Software Updates Banner. Illustration by Esma melike Sezer on Unsplash
---

Jamf has released an update to [Jamf Pro v.11](https://community.jamf.com/release-announcements-177/jamf-pro-11-22-now-available-56595).22.0, which includes a few new features.

Reviewing the [release notes](https://learn.jamf.com/en-US/bundle/jamf-pro-release-notes-current/page/New_Features_and_Enhancements.html), we see the following within some of the new functionality:

1. MDM Server Migration with App Preservation for iOS and iPadOS Devices
    1. You can migrate iOS and iPadOS devices from another MDM server to the Jamf Pro server while preserving installed apps and data.
    2. Mobile device PreStage enrollments include a new setting to install apps before Setup Assistant. The settings serve two purposes:
        1. Installs apps before Setup Assistant displays for standard PreStage enrollments with Jamf Pro
        2. Installs apps and associated data before the Setup Assistant displays during MDM migration to Jamf Pro

Under [deprecations](https://learn.jamf.com/en-US/bundle/jamf-pro-release-notes-current/page/Deprecations_and_Removals.html), though, the primary purpose for this post is the following callout:

<aside>

**Support for MDM software updates via remote command**

The ability to deploy software updates via remote command using the **Management** tab in an individual device record or via mass action will be removed in **late 2026.** Managed software updates will remain functional.

</aside>

This deprecation is in line with Apple’s [announced deprecations](https://support.apple.com/en-us/124963#:~:text=Software%20update%20management%20using%20mobile%20device%20management%20commands%2C%20restrictions%2C%20the%20com.apple.SoftwareUpdate%20payload%2C%20and%20queries%20is%20deprecated%20and%20will%20be%20removed%20next%20year.%20Going%20forward%2C%20software%20updates%20can%20be%20managed%20and%20enforced%20using%20only%20declarative%20software%20update%20management) around software updates in macOS and iOS/iPadOS 26:

> Software update management using mobile device management commands, restrictions, the `com.apple.SoftwareUpdate` payload, and queries is deprecated and will be removed next year. Going forward, software updates can be managed and enforced using only declarative software update management.
> 

While no action may be required at this moment, organizations should begin planning or migrating to non-deprecated workflows. 

What does that look like, though?

## Scenario: Using super to deploy updates with the Jamf Pro API

If using `super` with the [Legacy Managed Software Updates API](https://github.com/Macjutsu/super/wiki/Apple-Silicon-Jamf-Pro-API-Credentials#jamf-pro-legacy-managed-software-updates-privileges), aka you’ve never enabled “use the new software updates feature as seen in the image below, you will want to consider enabling the functionality and updating the API permissions. As called out in the current [change log](https://github.com/Macjutsu/super/blob/main/CHANGELOG.md#:~:text=The%20Jamf%20Pro%20new%20Managed%20Software%20Updates%20feature%20remains%20unreliable%20if%20the%20workflow%20target%20is%20not%20the%20latest%20minor%20update%20or%20major%20upgrade.%20In%20the%20mean%20time%2C%20the%20legacy%20Jamf%20Pro%20software%20update%20API%20remains%20stable%20(although%20deprecated)%20and%20local%20authentication%20is%20always%20the%20most%20reliable) for super, *“The [Jamf Pro new Managed Software Updates feature](https://learn.jamf.com/en-US/bundle/jamf-pro-documentation-current/page/Updating_macOS_Groups_Using_Beta_Managed_Software_Updates.html) remains unreliable if the workflow target is not the latest minor update or major upgrade. In the meantime, the legacy Jamf Pro software update API remains stable (although deprecated), and local authentication is always the most reliable.”*

![Prompt to Enable New Jamf Software Updates Feature](/assets/img/postimages/LegacySoftwareUpdatesEnabled.png)

An alternative method, if one does not wish to enable the new software update feature, is to migrate to local authentication.

## Scenario: Deploying Software Update and Deferral Configuration Profiles

Many organizations have configuration profiles that affect both `com.apple.SoftwareUpdate` and `com.apple.applicationaccess` to configure software update settings and deferrals. An example profile is pictured below for when using the Jamf Pro UI to do so:

![Configuring a Legacy MDM Configuration Profile for Software Updates and Deferrals](/assets/img/postimages/SoftwareUpdatesLegacyProfile01.png)

![Configuring a Legacy MDM Configuration Profile for Software Updates and Deferrals](/assets/img/postimages/SoftwareUpdatesLegacyProfile02.png)

![Configuring a Legacy MDM Configuration Profile for Software Updates and Deferrals](/assets/img/postimages/SoftwareUpdatesLegacyProfile03.png)

This configuration profile covers software updates and deferrals. All settings are deprecated and to be removed *either* in a future update to macOS Tahoe 26 or macOS 27, as well as iOS 26 or iOS 27.

### What should you do? Transition to Jamf Blueprints now.

To replicate this profile in a supported manner, you’ll want to take advantage of Declarative Device Management’s (DDM) declarative configuration, and not legacy profile payloads. The DDM Configuration Domain is `com.apple.configuration.softwareupdate.settings`.

{%
  include embed/video.html
  src='/assets/vid/Blueprint_SoftwareUpdate_Deferrals.mov'
  types='mov'
  title='Configuring Jamf Blueprint for Software Update and Deferrals'
  autoplay=true
  loop=true
  muted=true
%}

## Scenario: Attempting to send software updates via a Computer Record or Mass Action in Jamf Pro while New Software Updates are Enabled.

Many organizations already have Jamf’s New Software Updates featured enabled. So when attempting to send Mass Action Commands, it has already been disabled for some time.

Attempting to manually send commands to update macOS via Jamf Pro are no longer viable. 

![Attempting to send a Mass Action to a single system while New Software Updates Feature is enabled](/assets/img/postimages/JamfPro_Demo_Single_SU.png)

Navigating to a single system, selecting the Management tab, and then clicking “Download and Install Updates” displays a pop-up message directing you to the Software Updates section and to enable the functionality if applicable.

Trying to take a Mass Action against a group of systems will prevent you from progressing when selecting install updates.

![Attempting to send a Mass Action while New Software Updates Feature is enabled](/assets/img/postimages/JamfPro_Demo_Mass_Action_Updates.png)

Notice the **Next** button is non-selectable.

## Scenario: Using Jamf's new Software Updates functionality and are Unhappy with Reliability

At JNUC 2025, Jamf announced enhanced functionality in Blueprints, which they are marketing as “set it and forget it updates.” You would configure this within the Software Update blueprint itself, which alleviates the need to go to Computers > Software Update and deploy a plan. Instead, you’d set a maximum number of days before software updates must be applied. Watch the JNUC keynote to see how to configure this. This feature is in beta. 

{% include embed/youtube.html id='ij4kLJsGAe8' %}

In my conversations around JNUC, there was an unconfirmed rumor that the entire software update section (i.e, Computers > Software Update or Devices > Software Update) will ultimately become deprecated and removed in favor of having everything set within Jamf Blueprints, like this new “set it and forget it” declaration and configuration. I would assume this will be the ultimate outcome of the late 2026 removal. So, it might not be that far off down the road, but if true, it’s always better to be ahead of the curve.
