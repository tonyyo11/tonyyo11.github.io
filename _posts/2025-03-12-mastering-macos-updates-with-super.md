---

title: "Mastering macOS Updates with Super 5.1.0: New Features and Real-World Deployment Strategies"
date: 2025-03-12
tags: [macOS, super, GitHub, superman, open-source, macOS Updates, softwareupdate]
categories: [Mac Administrator]
pin: false
---
![Mastering macOS Updates Banner Image](/assets/img/postimages/masteringmacOSUpdatesBanner.png)
## What’s New in Super 5.1.0 Beta and How Mac Admins Can Implement It for Smarter Update Management

S.U.P.E.R.M.A.N is a prominent tool in the Mac Admin community. It provides flexible options to encourage or enforce macOS updates (both minor patches and major OS upgrades) with custom prompts, deferrals, scheduling, and automation. Version 5.0 released in October 2024, and now Kevin White, aka Macjutsu on GitHub, has unveiled the beta version of its upcoming 5.1.0 release. This update introduces several noteworthy features and enhancements designed to streamline device management and improve workflow efficiency.

## Key Highlights of Version 5.0.0

- **Support for macOS 15 Sequoia**: Ensures compatibility with Apple's latest macOS version, allowing seamless device management.
- **Scheduled Installation Workflows**: Enables precise scheduling of macOS updates, Jamf Pro policies, and system restarts.
- **Active Schedule Workflows**: Introduces maintenance windows for controlled update deployments.
- **Integration with MacAdmin's SOFA**: Aligns update schedules with macOS release dates for better compliance.
- **Enhanced Software Update Discovery**: Improves update and upgrade detection, enhancing reliability.

