---
title: "Enhancing Super Status Reporting in Jamf Pro"
date: 2025-04-21 14:00:00 -0400
description: "Categorizing, Tracking, and Trending SUPERMAN v5.1. Introducing set of complementary Jamf Pro Extension Attributes that work to surface SUPERMAN’s status data in a simplified format IT and Help Desk teams can use immediately, while fitting in nicely with the other useful extension attributes that currently exist."
tags: [macOS, Reporting, Jamf Pro, Jamf, super, Audit, softwareupdate, Software Updates, Extension Attributes, macjutsu]
categories: [Mac Management]
pin: false
---

![Enhancing Super Status Reporting Banner.png](/assets/img/postimages/Enhancing_Super_Status_Reporting.png){: lqip="/assets/img/postimages/Enhancing_Super_Status_Reporting.png" }

I work within a highly regulated enterprise environment, and so, maintaining visibility into the macOS update workflow is critical. Whether for compliance reporting, proactive remediation, or general fleet health, knowing exactly what’s happening during an update—on every device—is a necessity, not a luxury.  Many organizations like my own make use of `super`, an amazing tool designed and maintained by Kevin White (aka Macjutsu).

I’ve previously written about various ways organizations can [develop a configuration strategy](https://tonyyo11.github.io/posts/mastering-macos-updates-with-super/#develop-your-configuration-strategy) for super depending on the industry and need. 

A significant change in the 5.1.0 release is the introduction of a new, condensed audit log file, located at:

`/Library/Management/super/logs/super-audit.log`

This file now contains only the most important status events—installation events, workflow target changes, deadline adjustments, successful workflow completions, and any other event that dramatically alters the SUPERMAN workflow. With this new audit log file comes a new extension attribute to capture these status events in Jamf Pro so that admins may see a glimpse into the timeline of events that `super` goes through on a local device that extends beyond the initial installation process that policy logs may show. That extension attribute can be [found here on GitHub [super-Audit-Log-Jamf-Pro-EA.sh].](https://github.com/Macjutsu/super/blob/5.1.0-beta2/Super-Friends/Jamf-Pro/super-Audit-Log-Jamf-Pro-EA.sh)

This enhanced version not only aligns with best practices but also simplifies troubleshooting and remediation efforts. With these welcomed enhancements, it still so happened that a need arose, within my own work and in other organizations, to better group systems to pinpoint those that may need…. some extra love. 

To address that, I’ve developed a set of **three complementary Jamf Pro Extension Attributes** that work together to surface Super’s status data in a format IT teams can use immediately, while fitting in nicely with the other useful extension attributes that presently exist for `super`.

All three Extension Attributes can be found on my Github: [https://github.com/tonyyo11/MacAdministration](https://github.com/tonyyo11/MacAdministration/tree/main/Jamf%20Extension%20Attributes)

---

## **The Three Extension Attributes**

### **1. Super Category EA**

**Script Name:** [super-Category-EA.sh](https://github.com/tonyyo11/MacAdministration/blob/main/Jamf%20Extension%20Attributes/super-Category-EA.sh)

**Purpose:** Categorize the *current* Super status on a device.

This new Super Categories Extesnion Attribute is meant to complement existing Super EAs such as `super-Status-Jamf-Pro-EA.sh` and `super-Audit-Log-Jamf-Pro-EA.sh`. This EA reads the latest status information from both the local Super `.plist` file at `/Library/Management/super/com.macjutsu.super.plist` and, if using version 5.1.0 or later, the `super-audit.log`, determines which is newer, and maps the result to one of six high-level categories:

- **Error**
- **Inactive**
- **Pending**
- **Running SoftwareUpdate**
- **Dialog Prompts**
- **Complete**

**More on the Category Breakdowns here:**
<details>
<summary>Click to expand</summary>

<br>
     <strong>For Super Status EA (Local Property List)</strong>
<br>
    For statuses returned by the <code>super-Status-Jamf-Pro-EA.sh</code> Extension Attribute, the new categories and rules for mapping are:
<br>
    - <strong>Error:</strong><br>
        - Any status containing the substring <strong>“Inactive Error:”</strong> is immediately classified as an <strong>Error</strong>.<br>
    - <strong>Inactive:</strong><br>
        - Any status beginning with <strong>“Inactive:”</strong> (unless caught by other more specific rules) is mapped to <strong>Inactive</strong>.<br>
    - <strong>Running SoftwareUpdate:</strong><br>
        - Any status starting with <strong>“Running:”</strong> that does <strong>not</strong> include the term <strong>“Dialog”</strong> indicates that a SUPERMAN workflow is actively processing a software update.<br>
    - <strong>Dialog Prompts:</strong><br>
        - Any status starting with <strong>“Running:”</strong> that includes <strong>“Dialog”</strong> requires user interaction and is thus mapped to <strong>Dialog Prompts</strong>.<br>
    - <strong>Pending:</strong><br>
        - Any status containing <strong>“Pending:”</strong> (provided it does not include other overriding conditions) is categorized as <strong>Pending</strong>.<br>
    - <strong>Complete:</strong><br>
        - Any status containing <strong>“Full super workflow complete!”</strong> is forced to map to <strong>Complete</strong> even if it indicates that an automatic relaunch is scheduled. The local status is therefore considered complete regardless of whether a relaunch will occur.<br>
<br>
     <strong>Example</strong>
<br>
    <code>
    Thu Apr 03 11:17:43: Pending: Full super workflow complete! The super workflow is scheduled to automatically relaunch in 360 minutes.
    </code>
<br><br>
    This is mapped to <strong>Complete</strong>.
<br><br>
     <strong>For Super Audit EA (Audit Log)</strong>
<br>
    For the status returned from the <code>super-Audit-Log-Jamf-Pro-EA.sh</code> Extension Attribute log entries, the mapping is defined as:
<br>
     <strong>Resetting:</strong><br>
        - Any status that contains phrases such as <strong>“Status: Resetting”</strong>, <strong>“Status: Deleting”</strong>, <strong>“Status: Migrating”</strong>, or any reference to <strong>“log_super_audit:”</strong> is categorized as <strong>Resetting</strong>. These indicate actions like credential or preference resets.<br>
     <strong>Pending:</strong><br>
        - Entries with <strong>“Status: Setting new scheduled installation”</strong> or <strong>“Restarting computer”</strong> are mapped to <strong>Pending</strong>.<br>
        - In addition, if a full workflow completion message includes <strong>“scheduled to automatically relaunch”</strong>, then it is also mapped to <strong>Pending</strong>.<br>
     <strong>Running SoftwareUpdate:</strong><br>
        - Any line mentioning <strong>“softwareupdate:”</strong>, <strong>“MDM:”</strong>, or <strong>“Installation:”</strong> is mapped to <strong>Running SoftwareUpdate</strong> because these denote that the macOS update or upgrade workflows are active.<br>
     <strong>Inactive:</strong><br>
        - If the status message includes <strong>“Inactive”</strong>, it is mapped to <strong>Inactive</strong>, unless another rule takes precedence.<br>
     <strong>Error:</strong><br>
        - Any audit log entry with <strong>“Error:”</strong> or <strong>“Warning:”</strong> is categorized as <strong>Error</strong>.<br>
        - Furthermore, for full workflow completion messages, if the text explicitly states <strong>“automatic relaunch is disabled”</strong>, the entry is mapped to <strong>Error</strong>.<br>
     <strong>Complete:</strong><br>
        - Any status indicating successful completion (using phrases like <strong>“Completed installation”</strong>, <strong>“completed!”</strong>, or <strong>“all available”</strong>) is mapped to <strong>Complete</strong>, as long as it does not meet one of the above Pending criteria.<br>
<br>
     <strong>Example Audit Log Snippets</strong>
<br>
    <code>
    Thu Apr 03 11:16:20 super[31359]: Status: Full super workflow complete! The super workflow is scheduled to automatically relaunch in 360 minutes.<br>
    Thu Apr 03 11:16:35 super[32958]: Status: Resetting all local (non-managed and non-authentication) preferences.<br>
    Thu Apr 03 11:17:18 super[36126]: Status: Resetting all local (non-managed and non-authentication) preferences.
    </code>
<br><br>
    - Full workflow completion messages that include <strong>“scheduled to automatically relaunch”</strong> are mapped to <strong>Pending</strong> (for audit log purposes), and resetting actions are mapped to <strong>Resetting</strong>.<br>
<br>

</details>


This simplifies complex log data into concise, actionable states. Whether a workflow is actively running, stuck in user deferral, or has completed, you’ll know at a glance.
There are such a vast amount of statuses that are returnable when using Super, which is why this EA exists.

**Expand to see the full list of 75 possible results returnable with the `super-Status-Jamf-Pro-EA.sh` Script**
<details>
<summary>Click to expand</summary>

    1. "Inactive Error: Unrecognized Options: ${unrecognized_options_array[*]%%=*}"<br>
    2. "Inactive Error: Apple silicon authentication options could not be validated and no failover option was specified, the workflow cannot continue."<br>
    3. "Inactive Error: Initial startup validation failed."<br>
    4. "Inactive Error: Configured authentication workflow can not currently install macOS updates/upgrades, install now workflow can not continue."<br>
    5. "Inactive Error: Network unavailable, install now workflow can not continue."<br>
    6. "Inactive Error: Checking for macOS software status workflow failed, install now workflow can not continue."<br>
    7. "Inactive Error: Download macOS update/upgrade workflow failed, install now workflow can not continue."<br>
    8. "Inactive Error: Installation of macOS update/upgrade via softwareupdate failed, install now workflow can not continue."<br>
    9. "Inactive Error: Installation of macOS major upgrade via installer application failed, install now workflow can not continue."<br>
    10. "Inactive Error: Download/install of macOS via MDM failed, install now workflow can not continue."<br>
    11. "Inactive Error: macOS update/upgrade workflow failed, install now workflow can not continue."<br>
    12. "Inactive Error: macOS update/upgrade workflow failed, install now workflow can not continue."<br>
    13. "Inactive Error: Some Jamf Pro Policies failed, install now workflow can not continue."<br>
    14. "Inactive: Full super workflow complete! Automatic relaunch is disabled."<br>
    15. "Inactive Error: Some non-system macOS software updates did not complete, install now workflow can not continue."<br>
    16. "Running: Installation workflow."<br>
    17. "Running: Startup workflow."<br>
    18. "Running: Check for software update status workflow."<br>
    19. "Running: softwareupdate: Starting ${macos_msu_title} authenticated download workflow."<br>
    20. "Running: softwareupdate: Starting ${macos_msu_title} download workflow."<br>
    21. "Running: mist_cli: Starting ${macos_installer_title} ${macos_installer_version}-${macos_installer_build} download installer workflow."<br>
    22. "Running: softwareupdate: Starting non-system macOS software updates installation workflow."<br>
    23. "Running: softwareupdate: Starting Safari ${non_system_msu_safari_target} update installation workflow."<br>
    24. "Running: softwareupdate: Starting ${macos_msu_label} download and upgrade workflow."<br>
    25. "Running: softwareupdate: Starting ${macos_msu_label} upgrade workflow."<br>
    26. "Running: softwareupdate: Starting ${macos_msu_label} download and update workflow."<br>
    27. "Running: softwareupdate: Starting ${macos_msu_label} update workflow."<br>
    28. "Running: Starting ${macos_installer_title} ${macos_installer_version}-${macos_installer_build} install upgrade workflow."<br>
    29. "Running: MDM: Starting ${macos_installer_title} ${macos_installer_version}-${macos_installer_build} install workflow with user authenticated failover."<br>
    30. "Running: MDM: Starting ${macos_installer_title} ${macos_installer_version}-${macos_installer_build} install workflow."<br>
    31. "Running: MDM: Starting macOS ${macos_msu_version} download workflow with user authenticated failover."<br>
    32. "Running: MDM: Starting macOS ${macos_msu_version} download workflow."<br>
    33. "Running: MDM: Starting macOS ${macos_msu_version} download and update/upgrade workflow with user authenticated failover."<br>
    34. "Running: MDM: Starting macOS ${macos_msu_version} download and update/upgrade workflow."<br>
    35. "Running: MDM: Starting macOS ${macos_msu_version} update/upgrade workflow with user authenticated failover."<br>
    36. "Running: MDM: Starting macOS ${macos_msu_version} update/upgrade workflow."<br>
    37. "Running: Restart validation workflow."<br>
    38. "Running: Installing Jamf Pro Policy triggers."<br>
    39. "Running: Dialog user scheduled installation."<br>
    40. "Running: Dialog soft deadline."<br>
    41. "Running: Dialog user authentication."<br>
    42. "Pending: Configured authentication workflow is not currently possible, trying again in ${deferral_timer_minutes} minutes."<br>
    43. "Pending: Network unavailable, trying again in ${deferral_timer_minutes} minutes."<br>
    44. "Pending: Scheduled installation on $(date -j -f %Y-%m-%d "${workflow_scheduled_install:0:10}" +%a | tr '[:lower:]' '[:upper:]') ${workflow_scheduled_install}, with a warning notification ${scheduled_install_reminder_item} minutes prior, deferring for ${deferral_timer_minutes} minutes from now."<br>
    45. "Pending: Scheduled installation on $(date -j -f %Y-%m-%d "${workflow_scheduled_install:0:10}" +%a | tr '[:lower:]' '[:upper:]') ${workflow_scheduled_install}, deferring for ${deferral_timer_minutes} minutes from now."<br>
    46. "Pending: Checking for macOS software status workflow failed, trying restart validation workflow again in ${deferral_timer_minutes} minutes."<br>
    47. "Pending: Checking for macOS software status workflow failed, trying again in ${deferral_timer_minutes} minutes."<br>
    48. Pending: Download macOS update/upgrade workflow failed, trying again in ${deferral_timer_minutes} minutes."<br>
    49. "Pending: Installation of macOS update/upgrade via softwareupdate failed, trying again in ${deferral_timer_minutes} minutes."<br>
    50. "Pending: Installation of macOS major upgrade via installer application failed, trying again in ${deferral_timer_minutes} minutes."<br>
    51. "Pending: Download/install of macOS via MDM failed, trying again in ${deferral_timer_minutes} minutes."<br>
    52. "Pending: Some non-system macOS software updates did not complete, trying again in ${deferral_timer_minutes} minutes."<br>
    53. "Pending: Safari update did not complete, trying again in ${deferral_timer_minutes} minutes."<br>
    54. "Pending: macOS update/upgrade workflow failed, trying again in ${deferral_timer_minutes} minutes."<br>
    55. "Pending: macOS update/upgrade workflow failed, trying again in ${deferral_timer_minutes} minutes."<br>
    56. "Pending: Some non-system macOS software updates remain available for installation, trying restart validation workflow again in ${deferral_timer_minutes} minutes."<br>
    57. "Pending: Unable to submit inventory or perform check-in via Jamf Pro, trying restart validation workflow again in ${deferral_timer_minutes} minutes."<br>
    58. "Pending: macOS update/upgrade workflow failed, trying again in ${deferral_timer_minutes} minutes."<br>
    59. Running: Dialog insufficient storage."<br>
    60. "Running: Dialog power required."<br>
    61. "Running: Dialog user choice."<br>
    62. "Pending: User chose to defer for ${deferral_timer_minutes} minutes."<br>
    63. "Pending: User chose to defer, setting a deferral of ${deferral_timer_minutes} minutes."<br>
    64. "Pending: Display timeout automatically chose to defer for ${deferral_timer_minutes} minutes."<br>
    65. "Pending: Display timeout automatically chose to defer, using the default deferral of ${deferral_timer_minutes} minutes."<br>
    66. "Pending: User authentication for scheduled restart failed, trying again in ${deferral_timer_minutes} minutes."<br>
    67. "Pending: Display timeout automatically chose to defer, using the default deferral of ${deferral_timer_minutes} minutes."<br>
    68. "Pending: Configured authentication workflow is not currently possible, trying again in ${deferral_timer_minutes} minutes."<br>
    69. "Pending: Automatic schedule workflow active deferral until ${schedule_workflow_active_next_start}, deferring for ${deferral_timer_minutes} minutes."<br>
    70. "Inactive Error: No valid Apple slicon authentication, install now workflow can not continue."<br>
    71. "Pending: No current active user, due to limitations in macOS the only download workflow can not continue, trying again in ${deferral_timer_minutes} minutes."<br>
    72. "Pending: The system started up less than $RECENT_STARTUP_AUTO_DEFERRAL_SECONDS seconds ago so it's unsafe to assume current active user status, trying again in ${deferral_timer_minutes} minutes."<br>
    73. "Pending: Automatic user focus deferral, trying again in ${deferral_timer_minutes} minutes."<br>
    74. "Pending: System restart is imminent and the super restart validation workflow is scheduled to automatically relaunch at next startup."<br>
    75. "Pending: Unable to submit inventory to Jamf Pro, trying again in ${deferral_timer_minutes} minutes."<br>
    76. "Pending: Full super workflow complete! The super workflow is scheduled to automatically relaunch in ${deferral_timer_minutes} minutes."<br>
</details>


**Expand to see the full list of 57 possible results returnable with the `super-Audit-Log-Jamf-Pro-EA.sh` Script**
<details>
<summary>Click to expand</summary>

    1. "Status: Resetting all local (non-managed and non-authentication) preferences."<br>
    2. "Status: Resetting all local deferral timer preferences."<br>
    3. "Status: Deleting all scheduled installation preferences."<br>
    4. "Status: Deleting all local deadline count preferences."<br>
    5. "Status: Deleting all local deadline days preferences."<br>
    6. "Status: Deleting all local deadline date preferences."<br>
    7. "Status: Deleting all local dialog timeout preferences."<br>
    8. "Status: Deleting saved credentials for the --auth-ask-user-to-save-password option."<br>
    9. "Status: Deleting saved credentials for the --auth-local-account option."<br>
    10. "Status: Deleting the super service account and saved credentials."<br>
    11. "Status: Deleting saved credentials for the --auth-jamf-client option."<br>
    12. "Status: Deleting saved credentials for the --auth-jamf-account option."<br>
    13. "Status: Deleting saved credentials for legacy local account."<br>
    14. "Status: Deleting local account and saved credentials for legacy super service account."<br>
    15. "Status: Deleting saved credentials for legacy Jamf Pro API account."<br>
    16. "Status: Migrating saved legacy local account credentials..."<br>
    17. "Status: Migrating saved legacy super service account credentials..."<br>
    18. "Status: Migrating saved legacy Jamf Pro API account credentials..."<br>
    19. "Status: Saved new credentials for the --auth-local-account option."<br>
    20. "Status: Saved migrated credentials for the --auth-local-account option."<br>
    21. "log_super_audit: Created new super service account."<br>
    22. "log_super_audit: Created new super service account."<br>
    23. "log_super_audit: Validated migrated super service account."<br>
    24. "log_super_audit: Validated migrated super service account."<br>
    25. Status: Saved new credentials for the --auth-jamf-client option."<br>
    26. "Status: Saved new credentials for the --auth-jamf-account option."<br>
    27. "Status: Saved migrated credentials for the --auth-jamf-account option."<br>
    28. "Status: Deleting saved credentials for legacy local account."<br>
    29. "Status: Deleting saved credentials for legacy super service account."<br>
    30. "Status: Deleting saved credentials for legacy Jamf Pro API account."<br>
    31. "Installation: Copying super ${SUPER_VERSION} to ${SUPER_FOLDER}/super."<br>
    32. "Status: Setting new automatic zero date based on the ${workflow_target} release date of ${schedule_zero_date}."<br>
    33. "Status: Setting new automatic zero date based on the ${workflow_target} workflow start date of ${schedule_zero_date}."<br>
    34. "Status: Setting new scheduled installation for $(date -j -f %Y-%m-%d "${workflow_scheduled_install:0:10}" +%a | tr '[:lower:]' '[:upper:]') ${workflow_scheduled_install} which is the start of the soonest workflow active time frame after zero day."<br>
    35. "Status: Setting new scheduled installation for $(date -j -f %Y-%m-%d "${workflow_scheduled_install:0:10}" +%a | tr '[:lower:]' '[:upper:]') ${workflow_scheduled_install} which is the start of the current schedule workflow active time frame."<br>
    36. "Status: Setting new scheduled installation for $(date -j -f %Y-%m-%d "${workflow_scheduled_install:0:10}" +%a | tr '[:lower:]' '[:upper:]') ${workflow_scheduled_install} which is the start of the latest workflow active time frame on the original scheduled installation date."<br>
    37. "Status: Setting new scheduled installation for ${workflow_scheduled_install} which is ${scheduled_install_days} days after zero date."<br>
    38. "softwareupdate: ${macos_msu_title_downloaded} download and preparation complete."<br>
    39. "Status: A macOS installer is now available at: /Applications/Install ${macos_installer_title}.app"<br>
    40. "softwareupdate: macOS update/upgrade is prepared and ready for restart!"<br>
    41. "Status: ${macos_installer_title} ${macos_installer_version}-${macos_installer_build} is prepared and ready for restart!"<br>
    42. "MDM: ${macos_msu_label} upgrade is prepared and ready for restart!"<br>
    43. "MDM: ${macos_msu_label} update is prepared and ready for restart!"<br>
    44. "MDM: ${macos_installer_title} ${macos_installer_version}-${macos_installer_build} installer is prepared and ready for restart!"<br>
    45. "Status: Completed installation of all non-system macOS software updates!"<br>
    46. "Status: Completed installation of Safari update!"<br>
    47. "Status: Restarting computer in one minute..."<br>
    48. "Status: Restarting computer in one minute..."<br>
    49. "Warning: Some macOS software updates/upgrades did not complete after last restart, continuing workflow."<br>
    50. "Status: Jamf Pro Policy with Trigger \"${jamf_policy_trigger}\" was successful."<br>
    51. "Status: Setting new workflow target to ${workflow_target}".<br>
    52. "Warning: Previous workflow target of ${workflow_target_previous} has been changed to ${workflow_target}."<br>
    53. "Status: Completed installation of all non-system macOS software updates!"<br>
    54. "Status: All available and enabled macOS software updates/upgrades completed!"<br>
    55. "Status: All Jamf Pro Policies completed."<br>
    56. "Status: Full super workflow complete! Automatic relaunch is disabled."<br>
    57. "Status: Full super workflow complete! The super workflow is scheduled to automatically relaunch in ${deferral_timer_minutes} minutes."<br>
<br>

</details>
---

### **2. Super Trend EA**

**Script Name:** [super-Trend-EA.sh](https://github.com/tonyyo11/MacAdministration/blob/main/Jamf%20Extension%20Attributes/super-Trend-EA.sh)

**Purpose:** Show a *timeline* of Super status categories over the most recent cycles.

This EA parses the `super.log`, identifies major workflow milestones (like start, update, reset, and exit), and constructs a one-line historical timeline for each device.

**It’s configurable**:

- `MAX_CYCLES` – how many full cycles to include
- `MAX_EVENTS_PER_CYCLE` – limits how verbose each cycle’s timeline is
- `ONLY_UNIQUE_TRANSITIONS` – filters out repeated status types

**Example Output:**

```
Timeline from Apr 12 06:45:25 to Apr 12 10:20:04
Start - Pending - Running SoftwareUpdate - Complete
```

This gives admins quick insight into how Super has behaved over time—useful for identifying stuck devices, excessive restarts, or failure loops.
For organizations that enable verbose mode, the configurable options come in super handy as the logs can be very…. chatty.

---

### **3. Cycle Count EA**

**Script Name:** [super-CycleCount-EA.sh](https://github.com/tonyyo11/MacAdministration/blob/main/Jamf%20Extension%20Attributes/super-CycleCount-EA.sh)

**Purpose:** Count how many *full update cycles* have occurred.

This EA scans `super.log` for matching pairs of “SUPER STARTUP” and either “EXIT CLEAN” or “EXIT ERROR” lines, indicating a full attempt at an update workflow. This EA directly compliments the Super Trends EA (`super-Trends-EA.sh`)

**Example Output:**

```
4 cycles (1 with dialogs) from Apr 03 09:25:13 to Apr 17 08:11:47
```

This tells you whether Super has run at all on the device, how many times it’s cycled, how many of those cycles resulted in dialogs being presented to the user, and how recently it’s been active.

---

## **Why These Matter for Mac Admins**

Together, these three extension attributes offer a comprehensive look at Super’s status on any managed Mac—from **real-time state**, to **historical behavior**, to **execution frequency**.

### **Key Benefits**

- **Simplified Compliance Tracking**
    
    No need to dig into raw logs. These EAs turn complex workflows into clean, filterable values for inventory views or Smart Groups. They **are not a replacement** for the need to sometimes lift and review raw logs from devices to debug and troubleshoot, though.
    
- **Improved Troubleshooting**
    
    Find stuck devices with just a glance. Compare cycle counts to detect systems that haven’t checked in, or view trend timelines to diagnose repeated issues.
    
- **Smarter Dashboards**
    
    Build real-time Jamf Pro dashboards or exports that show:
    
    - What stage each device is at based on smart groups
- **Automation-Ready**
    
    Trigger remediation policies when:
    
    - Cycle count is zero
    - Category is “Error” or “Inactive”
- **Better Change Window Visibility**
    
    During a patch cycle, it’s invaluable to know which devices are actively installing, which completed, and which still require user action. My own organization runs `super` during change request windows so these additional insights help quickly build our understanding of the current state of the fleet as we attempt to reach our internal compliance and patch numbers before the end of any window. 
    

---

## **What You’ll See in Jamf Pro**

A quick word on timing.

It’s important to remember that Jamf Pro’s reporting is only as fresh as the last **inventory update**. If no recon has occurred since the last Super update, the reported category might lag behind real-time events.

Once added as Jamf Pro Extension Attributes, you can use them like any other field in inventory searches, Smart Groups, or advanced computer searches and reports.

| **Extension Attribute** | **Sample Value** |
| --- | --- |
| super-Category-EA | Running SoftwareUpdate |
| super-Trend-EA | Timeline from Apr 12 to Apr 17: Start - Running  |
| super-CycleCount-EA | 4 cycles (1 with dialogs) from Apr 03 to Apr 17 |

---

## **Best Practices for Deployment**

- Set `MAX_CYCLES=3` and `MAX_EVENTS_PER_CYCLE=5` in the Trend EA to keep reports concise. I recommend this approach if you have verbose logging enabled.
- Use Smart Groups to:
    - Isolate devices with recent Error or Inactive categories
    - Flag devices where Trend EA shows no Complete events

---

## **Sample Use Case: Identifying Broken Devices**

Let’s say you’re reviewing a fleet of 500 Macs. With these EAs enabled, your Jamf Pro report might look like this:

| **Computer Name** | **Category** | **Trend Timeline** | **Cycle Count** |
| --- | --- | --- | --- |
| patchnotes-DESMK6PSJLK8 | Complete | Start - Running - Complete | 4 cycles from Apr 3 to Apr 17 |
| patchnotes-8WCZC6BZ8FT2 | Error | Start - Pending - Error | 2 cycles from Apr 10 to Apr 17 |
| patchnotes-0KHBGPFUS929 | Inactive | No events found | 0 cycles |
| patchnotes-K2YTM8QV26U9 | Running SoftwareUpdate | Start - Running | 1 cycles from Apr 17 to Apr 17 |

From this table:

- patchnotes-DESMK6PSJLK8 is good to go.
- patchnotes-8WCZC6BZ8FT2 is failing and needs remediation.
- patchnotes-0KHBGPFUS929 may be offline or misconfigured.
- patchnotes-K2YTM8QV26U9 is currently updating.

This can help transform Super reporting within Jamf Pro into a transparent, quickly to diagnose system.

---

## **Today’s Tech Tip: Try Suspicious Package**

Inspect `.pkg` installers before deploying them! [Suspicious Package](https://www.mothersruin.com/software/SuspiciousPackage/) lets you review scripts, payloads, and permissions without installing. It’s a must-have for vetting third-party software in secure environments.

---

## **Final Thoughts**

Building these extension attributes taught me a lot about the power of simplification. Instead of raw logs buried in `/Library/Management/super/logs/`, which I often found myself uploading from systems to Jamf Pro, then downloading to my own system for review, we now have clean, structured reporting that scales across hundreds—or thousands—of Macs. Now my team and I can quickly filter through devices that may just need a little extra time versus systems where we do need to look at more verbose logging to determine what is going on and why the device is not updating. Super already gives us powerful control over update workflows. With these Jamf Pro integrations, we now gain clarity, automation, and insight.
If you’ve implemented Super and want to take your reporting to the next level, I hope these tools help your team move faster, troubleshoot smarter, and sleep a little easier during patch week or other compliance deadlines.
Also, if you’ve implemented any of these three new extension attributes, I would love to know how well they serve your needs. How can I make them better? Feel free to leave comments here on this post, or open issues/requests on my Github page! I will continue working on enhancing and refining the EA's to provide the best data possible for organizations!

---
