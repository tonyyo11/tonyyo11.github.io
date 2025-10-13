---
title: "Jamf Pro 11.19 Updates and Enhancements"
date: 2025-07-31 11:30:00 -0400
description: "Jamf Pro 11.19 Update: What’s New and Interesting for Jamf Admins"
categories: [Mac Management]
tags: [macOS, iOS, iPadOS, Jamf Pro, Jamf]
image:
  path: /assets/img/postimages/JamfPro1119Banner.png
  lqip: /assets/img/postimages/JamfPro1119Banner.png
  alt: Jamf Pro 11.19 Banner Image
---

The recent release Jamf Pro 11.19 introduces two features that give admins more guardrails around large-scale changes. A **very** welcomed new feature set in the recent release - Impact Alert Notifications, and Descriptions for Computer Groups. These features help you avoid accidental mass-scoping errors and document the purpose of each group right in the UI. 

See the full release notes [here](https://learn.jamf.com/en-US/bundle/jamf-pro-release-notes-current/page/New_Features_and_Enhancements.html).

## **Impact Alert Notifications for Scopeable Objects**

Taken directly from the Jamf Pro Release Notes:
*Impact alert notifications provide enhanced visibility and control when modifying group configurations. This feature helps prevent unintended large-scale changes by giving administrators clear insight into how their modifications will affect devices and existing group configurations across their organization. When creating or editing any group type (i.e., smart groups, static groups, classes), a confirmation modal displays the following:*

1. *Total number of affected devices*
2. *Number of devices being added to scope*
3. *Number of devices being removed from scope*

*Administrators can enable this feature in **Settings** > **System** > **Impact alert notifications**. An additional option allows administrators to require users to type "Commit" before saving group changes, creating an additional barrier to accidental changes.*

![Jamf Pro 11.19 Impact Alert Settings](/assets/img/postimages/JamfPro_1119_ImpactAlert.png)

What does this functionality look like in practice? Well, when editing a group that may impact any number of systems, Jamf Admins will now be able to see the scale of impact of any given change, and be required to verify the change before it is implemented. 

![Jamf Pro 11.19 Banner Commit Required](/assets/img/postimages/JamfPro_1119_ImpactCommit.png)

## New and Current Group Description Fields

*A **Description** field has been added to all smart and static computer, mobile device, and user groups, including existing groups.* 

This new feature adds a free-text field to every group for documenting purposes, owner, scope logic, etc.

I believe this will become extremely helpful for those who document **within** Jamf Pro directly. It will help make audits far easier, facilitate easier handoffs of Jamf Pro Servers when a new admin may be coming in, and generally help reduce “mystery groups”. I’d encourage any admin to spend ten or twenty minutes reviewing their top five smart/static groups and filling in the new description fields.

![Jamf Pro 11.19 Adds the ability to set descriptions for Groups](/assets/img/postimages/JamfPro_1119_GroupDescription.png)

With two minor features, we get a significant functionality impact. Now, Jamf Admins can sit back and enjoy fewer surprises due to accidental scoping changes and a far more robustly documented Jamf Pro Server.

Will you be enabling Impact Alerts? And what groups will you be adding some extra clarity to with a new description? I’ve immediately enabled the functionality. No more learning the hard way for new engineers and support staff!.

---

## Mac Tip of the Day

Did you know the original Mac Finder icon (the little smiling face) is called the “Happy Mac”? It’s been greeting users since 1984. ![Happy Mac Icon](/assets/img/postimages/happymac_og.webp){: width="627" height="420" .w-50 .right}