Version 5.1.0 continues on this path by introducing a few new keys. Let’s look at the [Changelog](https://github.com/Macjutsu/super/releases/tag/v5.1.0-beta):

## **Specific Changes (5.1.0-beta1)**

- New `-install-safari-update-without-restarting` option targets Safari updates for installation without restarting the system. This is a counter-option to the existing `-install-non-system-updates-without-restarting` option that targets all non-system updates for installation without restarting the system. This option is also unique in that if Safari is not active, then the workflow installs the Safari update automatically with no user interaction.
- New `-display-accessory-safari-update-file` option to specify a custom display accessory when the workflow target is a Safari update, and Safari is actively in use.
- New `-install-prioritize-non-restart-updates` option works with either the `-install-safari-update-without-restarting` or the `-install-non-system-updates-without-restarting` option to prioritize non-system updates before any other potential workflows. In other words, even if a macOS update or upgrade is possible, this option forces the super workflow to target non-system updates for installation first. After the appropriate non-system items are installed, then the next time (based on the relaunch deferral timer) the super workflow runs, it reverts to the default targeting behavior (likely a pending macOS update/upgrade).

## Impact on Mac Administration

These new features enhance Mac administration by improving update planning, ensuring compliance, and providing greater flexibility in configuration. Now, organizations can focus on enforcing only Safari updates when they may not want to enforce updates to something like Xcode command-line tools. Kevin continues to provide a plethora of enforcement and deployment options for the versatile tool. 

> Note: Since this is a beta release, thorough testing is advised before full deployment.
> 

---

As mentioned, there are many different ways one can configure and deploy `super` to an organization, and someone new to the tool may be wondering, “What should I configure, and what do I **need** to configure for my fleet?”

Before I dive into a few examples and scenarios, let's cover a few basics. 

### Getting Started with `super`

- Read the [GitHub Wiki](https://github.com/Macjutsu/super/wiki) thoroughly and fully before you start testing.
If something does not appear right in your testing, you should review on-device logging. Logging in Jamf Pro Policies may not tell the full story of what is occurring on the device.
    - Logs can be found at /Library/Management/super
- **Important** - The correct number of settings in a super configuration profile is the ***least*** required for your organization.

**Resources**:

- **Wiki** - [Macjutsu/super - *S.U.P.E.R.M.A.N. v5.0.0*](https://github.com/Macjutsu/super/wiki)
- **Reading** - Tech IT Out – [Getting Started With S.U.P.E.R.M.A.N.](https://techitout.xyz/2023/01/19/getting-started-with-s-u-p-e-r-m-a-n/)
- **Reading** - FileWave Knowledge Base - [S.U.P.E.R.M.A.N. for macOS Software Updates (macOS Script)](https://kb.filewave.com/books/software-updates-apple/page/superman-for-macos-software-updates-macos-script)
- **Video** - [S.U.P.E.R.M.A.N. II](https://www.youtube.com/watch?v=Lj26nuCH1jA) | JNUC 2023
- **Video** - [How to Soar with S.U.P.E.R.M.A.N.](https://www.youtube.com/watch?v=KSAOENlon4A) | Rocketman Tech LaunchPad (May 2023)
- **Video** - [Need S.U.P.E.R.M.A.N. to Save Your Jamf Server?](https://www.youtube.com/watch?v=ILg37NUWz20) | Rocketman Tech LaunchPad (May 2024)
- **Reading** - Jamf Blog - [S.U.P.E.R.M.A.N III - JNUC 2024](https://www.jamf.com/blog/superman-macos-updates-mobile-device-management/)
- **Video** - [Mobile Device Management - S.U.P.E.R.M.A.N. III](https://www.youtube.com/watch?v=ilVj6gqZiP4) | JNUC 2024

## Testing and Implementation Recommendations

1. **Review Release Notes**: Understand all changes before deployment if you intend to test version 5.1.0 and its new keys. 
2. **Test in a Controlled Environment**: Validate in a non-production setup. VMs work wonders. 
3. **Evaluate Key Features**: Assess scheduling workflows and update enhancements.

After having a general foundation on the capabilities of `super` , you, the Mac Admin must develop your strategy of configuration; meaning, what keys do you **require?**

## Develop Your Configuration Strategy

I’ve reached out to Mac Admins online across multiple industries to gather some real-world examples of how others are configuring the tool for their platforms.
Here are two real-world examples of simple configurations but have been given a lot of thought on implementation.

Richard Smith supports a HigherED environment at the University of Waikato in New Zealand. You can find him on the Mac Admins Slack as @wakco 

> I only have two configurations:
Labs - where super is configured to just perform updates immediately, but it is limited to running only on weekends.
Assigned - where super is configured with deferrals based on days since release, using SOFA, with a 3/5/7 configuration for focus, soft, hard days.
The same policy is used for both, including a `--reset-super` and any details I don't want in the config profiles. I also have Self Service and Support.app configured with `--workflow-install-now` `--workflow-reset-super-after-completion` for people to be able to trigger a check and update now.
> 

Andrew Stokes is a Client Engineer at Zillow. He shared how he configures super across his fleet. You can find him on Slack @TechTrekkie.

> I typically use three separate configurations:
- One for most users
- One for devices enrolled within the last 2 days

One for a our incident management team to give them a longer window when they're on call and can't really reboot.

1st Config: I give a deadline of 5 days with `DeadlineDaysSoft`, and use `ScheduleZeroDateRelease` to base the zero date off of SOFA release date. I'll set `DeadlineDaysFocus` to 4 so if for some reason they have something causing a non-stop focus scenario, they'll at least get notifications that last 24 hours. I have `ScheduledInstallUserChoice` enabled to allow users to schedule the update, or pick one of the options from `DeferralTimerMenu` ranging from 30 min. to 2 days.
`DialogTimeoutSoftDeadline` is set to 24 hours so they have  plenty of time for that final warning.

2nd Config: for devices enrolled in the last two days, I don't include `ScheduleZeroDateRelease`  and give them a soft deadline of 2 days. This is essentially for new hires to not force them into an immediate update on day 1 if it's past the window from the SOFA release date.

3rd Config, only difference from the first config is the `DeadlineDaysSoft` is set to 8 days.

**Deployment/Update Rings:**
I have 7 smart groups for deployment rings using an extension attribute based off of the UDID for the device, and use software update deferrals to space out when each ring will see a macOS update by 1 day each ring (Ring 1 will see on release day, Ring 2 release+1 and so on). Typically I'll set rings 5-7 on the same day unless I see issues with the first four.  The rings progress in size, the percentage of the mac population is as follows:
Ring 1 - 1%
Ring 2 - 4%
Ring 3 - 10%
Ring 4 - 20%
Ring 5 - 20%
Ring 6 - 20%
Ring 7 - 25%

**Apple Silicon Authentication:**
The majority of our Macs have a LAPS admin account with a SecureToken, so I have a deployment script that will deploy super with relaunch disabled to do the initial install, and then the script that verifies the LAPS credentials and will trigger super to create a super service account using the LAPS account with the trigger `--auth-service-add-via-admin-account`.  If for some reason the checks against the LAPS account fail, it reverts to using `--auth-ask-user-to-save-password`

**Upgrades:**
For any upgrade deployments, I just use a separate script scoped to the appropriate macs with the following triggers:
`--install-macos-major-upgrades`
`--install-macos-major-version-target=XX`
`--workflow-reset-super-after-completion`
> 

Now, lets look at some more scenario examples of how one can configure `super` in the enterprise. 

The following scenarios are meant to serve as simplistic examples and are by no means a step-by-step guide on how you, the reader, should/need to configure yourself. 

**Example 1: Set It and Forget It.**
![Super Configuration Demo 01](/assets/img/postimages/super_demo_01.JPG)
In this scenario, our example organization has given the end user a great deal of freedom on when software updates get applied, allowing the user to choose (`ScheduledInstallUserChoice`) a date and time up to 14 days (`DeadlineDaysHard`) from when Apple has released an update (`ScheduleZeroDateRelease`). We also will apply non macOS updates with `InstallNonSystemUpdatesWithoutRestarting`. The only other customization is the organization is setting the company logo (`DisplayIconFile`) within the configuration profile. 

Not shown, when deploying `super` we will make use of the Jamf Pro API Role and Client, and be required to pass Script Parameters in the form of `--auth-jamf-client=688326af-6be1-4fd4-868a-e33a167b01e6` 

`--auth-jamf-secret=f56df3be3f73e3694b6a19ffd0e59cbf28e20b4acb1a6868e6d73170120d955e`

**Example 2: Auto Pilot Disengaged**
![Super Configuration Demo 02](/assets/img/postimages/super_demo_02.JPG)

More regulated environments may not have the desire to have `super` running in an automated fashion, and instead, only want the tool to run when called upon by IT. This org has deployed the key `WorkflowDisableRelaunch` so that once `super` is done, **it is done**. *Finito*. 

This organization redeploys the super script via a Jamf Pro Policy any time they wish to engage the tool to enforce software updates to the enterprise. Users have 3 days from when `super` sees a macOS update as being available - as they are not making use of SOFA, and afterwards, the update is enforced. After a successful update, `super` turns itself off. This allows the organization to have total control over when updates are applied, allowing them to go through internal approval processes before acknowledging the update is validated for the enterprise. It prevents super from accidentally enforcing updates before the organization is ready for it - maybe because they are not running beta versions of macOS internally. 

**Example 3: Too Many Choices On The Menu Make It Hard To Pick**
![Super Configuration Demo 03](/assets/img/postimages/super_demo_03.JPG)

In this scenario, the configuration profile requires regular updates to set the `ScheduledInstallDate`. The organization has opted to set the installation date for all systems across the fleet instead of allowing the User to pick their installation date and time. The organization may opt to couple this workflow with regular communications or announcements informing end users of the mandatory installation time. No other major changes are required for the configuration profile, but it can surely be expanded to prefer the user’s credentials for authenticating the software update. Otherwise, the Jamf Pro API, a dedicated service account, or local account credentials can be passed through script parameters within Jamf Pro’s policy section.

Every time Apple releases a new update, the organization must deploy an update to the profile to extend the date for `ScheduledInstallDate` so that it is no longer a time in the past. This process will repeat per update or until a different workflow is put into use.

“*Those examples are great Tony… But I want more.*”

### **Fully Automated Updates (Minimal User Interaction)**

Some organizations opt to run macOS updates almost completely hands-off. With Super configured with nothing but default behaviors, updates download in the background, and the decision is mainly left up to the end user. 

![Super No Deadlines](/assets/img/postimages/macOS-Update-Upgrade-Dialog.png)

- **No User Authentication Prompts:** On **Intel Macs**, Super can install updates as root without any additional credentials. On **Apple Silicon Macs**, fully silent updates require either an MDM command or supplying admin credentials to Super (more on that below). If neither is configured, Super will **prompt the logged-in user for their password** at install time. The best practice for automation is to avoid that prompt by providing credentials or using MDM, ensuring updates run without user intervention.

![Super Apple Silicon Authentication](/assets/img/postimages/Example-User-Authentication-Dialog.png)

- **Scheduled Installation:** Highly automated setups often want to ensure the user doesn’t have to interact with super. You can also leverage Super’s **maintenance window** or **scheduled install** features to run updates during off-hours (e.g., nightly) to minimize disruption.

** A Best Practice: ** If you need hands-off patching, configure Super with no (or very limited) deferrals and automate authentication**. Use an MDM integration or stored credentials for Apple Silicon so the process doesn’t stall for a password. Schedule the updates during low-usage times when possible to reduce impact.

### **IT-Controlled Updates for Regulated Environments**

IT often tightly controls updates in highly regulated industries (finance, healthcare, government, etc.). Organizations might **delay or manually approve updates** due to compliance checks or software compatibility testing. Super can be configured to support this IT-driven approach:

- **Manual Trigger & Maintenance Windows:** Rather than running continuously, Super can be deployed or triggered only when IT is ready to roll out an update. Admins might push a Jamf policy to **initiate updates at a specific approved time**.
- **Minimal User Choice:** In compliance-focused settings, you may choose **not to allow deferrals** or only allow very short deferrals (e.g. postpone by a few hours, or just a few days) once the update is triggered. The priority is ensuring devices patch promptly within mandated timeframes. Super can enforce an immediate restart or update once the scheduled time arrives, with limited ability for users to skip it.
- **Block/Allow Specific Updates:** These organizations often differentiate between **minor security updates and major OS upgrades**. A common practice is to *enforce minor updates* (security patches) regularly but *delay major OS upgrades* until they are tested. Super supports this by configuration. By default, Super focuses on minor macOS updates; it will only upgrade to a new OS if explicitly allowed. For instance, adding the `AllowUpgrade` key set to true in Super’s config profile will permit jumping to the next macOS version. Not setting that means Super will stick to the current OS family’s updates – a helpful safeguard if you want to **hold off on major OS changes** (e.g. going from macOS 14 Sonoma to macOS 15 Sequoia) in regulated environments. A healthcare or government agency might use Super to enforce that all Macs apply, say, the latest macOS security update within 7 days of release (to meet security requirements). They might **hold back major version upgrades** for months - to a max of 90 days, and only push them via Super after thorough testing.

**A Best Practice:** In these environments, **integrate Super with your change-management process**. Use configuration profiles to set a policy that *disables automatic major upgrades* (unless needed) and consider using **Super’s scheduling or maintenance window** to apply updates during authorized times. Ensure any required credentials (Jamf API or local admin) are in place on Apple Silicon Macs so that when you trigger the update, it is complete without user input. This approach gives IT full control over *when* updates occur, aligning with regulatory or business requirements.

### **Hybrid Approach – Automation with User Deferrals**

Many organizations strike a balance between behind-the-scenes automation and user convenience. In this **hybrid approach**, Super is configured to handle updates automatically but gives end-users some ability to defer or choose timing – up to a point:

- **User Deferrals with Deadline:** Admins often allow users to **postpone updates for a limited period**. For example, company-issued laptops might get a new OS update and Super will start prompting the user, but the user can hit defer a few times. With Super, you can set a **maximum deferral count or X days** the user can delay, or a final **deadline date** after which deferral is no longer permitted.
- **Custom Notifications and UX:** Super’s strength is in its user communication. It uses IBM Notifier for customizable dialogs. In a hybrid model, you can configure **polite reminders that grow more insistent** as the deadline nears – for example, initial pop-ups explaining an update is available, later notifications counting down days left, and final ones that warn of an imminent auto-restart. This helps encourage users to act independently before they hit the hard enforcement. Custom notifications are handled via the “[Custom Display Accessory](https://github.com/Macjutsu/super/wiki/Display-Customization)” settings within super’s configuration.
- **User-Scheduled Installation:** To further empower users, Super supports letting the **end user pick a specific time** to run the update via the `ScheduledInstallUserChoice` key. For instance, the prompt might offer an “Install tonight at 11:00 PM” option. This hybrid tactic increases compliance (users schedule it themselves) while still ensuring it happens soon.

![Super Schedule Install User Choice](/assets/img/postimages/Scheduled-Install-User-Choice-Deadline-Dialog.png)

- **Use Case – Corporate Offices & Education (Staff/Faculty):** This balanced approach is increasingly common in enterprise and education. Employees or faculty appreciate not being surprised by an immediate reboot, but the organization still needs everyone updated within e.g. a week or two for security. Super’s deferral and notification features were designed exactly for this scenario: *encourage* users to update while eventually *enforcing* it. The result often is fewer help-desk tickets and a high compliance rate by the deadline.

**Best Practice:** Configure Super’s **deferral options and deadline** to align with your desired grace period. Many admins choose a short deferral interval (like allowing 1-day deferrals up to 7 days) or a fixed deadline (e.g.,) “next Monday”). Use Super’s messaging customization to communicate to users what’s happening and by when clearly. This approach keeps users in the loop and somewhat in control, but guarantees updates aren’t postponed indefinitely. If using Jamf make use of and monitor extension attributes to ensure devices do meet the deadline and adjust the balance of deferral vs. force as needed for your user base.

### **Best Practice:** Tailor Super's configuration to your industry's demands:

**If uptime is critical** (healthcare, manufacturing), use maintenance windows or user scheduling to ensure updates happen only during safe downtime.

**If security compliance is king** (finance, government), use short deferral periods or none at all, and communicate to users that updates will be enforced by a deadline.

**If devices are shared or users aren't admins** (education labs, call centers), lean on fully automated, silent updates with MDM commands to avoid needing user credentials.

**If flexibility is needed** (corporate knowledge workers), adopt the hybrid approach with clear deadlines.

Super's broad feature set — from deferral counts to scheduled installs — allows it to be configured for all these scenarios. Its presence in education, enterprise, and government environments shows that you can likely adapt it to your organizational needs.

## Conclusion

The 5.0 release of S.U.P.E.R.M.A.N offers substantial improvements in update management and scheduling. It’s already used in education, enterprise, and government settings to keep Macs updated with minimal hassle￼. Whether you want fully automated, strictly controlled, or balanced updates, there’s likely a Super configuration that fits. Many organizations are still evaluating upgrading their deployments from 3.x or 4.x to the latest version. Now with 5.1.0-beta1, Mac Admins can begin the careful testing and implementation process to help organizations effectively leverage these benefits.

Want to learn more? Join the conversation at the [Mac Admins Foundation Slack](https://www.macadmins.org/) in the channel [#super](https://macadmins.slack.com/archives/C03LKQ8EN2C).
