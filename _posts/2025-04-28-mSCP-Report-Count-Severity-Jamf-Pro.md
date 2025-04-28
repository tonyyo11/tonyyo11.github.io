---
title: "Recording mSCP Failed Results Count by Severity in Jamf Pro"
date: 2025-04-28 09:30:00 -0400
description: "Tracking mSCP Results by DISA STIG Severity within Jamf Pro. I am back with another Jamf Pro Extension Attribute, which turns mSCP compliance failure counts into categorized counts by DISA STIG severity buckets—Category I (High), II (Medium), III (Low), plus Unspecified for any unmapped controls. Designed for organizations implementing STIG baselines, it makes prioritization and reporting a breeze."
tags: [macOS, Reporting, Jamf Pro, Jamf, Audit, Extension Attributes, mSCP, DISA STIG, CIS, Benchmarks]
categories: [Mac Management]
pin: false
---
![Recording mSCP Failed Results Severity.png](/assets/img/postimages/Recording_mSCP_Failed_Results_Severity.png){: lqip="/assets/img/postimages/Recording_mSCP_Failed_Results_Severity.png" }

I am back with another Jamf Pro Extension Attribute, which turns mSCP compliance failure counts into categorized counts by DISA STIG severity buckets—Category I (High), II (Medium), III (Low), plus Unspecified for any unmapped controls. Designed for organizations implementing STIG baselines, it makes prioritization and reporting a breeze.

> Note: The macOS Security Compliance Project (mSCP) applies severity to various rules, based upon the DISA STIG. If implementing the Center for Internet Security (CIS) Benchmark for macOS, this extension attribute truly should not apply.
{: .prompt-info }

Lets review a rule from mSCP – *edited for simplicity.*

```yaml
id: system_settings_content_caching_disable
title: Disable Content Caching Service
discussion: |
  Content caching _MUST_ be disabled.

  Content caching is a macOS service that helps reduce Internet data usage and speed up software installation on Mac computers. It is not recommended for devices furnished to employees to act as a caching server.
references:
  cce:
    - CCE-94357-1
  cci:
    - CCI-000381
  800-53r5:
    - CM-7
    - CM-7(1)
  800-53r4:
    - CM-7
    - CM-7(1)
  srg:
    - SRG-OS-000095-GPOS-00049
  disa_stig:
    - APPL-15-002140
macOS:
  - '15.0'
tags:
  - 800-53r5_low
  - 800-53r5_moderate
  - 800-53r5_high
  - 800-53r4_low
  - 800-53r4_moderate
  - 800-53r4_high
  - stig
**severity: medium**
mobileconfig: true
mobileconfig_info:
  com.apple.applicationaccess:
    allowContentCaching: false
```

# Why Another Extension Attribute?

This extension attribute uses the DISA STIG category definitions published on the DoD Cyber Exchange to automatically map each mSCP compliance failure into High, Medium, or Low severity buckets, and it includes a fully customizable ruleset—just edit the `HIGH_RULES`, `MEDIUM_RULES`, and `LOW_RULES` arrays in the script—to rebalance those buckets or add entirely new mappings for any internal baselines or extra controls your organization requires.

### Demo Data

| **Computer Name** | **mSCP Failed Rules Severity Count** |
| --- | --- |
| patchnotes-A1B2C3 | **Low** 0  **Medium** 2  **High** 1  **Unspecified** 0 |
| patchnotes-D4E5F6 | **Low** 3  **Medium** 0  **High** 0  **Unspecified** 1 |
| patchnotes-G7H8I9 | **Low** 1  **Medium** 4  **High** 2  **Unspecified** 0 |
| patchnotes-J0K1L2 | **Low** 0  **Medium** 0  **High** 0  **Unspecified** 5 |

The systems here have a variety of failures. Those that have Unspecified counts are for rules that are not part of the DISA STIG, but the demo organization “*Patch Notes Inc.*” has tailored a baseline via mSCP to include additional rules they wish to enforce. 

# **Impact on Enterprise STIG Compliance**

By pushing severity counts directly into Jamf Pro as separate EA fields, you can:

