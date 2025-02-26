---
title: "Prepping for and Staging macOS 15.4 Restrictions for Apple Intelligence"
date: 2025-02-24
tags: [jamf, macos, configuration profiles, Apple Intelligence, kandji, mosyle]
categories: [Mac Management]
pin: false
---
![2025-02-24-Sequoia-15-4-Restrictions-Banner](/assets/img/postimages/2025-02-24-Sequoia-15-4-Restrictions-Banner.png)

In my previous post, [Creating Custom Configuration Profiles for Jamf Pro Restrictions Payload](https://tonyyo11.github.io/posts/Creating-Custom-Configuration-Profiles-Jamf-Restrictions/), we looked at multiple methods for creating and deploying customized configuration profiles within Jamf Pro in order to prep and account for the many changes introduced in the MDM Framework for managing Apple Intelligence in macOS Sequoia 15.x.

Apple has recently released macOS 15.4 Beta for Developers and Appleseed for IT.

Now, we must begin to prep our Jamf Pro servers with new keys introduced in macOS 15.4 so that organizations can confirm that the keys are functional as intended and to prepare for future deployment once macOS 15.4 is generally available.

As I mentioned in my last post, if you picked **Method One: Continue with Jamf's Restriction Payload**, you must consider deploying a custom profile at this point, as Jamf has not introduced the keys introduced in macOS 15.4 Beta. Otherwise, Jamf Cloud customers can wait for a future update to the Jamf Pro server for the new keys to be introduced in the Restrictions Payload. Organizations deploying Jamf Pro on-premise must also wait for a new version of the Jamf Pro server and properly plan their upgrade workflows.

I personally recommend that organizations use **Method Two: Migrate to Utilizing Custom Configuration Profiles** and "stack" profiles for maximum flexibility and future planning. Stacking or using smaller, targeted profiles (e.g., one for Apple Intelligence restrictions, another for Security & Privacy baseline, etc.) makes the environment more flexible when Apple or Jamf Pro issues updates. It also simplifies troubleshooting by letting you pinpoint exactly which profile or setting might be causing conflicts rather than sorting through one massive restrictions profile.
So, let's use this method to prepare our server for macOS 15.4.

### Creating a macOS 15.4 Smart Group

First, we must create a new Jamf Pro Smart Group. We can name the group "macOS 15.4 and Later" with a Criteria of Operating System GREATER THAN OR EQUAL 15.4.
![jamf_15_4_Group_Criteria](/assets/img/postimages/jamf_15_4_Group_Criteria.png)

Once saved, we can create a new Configuration Profile. A simple yet descriptive name such as "macOS 15.4 Restrictions" should be more than sufficient for the name. Include any description for the profile based on your documentation and organizational requirements. 

### Adding new Management Keys to a Custom Profile

I prefer to use JSON Schemas when building configuration profiles. So, under "Application & Custom Settings," we will use the External Applications and add a Custom Schema to include the newly introduced keys. 
The following code contains just the macOS Sequoia 15.4 Restrictions:
<script src="https://gist.github.com/tonyyo11/aa526b49aaa5f28d72e12838391029c9.js"></script>

Once the schema has been added and saved, and the Preference Domain has been set to `com.apple.applicationaccess`, we can set the desired state for each key. In my current assignment, we must disable all Apple Intelligence functionality, so every key will be set to `FALSE`. 
![jamf_15_4_Profile_Schema](/assets/img/postimages/jamf_15_4_Profile_Schema.png)

It is important to note that one key, `allowNotesTranscription,` is not related to Apple Intelligence. This key allows/disallows the use of Notes Transcription within Apple Notes. A separate key, `allowNotesTranscriptionSummary,` was introduced in macOS 15.3. This key allows Apple Intelligence to summarize those Notes Transcriptions, and because it is related to Apple Intelligence, it would surely fall under our mandate to disable it. 

Scope your newly created configuration profile to your macOS 15.4 and Later smart group created early, and save your changes.
![jamf_15_4_Config_Scope](/assets/img/postimages/jamf_15_4_Config_Scope.png)

Because we’re still in a Beta release, it's essential to monitor official resources. Apple regularly updates its Developer Documentation and AppleSeed for IT release notes, which can highlight changes or new configurations introduced in macOS 15.4. It’s a good idea to review these resources whenever you see an updated Beta build or patch so you can confirm that you don’t need to account for additional keys or changes.

With this, we are now ready to upgrade test systems to macOS 15.4 Beta to begin testing the enforcement of these new keys, and our Jamf Pro Server is prepped and staged for when macOS 15.4 is generally available. No additional action should be required on the Mac Admin managing the Jamf Pro Server, as the configuration profile will automatically deploy as systems update/upgrade to the specific version of macOS 15.4. Be sure to document and validate this new profile through any internal change management processes within your organization.

### Security, Security, Security
Disabling or restricting Apple Intelligence features isn’t just about user experience—it’s also a security and privacy consideration in many regulated or high-security environments. Whether you’re adhering to NIST, CIS, or another compliance framework, limiting advanced AI-driven features can reduce the “attack surface” and ensure that user data remains protected within an approved environment. As Apple Intelligence evolves, be prepared to review your threat model and update your restrictions accordingly so you maintain compliance across your device fleet.

### Few new Mac Admins managing Jamf Pro
#### FAQ

1. What if Jamf Cloud doesn’t have these keys yet?
	- Continue using a custom configuration profile. Jamf typically adds new OS keys in a later release. Once they become native, you can migrate them to your main Restrictions payload. Or you can migrate away from using Jamf's Native Restrictions payload as detailed in previous posts.
2. How can I confirm the keys were correctly applied?
	- Check profiles via Terminal on the target Mac (`profiles -P` command), by going to System Settings > General > Device Management and looking through the list of installed profiles, or review the MDM logs in Console to ensure the profile is active and the keys show as “true/false” as expected.
3. Is there a risk to deploying Beta restrictions in production?
	- Absolutely—Beta means things might change or break. Restrict these new keys to test groups, pilot Macs, or lab devices to avoid widespread disruptions.

**Checklist**
- Create a pilot Smart Group (e.g., “macOS 15.4 Beta Testing”).
- Audit existing profiles to confirm no overlap or duplication.
- Review AppleSeed for IT notes to see if anything changed in the latest Beta build.
- Push to a small set of devices and verify that your restrictions are enforced.
- Document all findings and share with relevant teams.

### I manage Macs, but I don't use Jamf, what should I do?

While this guide focuses on Jamf Pro, the exact same keys and logic typically apply to other MDM solutions as well:
- Microsoft Intune: You can upload these same custom profiles as .mobileconfig files to Intune, with a scope that specifically targets macOS 15.4 devices.
	- Intune has documentation on custom configuration profiles [here](https://learn.microsoft.com/en-us/mem/intune/configuration/custom-settings-configure) and [here.](https://learn.microsoft.com/en-us/mem/intune/configuration/custom-settings-macos)
- Kandji and Mosyle: Both support custom configurations, so you’d follow a similar approach—import the custom schema, define the desired keys, and scope it to 15.4 devices.
	- Kandji has documentation related to custom profiles [here.](https://support.kandji.io/kb/custom-profiles-overview)

---
💬 Have questions? Find me on the **Mac Admins Slack**: @tonyyo11\
![slack_Profile](/assets/img/postimages/slack_Profile.png)
---

## **Disclaimer**

The views expressed in this blog post are my own and do not necessarily reflect those of my employer, past or present. All information provided is for informational purposes only, and it is up to the reader to test and validate any suggestions or claims made within this post. No warranty of correctness is given, and I disclaim any liability for decisions made based on this information. 

