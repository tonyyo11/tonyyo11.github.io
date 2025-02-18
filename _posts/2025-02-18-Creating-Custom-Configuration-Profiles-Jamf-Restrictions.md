---
title: "Creating Custom Configuration Profiles for Jamf Pro Restrictions Payload"
date: 2025-02-18
tags: [jamf, macos, configuration profiles]
categories: [Mac Management]
pin: false
---
In my previous post, [Jamf Configuration Profiles Analysis of Restrictions Payload Implementation](https://tonyyo11.github.io/posts/Jamf-Configuration-Profiles-Analysis-of-Restrictions-Payload-Implementation/), I discussed challenges many organizations face in managing Apple Intelligence features on macOS using Jamf Pro’s Restrictions Payload. 

That post highlighted two recommendations:

1. **Deploy version-specific profiles using Jamf Pro’s native Restrictions Payload.**
2. **Use "stackable" custom configuration profiles, also version-specific.**

For further reading on custom configurations within Jamf Pro, Dr. Emily Kausalik-Whittle, Ph.D., has excellent blog posts:

- [Quick look: using Application & Custom Settings to restrict 2024 fall release features with Jamf Pro](https://www.modtitan.com/2024/09/quick-look-using-application-custom.html)
- [Using the Applications & Custom Settings payload in Jamf Pro to manage the ChatGPT integration with Apple Intelligence](https://www.modtitan.com/2024/12/using-applications-custom-settings.html)

Now, let’s examine both strategies in detail.

---

## Method One: Continue with Jamf's Restriction Payload.

When I state Jamf's Native Restriction Payload, I specifically mean going to Jamf > Computers > Configuration Profiles and either within a current profile, or in a New Profile, going to the "Restrictions" option.
![jamf_RestrictionsPayload_Empty](/assets/img/postimages/jamf_RestrictionsPayload_Empty.png)

When I state using a custom configuration profile, I mean using the Application & Custom Settings Payload and either using the "External Applications" or "Upload" options.
![jamf_ApplicationCustomSettings](/assets/img/postimages/jamf_ApplicationCustomSettings.png)

We start with editing our current Restrictions Profile, or creating a new one.
![jamf_CorpRestricttions_14](/assets/img/postimages/jamf_CorpRestrictions_14.png)
Within the Restrictions Payload, configure all settings based on organizational requirements. Since Jamf Pro does not have an *include* switch, you **must** make a decision for every preference within this payload. If you are creating a brand new profile with the latest version of Jamf Pro (currently 11.3.1 as of this writing), note that Jamf's Restriction payload includes Apple Intelligence management keys up to macOS 15.2. macOS 15.3 introduced Allowed External Intelligence Workspace IDs `allowedExternalIntelligenceWorkspaceIDs`, and Allow Notes Transcription Summary `allowNotesTranscriptionSummary` as management keys. As Jamf Pro releases new updates, Jamf will likely add these new keys to the Restrictions Payload. Organizations must note, as I call out in my initial blog post under [Server Upgrade Implementation Gaps](https://tonyyo11.github.io/posts/Jamf-Configuration-Profiles-Analysis-of-Restrictions-Payload-Implementation/#3-server-upgrade-implementation-gaps), that when a Jamf Pro server finishes an upgrade and new Restrictions keys are added, systems that are already enrolled and have the profile installed do not automatically receive the new keys after the server upgrade or when the end-user system performs its own system updates. Instead, the configuration profile must be manually or systematically redeployed to the end-user system - and *only* redeployed after the system has also updated/upgraded to macOS 15.3 or later. Newly enrolled systems to Jamf Pro will automatically receive all available keys as they download the updated Restrictions profile for the very first time.

### **Using Smart Groups for Scoped Deployment**

To manage version-specific restrictions:

1. **Create a Smart Group** for devices running **macOS 14 and earlier**.
2. Deploy the **"Corp Restrictions - macOS 14 and Earlier"** profile to this group.
3. **Clone the profile**, rename it (e.g., **"Corp Restrictions - macOS 15 and Later"**), and scope it to **macOS 15+ devices**.
4. Regularly redeploy the macOS 15+ profile as new keys are introduced.
5. Continuously evaluate and adjust Smart Group criteria based on evolving organizational needs.

## Method Two: Migrate to Utilizing Custom Configuration Profiles.

Instead of using Jamf’s Restrictions Payload, organizations can create **stackable** profiles that contain only necessary keys per minor macOS version.
[IMAGE]

It is not recommended to mix and match the Jamf native Restrictions Profile and Custom Configuration Profiles since Jamf's Restriction Profile deploys all of the keys possible, which may conflict with what you, as the organization, deploy in your custom profiles. As Jamf adds new keys to the Restrictions Payload, the default setting for that key may be set to `FALSE` while your custom configuration profile is set to `TRUE` which will cause macOS to set the key(s) as `undefined.`

### **Why Stackable Profiles?**

- Avoids **constant redeployment** of a dedicated macOS 15 Restrictions Profile.
- Prevents **conflicts** between Jamf’s native Restrictions Payload and custom profiles.
- Provides **granular control** over individual keys.
- Enhances maintainability by separating concerns across multiple configuration profiles.

### **Configuration Domains to Consider**

In practice, an organization would have a baseline Restrictions Profile and any additional profiles to account for the other management domains included in Jamf's Restrictions payload.
Some of those domains included `com.apple.systempreferences`, `com.apple.MCX`, `com.apple.applicationaccess.new`, `com.apple.coremediaio.support`, `com.apple.DiscRecording`, `com.apple.appstore`, `com.apple.dashboard`, `com.apple.finder`, `com.apple.gamed`, `com.apple.applicationaccess`, `com.apple.preferences.users`, `com.apple.systemuiserver`, `com.apple.desktop`, `com.apple.fileproviderd`, `com.apple.ShareKitHelper`.
From this list, you can see just how far-reaching Jamf's Restriction Payload extends beyond standard restrictions housed within `com.apple.applicationaccess`. Organizations should verify whether they need to control these domains separately and determine appropriate settings for each. Organizations may find that they do not care to set settings for some of the other domains, or if they are utilizing the [macOS Security Compliance Project](https://github.com/usnistgov/macos_security) to set standards/baselines like the Center for Internet Security (CIS) macOS Benchmark or NIST's standards or DISA STIGs may already be configuring these domains. 

### **Uploading PLIST Files with iMazing Profile Editor**

For configuring your baseline Restrictions configuration profile, one tool that is highly recommended is [iMazing Profile Editor](https://imazing.com/profile-editor).
![iMazing_Restrictions](/assets/img/postimages/iMazing_Restrictions.png)

This tool will allow you to build configuration profiles in a way that only includes the keys you wish to set. You can customize the application to only show preferences that were introduced before/after a given version of macOS. With iMazing Profile Editor, the profiles created within the application can either be signed (read more [here](https://imazing.com/guides/how-to-sign-apple-configuration-profiles) and [here](https://learn.jamf.com/en-US/bundle/technical-articles/page/Creating_a_Signing_Certificate_Using_Jamf_Pros_Built-in_CA_to_Use_for_Signing_Configuration_Profiles_and_Packages.html).) or you can export the payloads as PLIST in order to upload them to Jamf Pro. 

1. Use [iMazing Profile Editor](https://imazing.com/profile-editor) to create a configuration profile.
2. Export the configuration profile as a **PLIST file**.
3. In Jamf Pro:
   - Go to **Application & Custom Settings** payload.
   - Select **Upload** > **Add** > **Upload PLIST File**.
   - Enter the **Preference Domain** (excluding `.plist` extension).
4. Assign the profile to the correct Smart Group and test before full deployment.

Your end result will look something similar to this.
![jamf_RestrictionsPLIST_Upload](/assets/img/postimages/jamf_RestrictionsPLIST_Upload.png)

*Repeat this process for any other preference domains as needed.*

### **Using External Applications with Custom JSON Schema**

Some organizations may prefer to utilize JSON Schemas to build their configuration profiles. For this, I highly recommend exploring [Jamf-Custom-Profile-Schemas/ProfileManifestMirror](https://github.com/Jamf-Custom-Profile-Schemas/ProfileManifestsMirror). 
This method utilizes Jamf's Application & Custom Settings > External Applications to build profiles. Read more [here](https://www.elliotjordan.com/posts/profilemanifestsmirror/) and [here](https://developer.jamf.com/developer-guide/docs/application-and-custom-settings). Additionally, there are great JNUC sessions about Custom JSON Schemas: 
- [Simplifying application management: using custom schemas in Jamf Pro - JNUC 2021](https://www.youtube.com/watch?v=3ZdFzWBTkjg).
- [How to Create Jamf Manifests for Custom Configuration Profiles - JNUC 2023](https://www.youtube.com/watch?v=EYG-w3_Do3s).

I prefer using a custom JSON schema to create configuration profiles within Jamf Pro. This helps me document my setup further within Jamf Pro. Any auditor or junior engineer learning your setup can easily view the JSON Schema setup and see additional text explaining what a given key is doing and why it may be set to the current setting or what the default typically is. For more advanced users, Jamf Pro still allows you to view the XML/PLIST version of the profile and cut through all of the additional text. 
You can view my [GitHub Repository](https://github.com/tonyyo11/MacAdministration/blob/main/JSON%20Schemas/macOS-Sequoia-Restricions-Custom-Schema.json) for a JSON Schema for macOS 15 Sequoia Restrictions, where each key is documented with the minimum version of Sequoia that supports the key. This can be used to selectively build your stackable profiles. 

Once the baseline Restrictions Profile is built, now organizations can stack on top of this profile. Many considerations should go into the best way to stack profiles, all based on how many devices are still on earlier versions of macOS, are there any software update deferrals in place that prevent systems from updating or upgrading directly for the latest version of macOS Sequoia (_currently 15.3.1_), and more. You may opt to create a stacked profile beginning with macOS 15.0; then the following stack is a profile for keys that are available in 15.1 and later, then another profile for 15.2 and later. Another option is to have a profile that includes all of the current macOS 15.0-15.3 keys if your systems are all going to upgrade to Sequoia 15.3, and none will go to an earlier version of Sequoia. The goal is to build a workflow where you are not constantly having to redeploy profiles. 

## **Comparison Table: Method One vs. Method Two**

| Feature            | Method One: Jamf Restrictions Payload | Method Two: Custom Configuration Profiles |
| ------------------ | ------------------------------------- | ----------------------------------------- |
| Ease of Deployment | Easier, uses Jamf’s built-in UI       | Requires more setup & maintenance         |
| Future-Proofing    | Must redeploy profiles for new keys   | Stackable profiles reduce redeployment    |
| Conflicts          | Can conflict with future Jamf updates | Prevents unexpected restrictions          |
| Granular Control   | Limited, all settings must be defined | Allows selective key inclusion            |
| Maintainability    | Easier but less flexible              | More complex but scalable                 |



As of the time of this writing, Apple has not released a beta version of macOS 15.4. So any new keys that may or may not be introduced in the next version are yet to be known. Per Dr. K "To ensure you are ahead of new MDM controls and features available for Apple platforms, make sure to sign up for [AppleSeed for IT](https://beta.apple.com/for-it) (and for Jamf customers, the Jamf beta program via Jamf Account)." Additionally, organizations can track Apple's [Github Page](https://github.com/apple/device-management) for new branches that indicate betas are live, and new MDM keys have been introduced. 

As Apple releases betas and new MDM management keys, organizations can use stackable configuration profiles to pre-plan and pre-stage configuration profiles for macOS 15.4 and later within their servers. Once 15.4 is officially released to the general public, those profiles will automatically deploy to systems. No planning is needed to redeploy Jamf Pro's native Restriction Payload to systems as they upgrade. This process is repeatable for future versions of Sequoia 15.5, 15.6, and so on.

## **Final Thoughts**

Organizations should evaluate which method best suits their **security policies, compliance requirements, and operational efficiency**.

- Want a **quick, centralized** solution? → Use **Method One**.
- Need **long-term flexibility** and reduced redeployment? → Use **Method Two**.
- Hybrid approaches may provide the best balance, depending on organizational needs.

I hope this helps organizations begin to consider the possibilities available to them to cut down on much of the manual labor that some have found themselves in to ensure security policy is being enforced across their entire platform. Continue to upvote feature requests and provide feedback to Jamf directly on the need to update their restrictions payload and have a consistent experience across all of their payloads on both macOS and iOS/iPadOS. 

💬 Have questions? Find me on the **Mac Admins Slack**: @tonyyo11\
![slack_Profile](/assets/img/postimages/slack_Profile.png)
---

## **Disclaimer**

The views expressed in this blog post are my own and do not necessarily reflect those of my employer, past or present. All information provided is for informational purposes only, and it is up to the reader to test and validate any suggestions or claims made within this post. No warranty of correctness is given, and I disclaim any liability for decisions made based on this information. ;) 

