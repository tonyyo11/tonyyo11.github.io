---
title: "Enhancing Monthly Trend Reports with Jamf Pro and mSCP - Version 2.1"
date: 2025-03-23
description: "A follow-up on improving and streamlining the compliance trend report script using Jamf Pro reports and the macOS Security Compliance Project (mSCP)."
tags: [Jamf, macOS, Compliance, Automation, Security, reporting, scripting, python, mSCP]
categories: [Mac Management]
pin: false
---

![2025-03-23-mSCP-Trends-v2.1](/assets/img/postimages/2025-03-23-mSCP-Trends-v2.1.png)

## Introduction

In my previous [blog post](https://tonyyo11.github.io/posts/Generating-Monthly-Trend-Reports-mSCP/), Generating Monthly Trend Reports for mSCP Compliance in Jamf Pro, I detailed how I automated generating monthly compliance trend reports using Jamf Pro reports and the macOS Security Compliance Project (mSCP). Since then, and just in time for the [macOS Security Compliance Project Developer Conference](https://www.nist.gov/news-events/events/2025/03/macos-security-compliance-project-developer-conference) being held between March 25th and 26th, 2025, I’ve improved the script to be more user-friendly and adaptable for different environments. This post highlights the key changes in **Version 2.1** of the script and how these updates make it easier for others to use and customize it for their specific needs.

---

## Key Changes in Version 2.1

### 1. **Configurable Variables for Easy Customization**

One of the most significant updates is the introduction of configurable variables at the beginning of the script. Previously, much of the logic was hardcoded for my specific workflow, but now you can quickly adjust key parameters without modifying the script's core functionality.

### New Configurable Parameters:

- `INPUT_DIR` : Defines where the script will look for Jamf Pro compliance reports.
- `OUTPUT_FILE_PREFIX` : Sets the filename prefix for generated reports.
- `TIMEFRAME_DAYS` (optional): Filters data based on a specific timeframe.
- `COMPLIANCE_THRESHOLDS` (optional): Defines threshold levels for compliance status (e.g., Pass, Low, Medium, High).

These changes make adapting the script to various organizational needs easier without deep diving into the code.

---

### 2. **Dynamic File Handling Based on Metadata**

Previously, I relied on renaming files via Power Automate and sorting them based on filenames. I implemented a Power Automate flow to take Jamf Pro reports from my mailbox, rename them, and then place them into a Sharepoint directory. I built the original script around this workflow.

Now, the script dynamically scans the `INPUT_DIR` and sorts reports based on **file creation metadata** instead. This eliminates the need for manual renaming and allows the script to work with raw Jamf Pro report exports without modifications.

**Impact:**

- No need to modify filenames.
- Works with default Jamf Pro reports without additional processing.
- Reduces the chance of errors caused by inconsistent naming conventions.

---

### 3. **Improved Excel Formatting and Conditional Styling**

I’ve updated the default color palette to be more accessible to anyone who will be reviewing the trend report, using colorblind-friendly options.

![mscp_demo_spreadsheet.png](/assets/img/postimages/mscp_demo_spreadsheet.png)

All Excel formatting settings (column widths, conditional formatting, colors, and thresholds) are now centralized at the top of the script to enhance readability. Users can easily adjust the styling to fit their needs.

For example, if your organization uses different ranges for compliance levels, you can modify:

```python
COMPLIANCE_THRESHOLDS = {
    "pass": (0, 0),            # 0 failures = Pass
    "low": (1, 10),            # 1-25 failures = Low
    "medium": (11, 30),        # 26-50 failures = Medium
    "high": (31, float("inf")) # >50 failures = High
}

```

Or adjust colors:

```python
PIE_CHART_COLORS = {
    "pass": "#4CAF50",
    "low": "#FFEB3B",
    "medium": "#FF9800",
    "high": "#F44336",
}

```

---

### 4. **New Compliance Chart and PNG Export**

Another major enhancement is the addition of a compliance **trend chart** inside the Excel report. This helps visualize compliance trends over time.

Additionally, the script now exports a **standalone PNG image** of the compliance chart, allowing you to easily integrate it into presentations, emails, or dashboards.

**Example Chart Output:**

![mSCP_Compliance_Report_donut_20250323_053310.png](/assets/img/postimages/mSCP_Compliance_Report_donut_20250323_053310.png)

---

## What’s Next for Version 2.2?

Looking ahead, the next update will focus on improving how the script parses Jamf Pro reports dynamically. Currently, it assumes a specific column structure, but future versions will intelligently detect and adjust to variations in report formats.

Planned Improvements:

- More robust handling of different Jamf Pro report structures.
- Auto-detection of column headers for greater flexibility.
- Additional data visualization enhancements.

## Further Enhancements Down The Road

I am actively paying attention to Jamf’s Compliance reporting feature (currently in beta and only supports CIS Level 1 and Level 2 for now - **Cloud only**). I presently do not see an option to export reports through the Compliance GUI, or schedule regular reports to be emailed, but the built-in tool does automatically generate the extension attributes needed to then create an advanced computer search for reports. That being said, I have proactively created a Jamf Feature Request here to implement the core concepts of my script into the tooling since the feature is exclusive to cloud-hosted environments. 

---

## How You Can Customize It Further

As with all open-source scripts, this version is designed to be modified based on your specific compliance and reporting needs. Here are some common adjustments you might consider:

1. **Changing Compliance Categories:**
    - Adjust **COMPLIANCE_THRESHOLDS** for different risk categorizations.
    - Modify **PIE_CHART_COLORS** to match your organization’s color schemes.
2. **Expanding Compliance Levels:**
    - Instead of just Pass, Low, Medium, High, you might need more granular levels like:
        
        ```python
        COMPLIANCE_THRESHOLDS = {
            "pass": (0,0),
            "low": (1, 10),
            "moderate-low": (11,20),
            "moderate": (21, 40),
            "moderate-high": (41, 50),
            "high": (51, float("inf")),
        }
        
        ```
        
3. **Adjusting Excel Formatting:**
    - Change column widths or wrap text settings in:
        
        ```python
        sheet.set_column('A:A', 20)  # Computer Name
        sheet.set_column('B:B', 15)  # Serial Number
        sheet.set_column('H:H', 50, wrap_format)  # Failed List
        
        ```
        
4. **Integrating with Other Systems:**
    - You can automate report uploads to SharePoint, Slack, or an internal dashboard by adding a few extra lines of Python code.

---

## Try It Out!

You can download the updated script from GitHub:  [**jamf_compliance_report_monthly_v2.1.py**](https://github.com/tonyyo11/MacAdministration/blob/main/Scripts/jamf_compliance_report_monthly_v2.1.py)

Test the script with some **Demo Data** found here:  [**mSCP Demo Data**](https://github.com/tonyyo11/MacAdministration/tree/main/Docs/mSCP%20Demo%20Data)

Download the four “**Compliance_Report-<DATE>.csv**” files. 

You’ll want to adjust the creation dates of the demo data files. When downloading from Github, the creation date will be set to the date you as the reader have download said files. This will result in all of the demo data being marked as being created on the same exact date and cause problems when running the script.

![Demo_Report_CreationDate.png](/assets/img/postimages/Demo_Report_CreationDate.png)

If you haven't already, you'll need to install Xcode Command Line Tools.
To install the Xcode CLI Tools, run the following command in terminal:

```bash
xcode-select --install
```

Once done, you can edit the creation dates of the demo files.
You can do so by running a command such as:

```bash
SetFile -d "MM/DD/YYYY hh:mm:ss" /path/to/your/file
```

Example:

```bash
% SetFile -d "02/24/2025 08:21:00" ~/Downloads/mSCP_Demo_Data/mSCP_Demo_Data_Passing/Compliance_Report_2025-02-24.csv
```

![Demo_Report_CreationDateEdited.png](/assets/img/postimages/Demo_Report_CreationDateEdited.png)

Repeat this process for all of the files; the file names have a demo date set in order for it to be easier when updating their creation date. Place the files into a dedicated folder such as created a `/mSCP_Demo` folder. 

Update the compliance report script, changing the `INPUT_DIR` to your downloaded demo data, and then, run the [jamf_compliance_report_monthly_v2.1.py](https://github.com/tonyyo11/MacAdministration/blob/main/Scripts/jamf_compliance_report_monthly_v2.1.py) script on the demo data and see the results!

---

## Final Thoughts

This update makes the compliance report generation process **more flexible, automated, and user-friendly**. Whether you’re a Jamf Pro admin looking to streamline security reporting or an IT engineer trying to improve compliance monitoring, this new version should be significantly easier to use and customize.

Stay tuned for **Version 2.2**, which will further enhance report parsing and automation!

**Let me know how you’re using this script and what features you’d like to see next!**

---

### ⌘ Mac Tip of the Day:

**Use Quick Look to Preview Files Instantly!** Press **Spacebar** on any selected file in Finder to preview it without opening an app. Works great for PDFs, images, and even some code files!
