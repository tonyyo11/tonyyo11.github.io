---
title: "Visual Studio Code (VS Code) Extension Tracking with Jamf Pro"
date: 2025-02-26
tags: [jamf, macos, vscode, developers, visual studio code]
categories: [Mac Management]
pin: false
---
![2025-02-26-Tracking-VSCode-Extensions-macOS](/assets/img/postimages/2025-02-26-Tracking-VSCode-Extensions-macOS.png)

Visual Studio Code (VS Code) is a widely used code editor with a vast extension marketplace. However, certain extensions can pose security risks, such as the `equinusocio.vsc-material-theme` and `equinusocio.vsc-material-theme-icons`, which were removed from the marketplace due to being used to distribute malware ([source](https://news.ycombinator.com/item?id=43178831)). Organizations using VS Code should track installed extensions to prevent security threats. Microsoft has indicated that they have automatically attempted to remove vulnerable extensions from devices - but community conversations indicate that it may not be successful.

In this post, we will create a Jamf Pro Extension Attribute (EA) to track installed VS Code extensions on macOS devices. Using this EA, we can build Smart Groups to identify machines with potentially harmful extensions.

### **Step 1: Create a Jamf Pro Extension Attribute**
We will use a script to retrieve installed VS Code extensions and report them to Jamf Pro.

#### **Script for Extension Attribute**
```bash
#!/bin/bash

###################################################################################################
# Script Name:    [installedVSCodeExtensions.sh]
# By:            Tony Young
# Organization:   Cloud Lake Technology, an Akima company
# Date:          February 26, 2025
# 
# Purpose:       Retrieve installed extensions within the Visual Studio Code application for macOS
# Disclaimers:   Code originally written by @Fraser via the Mac Admins Slack Community - https://www.macadmins.org edited by Tony Young
###################################################################################################
# Set Initial Result
result="Not installed"

# Run as the current logged in user to grab their extensions
loggedInUser=$( echo "show State:/Users/ConsoleUser" | scutil | awk '/Name :/ { print $3 }' )
codePath="/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code"

# Check VS Code for installed extensions and include the current version installed
if [[ -e "${codePath}" ]]; then
  result=$(sudo -u "${loggedInUser}" "${codePath}" --list-extensions --show-versions)
fi

# If no extension found, return as such
if [[ -z "${result}" ]]; then
  result="No extensions found"
fi

# Return result for Jamf Pro EA
echo "<result>${result}</result>"
```
View the most current version of this code on [Gist.](https://gist.github.com/tonyyo11/ddb26f2834817512c1fe1b1402aded20)

1. Save this script.
2. Create a new Extension Attribute within Jamf Pro. Set the Display name to something appropriate. For Example: Visual Studio Code Installed Extensions or something shorter like CS Code Extensions.
3. Add a description, then Set the Data Type to `String` and Input Type to `Script`.
![VSCode_Ext_Overview](/assets/img/postimages/VSCode_Ext_Overview.png)
4. Paste the code snippet into the Script section
![VSCode_Ext_Script](/assets/img/postimages/VSCode_Ext_Script.png)
5. Enable and Save changes.

### **Step 2: Create Smart Groups in Jamf Pro**
After adding the Extension Attribute, create Smart Groups to track devices with specific extensions.

#### **Smart Group Criteria**
1. **Group Name:** `VS Code - Material Theme Installed`
   - Criteria: `Visual Studio Code Installed Extensions` **contains** `equinusocio.vsc-material-theme`
   - OR Criteria: `Visual Studio Code Installed Extensions` **contains** `equinusocio.vsc-material-theme-icons` 
![VSCode_SmartGroup_Criteria](/assets/img/postimages/VSCode_SmartGroup_Criteria.png)

### **Step 3: Automate Remediation (Optional)**
To automatically remediate the issue:
1. Deploy a Jamf Pro policy with a script to remove these extensions.
   - This should be scoped to the Smart Group created above. 

#### **Removal Script**
```bash
#!/bin/bash

# Get the current logged-in user and their home directory
CURRENT_USER=$(stat -f %Su /dev/console)
USER_HOME=$(eval echo ~$CURRENT_USER)

rm -rf "$USER_HOME/.vscode/extensions/equinusocio.vsc-material-theme*"
rm -rf "$USER_HOME/.vscode/extensions/equinusocio.vsc-material-theme-icons*"
echo "Malicious extensions removed."
```
View the most current version of this code on [Gist.](https://gist.github.com/tonyyo11/215069b5657e743387b67f2c3b255e50)

### **Conclusion**
Tracking VS Code extensions using Jamf Pro helps ensure security compliance and remove the installation of unsafe extensions. IT teams can use the Extension Attribute and Smart Groups to monitor, detect, and remediate security risks in their macOS environment.