- **Spot Critical Systems Instantly**
Target devices with any Category I failures for immediate remediation.
- **Build Smart Groups & Dashboards**
Filter on High > 0, Medium > 2, or any Unspecified > 0 to drive tailored workflows.
- **Trend Compliance Over Time:**
Create something similar to what I’ve documented in **Part One:** [Generating Monthly Trend Reports for mSCP Compliance in Jamf Pro](https://tonyyo11.github.io/posts/Generating-Monthly-Trend-Reports-mSCP/) and **Part Two:** [Enhancing Monthly Trend Reports with Jamf Pro and mSCP - Version 2.1](https://tonyyo11.github.io/posts/mSCP-Trend-Report-Part2/) to export reporting out of Jamf Pro and create trending data based upon the severities for a given system.

# Additional Info

For the script itself, see: [mscp_failed_rules_severity_count.sh on GitHub](https://github.com/tonyyo11/MacAdministration/blob/main/Jamf%20Extension%20Attributes/mscp_failed_rules_severity_count.sh)

Learn more about the underlying framework at the macOS Security Compliance Project GitHub:

[https://github.com/usnistgov/macos_security](https://github.com/usnistgov/macos_security)

And explore official DISA STIG guidance on the DoD Cyber Exchange:

[https://public.cyber.mil/stigs/](https://public.cyber.mil/stigs/)

### Tip of the Day:

Run `sudo jamf recon` on any Mac to force an immediate evaluation of all script-based extension attributes—including your new severity EA—so you can verify counts in real time without waiting for the next full inventory cycle.

Full Extension Attribute
Source: [tonyyo11/MacAdministration on Github](https://github.com/tonyyo11/MacAdministration/blob/main/Jamf%20Extension%20Attributes/mscp_failed_rules_severity_count.sh)

```bash
#!/bin/bash
#
# Script Name:            mscp_failed_rules_severity_count.sh
# Author:                 Tony Young
# Organization:           Cloud Lake Technology, an Akima company
# Date:                   2025-04-23
# Purpose:                Jamf Pro EA to count failed mSCP controls by severity.
# 			              Severity is mapped against the DISA STIG Baseline within the 
#						  Sequoia Guidance Revision 1.1 Release of macOS Security Compliance Project
# Description:            1) Run the exact “Failed Result List” logic to get `sorted[]`  
#                         2) Map those entries into Low/Medium/High/Unspecified counts
#
# === 1) Get raw failed‐rules list (from your “Failed Result List” EA) ===

audit=$(/bin/ls -l /Library/Preferences \
         | /usr/bin/grep 'org.*.audit.plist' \
         | /usr/bin/awk '{print $NF}')
FAILED_RULES=()
if [[ -n "$audit" ]]; then
  auditfile="/Library/Preferences/${audit}"

  # Extract every rule key
  rules=( $(
    /usr/libexec/PlistBuddy -c "print :" "${auditfile}" \
      | /usr/bin/awk '/Dict/ { print $1 }'
  ) )

  # Filter to only those with finding == true
  for rule in "${rules[@]}"; do
    [[ "$rule" == "Dict" ]] && continue
    FINDING=$(/usr/libexec/PlistBuddy -c "print :$rule:finding" "${auditfile}" 2>/dev/null)
    if [[ "$FINDING" == "true" ]]; then
      FAILED_RULES+=("$rule")
    fi
  done
else
  FAILED_RULES=("UNKNOWN")
fi

# Sort them (just like the List EA does)
IFS=$'\n' sorted=( $(printf "%s\n" "${FAILED_RULES[@]}" | /usr/bin/sort) )
unset IFS

# === 2) Define your severity buckets ===

LOW_RULES=(
  "audit_configure_capacity_notify"
  "audit_retention_configure"
  "os_burn_support_disable"
  "os_messages_app_disable"
  "os_skip_screen_time_prompt_enable"
)

MEDIUM_RULES=(
    "audit_acls_files_configure"
    "audit_acls_folders_configure"
    "audit_auditd_enabled"
    "audit_control_acls_configure"
    "audit_control_group_configure"
    "audit_control_mode_configure"
    "audit_control_owner_configure"
    "audit_failure_halt"
    "audit_files_group_configure"
    "audit_files_mode_configure"
    "audit_files_owner_configure"
    "audit_flags_aa_configure"
    "audit_flags_ad_configure"
    "audit_flags_ex_configure"
    "audit_flags_fd_configure"
    "audit_flags_fm_configure"
    "audit_flags_fm_failed_configure"
    "audit_flags_fr_configure"
    "audit_flags_fw_configure"
    "audit_flags_lo_configure"
    "audit_flags_na_configure"
    "audit_flags_pd_configure"
    "audit_flags_ps_configure"
    "audit_flags_pt_configure"
    "audit_flags_ss_configure"
    "audit_final_filter_configure"
    "audit_fm_filter_configure"
    "audit_fw_filter_configure"
    "audit_group_wheel_ownership"
    "audit_log_file_ownership"
    "audit_log_file_permissions"
    "audit_log_rotate_size_configure"
    "audit_owner_wheel_ownership"
    "audit_policy_flags"
    "audit_root_privilege_events"
    "audit_sacl_group_configure"
    "audit_sacl_mode_configure"
    "audit_sacl_owner_configure"
    "audit_sacl_success_events"
    "audit_sacl_unauthenticated_access"
    "audit_usergroup_access_events"
    "audit_usergroup_remote_access_events"
    "audit_user_remote_access_events"
    "audit_write_events"
    "kernel_event_audit_enable"
    "os_account_lockout_duration_configure"
    "os_account_lockout_threshold_configure"
    "os_account_lockout_unlock_time"
    "os_account_password_age_maximum"
    "os_account_password_history"
    "os_account_password_length_minimum"
    "os_account_password_lockout"
    "os_account_password_complexity"
    "os_airplay_password_protect_disable"
    "os_apple_signature_verification_disable"
    "os_automatic_login_disable"
    "os_bonjour_disable"
    "os_boot_efi_set_password"
    "os_camera_disable"
    "os_carplay_disable"
    "os_control_center_removal_disable"
    "os_control_center_settings_disable"
    "os_control_center_wifi_disable"
    "os_control_center_airplane_mode_disable"
    "os_control_center_bluetooth_disable"
    "os_control_center_dnd_disable"
    "os_control_center_media_disable"
    "os_dark_mode_disable"
    "os_developer_tools_disable"
    "os_dictation_disable"
    "os_display_sleep_enable"
    "os_filevault_recovery_key_rotation"
    "os_firmware_password_enable"
    "os_guest_account_disable"
    "os_icloud_drive_disable"
    "os_icloud_keychain_sync_disable"
    "os_icloud_password_sharing_disable"
    "os_local_account_creation_disable"
    "os_location_services_disable"
    "os_lock_screen_hotcorner_disable"
    "os_log_level_configure"
    "os_login_window_custom_message"
    "os_mac_app_store_auto_update_disable"
    "os_mac_app_store_automatic_download_disable"
    "os_mdm_enforced_removal_disable"
    "os_mdm_profile_removal_disable"
    "os_nfs_disable"
    "os_ntp_configure"
    "os_ntp_encrypt_disable"
    "os_one_time_password_disable"
    "os_parking_mode_disable"
    "os_password_auto_unlock_enable"
    "os_pin_uid"
    "os_plist_file_permissions"
    "os_power_settings_modify_disable"
    "os_proxy_configuration_disable"
    "os_recovery_mode_disable"
    "os_remote_apple_events_disable"
    "os_remote_login_disable"
    "os_remove_firefox_plugins"
    "os_restrict_wifi_disable"
    "os_root_login_disable"
    "os_screen_sharing_disable"
    "os_secure_token_enforce"
    "os_security_trust_settings_disable"
    "os_signal_lock_screen"
    "os_sip_authentikate"
    "os_sip_check_injected_kexts"
    "os_sip_configure"
    "os_sip_entitlements_disable"
    "os_sip_enable"
    "os_sip_fv_enable"
    "os_sip_fs_protections_disable"
    "os_sip_kexts_disable"
    "os_sip_nvram_disable"
    "os_sip_task_for_pid_disable"
    "os_sip_uikit_app_disable"
    "os_sip_unapproved_kexts_configure"
    "os_smb_share_disable"
    "os_software_updates_auto_download_disable"
    "os_software_update_diagnostics_disable"
    "os_software_update_enforce"
    "os_spotlight_disable"
    "os_time_machine_backup_disable"
    "os_time_machine_encrypted_backups_only"
    "os_timezone_enforce"
    "os_trusted_kernel_extensions"
    "os_tvos_remote_disable"
    "os_usb_disable"
    "os_user_groups_manage_disable"
    "os_user_guest_remove"
    "os_user_root_remove"
    "system_settings_app_store_disable"
    "system_settings_apple_pay_disable"
    "system_settings_auto_accept_invites_disable"
    "system_settings_bluetooth_autoconnect_disable"
    "system_settings_carplay_disable"
    "system_settings_control_center_disable"
    "system_settings_dashboard_disable"
    "system_settings_dark_mode_disable"
    "system_settings_display_sleep_timeout_enforce"
    "system_settings_dictation_disable"
    "system_settings_filevault_keychain_timeout"
    "system_settings_find_my_disable"
    "system_settings_game_center_disable"
    "system_settings_icloud_drive_disable"
    "system_settings_icloud_keychain_disable"
    "system_settings_icloud_private_relay_disable"
    "system_settings_jumpstart_disable"
    "system_settings_keyboard_autocorrect_disable"
    "system_settings_keyboard_spellcorrect_disable"
    "system_settings_login_window_disable"
    "system_settings_mail_remote_content_disable"
    "system_settings_mdm_enforced_disable"
    "system_settings_messages_disable"
    "system_settings_migration_assistant_disable"
    "system_settings_notifications_disable"
    "system_settings_password_reset_disable"
    "system_settings_password_requirements"
    "system_settings_password_timeout_enforce"
    "system_settings_playtime_disable"
    "system_settings_proxy_auto_config_disable"
    "system_settings_security_agent_disable"
    "system_settings_softwareupdate_critical_update_disable"
    "system_settings_sounds_disable"
    "system_settings_spotlight_disable"
    "system_settings_screensaver_ask_for_password_delay_enforce"
    "system_settings_screensaver_password_enforce"
    "system_settings_screensaver_timeout_enforce"
    "system_settings_siri_disable"
    "system_settings_siri_settings_disable"
    "system_settings_smbd_disable"
    "system_settings_softwareupdate_current"
    "system_settings_ssh_enable"
    "system_settings_time_server_configure"
    "system_settings_time_server_enforce"
    "system_settings_token_removal_enforce"
    "system_settings_touchid_unlock_disable"
    "system_settings_usb_restricted_mode"
    "system_settings_wallet_applepay_settings_disable"
    "system_settings_wifi_disable"
)

HIGH_RULES=(
  "auth_ssh_password_authentication_disable"
  "icloud_appleid_system_settings_disable"
  "os_anti_virus_installed"
  "os_certificate_authority_trust"
  "os_gatekeeper_enable"
  "os_setup_assistant_filevault_enforce"
  "os_sip_enable"
  "os_ssh_fips_compliant"
  "os_sshd_fips_compliant"
  "os_tftpd_disable"
  "system_settings_bluetooth_disable"
  "system_settings_filevault_enforce"
  "system_settings_gatekeeper_identified_developers_allowed"
  "system_settings_ssh_disable"
  "system_settings_system_wide_preferences_configure"
)

# === 3) Tally counts ===

low_count=0
medium_count=0
high_count=0
unspecified_count=0

for rule in "${sorted[@]}"; do
  if [[ " ${LOW_RULES[*]} "    =~ " ${rule} " ]]; then
    (( low_count++ ))
  elif [[ " ${MEDIUM_RULES[*]} " =~ " ${rule} " ]]; then
    (( medium_count++ ))
  elif [[ " ${HIGH_RULES[*]} "   =~ " ${rule} " ]]; then
    (( high_count++ ))
  else
    (( unspecified_count++ ))
  fi
done

# === 4) Output the four counts ===

printf "<result>Low: %d\nMedium: %d\nHigh: %d\nUnspecified: %d</result>" \
  "$low_count" "$medium_count" "$high_count" "$unspecified_count"
```
