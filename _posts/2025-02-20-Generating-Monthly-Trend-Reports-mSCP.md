---
title: "Generating Monthly Trend Reports for mSCP Compliance in Jamf Pro"
date: 2025-02-20
tags: [jamf, macos, reporting, scripting, python, mSCP]
categories: [Mac Management]
pin: false
---

![2025-02-20-Generating-Monthly-Trend-Reports-mSCP](/assets/img/postimages/2025-02-20-Generating-Monthly-Trend-Reports-mSCP.png)

### **Introduction**  
Many Jamf Pro shops rely on automated report generation for asset tracking, billing, and security compliance. Throughout my career, I have leveraged Jamf Pro’s built-in SMTP integration to send scheduled reports via email, providing my own, and other cross-functional teams with critical insights on device status and compliance.  

While security teams can forward compliance data to a SIEM, and asset managers can integrate with ServiceNow via the Jamf API, many organizations still rely on standard Jamf-generated reports. However, one major limitation of these reports is the lack of time-based historical insights—Jamf Pro does not offer native trend reporting.  

This gap makes it challenging to track compliance improvements or regressions over time, particularly when using frameworks like the [macOS Security Compliance Project (mSCP)](https://github.com/usnistgov/macos_security), which audits against standards such as CIS, DISA STIG, and NIST.  

To address this, I created a Python script that consolidates weekly Jamf Pro reports into a Monthly Trend Report. This provides upper management and security teams with a clear view of compliance progress over time.  

📌 **You can find the script on GitHub here:**  [Jamf Compliance Report Monthly Script](https://github.com/tonyyo11/MacAdministration/blob/main/Scripts/jamf-compliance-report-monthly.py)  

---

## **Why Time-Based Compliance Tracking is Critical**  
While point-in-time reports are useful, security teams need historical compliance data to:  
✔ Identify trends in security baseline adherence.  
✔ Highlight improvements or regressions in compliance.  
✔ Justify policy adjustments and resource allocation.  
✔ Provide evidence of security progress to auditors and executives.  

Jamf Pro does not offer a built-in way to compare multiple compliance reports over time, making it difficult to see whether your fleet’s security posture is improving or worsening.  

---

## **How the Python Script Works**  
### **Overview**  
This script processes multiple Jamf compliance reports to create a **consolidated monthly report** with trend analysis.  

### **Workflow:**  
1. **Weekly Reports:** The script reads weekly compliance reports exported from Jamf Pro.  
2. **Data Extraction:** It parses critical compliance fields such as:  
   - **Failed mSCP Result List**  
   - **Failed Results Count**  
   - **mSCP Version in Use**  
3. **Data Normalization:** Converts CSV reports into a structured format.  
4. **Trend Analysis:** Aggregates failed compliance checks by week.  
5. **Report Generation:** Outputs a **final Excel Spreadsheet** showing compliance progress over the month.  

---

## **Example Setup in Jamf Pro**  
Note: Assumptions are made that you, as the reader, understand how to generate Jamf Pro reports and schedule regularly occurring reports sent via email.

In Jamf Pro we have already established and deployed our mSCP baseline. In this example, it will be the DISA STIG. We also have Jamf Extension Attributes running to track the Failed mSCP Result List, Failed Results Count, and what version (or in the case, which baseline) a system is using. In Jamf, the Display names for these items are **Compliance - Failed mSCP Result List**, **Compliance - Failed mSCP Results Count**, and **Compliance - mSCP Version**, respectively.

### **Prerequisites**  
✔ Python 3.9+ installed  
✔ Required Python modules: `pandas`, `numpy`, `matplotlib`  
✔ Jamf configured with an SMTP Server for scheduled email reporting

### **Step 1: Create an Advanced Computer Search**  
In Jamf Pro, create an **Advanced Computer Search** named **"Compliance Audit Report"** 
![jamf_AdvSearch_ComplianceReport](/assets/img/postimages/jamf_AdvSearch_ComplianceReport.png)

Configure your search with the following criteria:  
- **Compliance - Failed mSCP Results Count IS 0**  
- **OR Compliance - Failed mSCP Results Count MORE THAN 0**  
It is set so that you do not capture empty listings. You cannot set the extension attribute criteria to "IS NOT (null)," as this will produce unwanted results. 
![jamf_AdvSearch_Criteria](/assets/img/postimages/jamf_AdvSearch_Criteria.png)

### **Step 2: Configure Display Fields**  
Ensure your report includes the following extension attributes:  
✔ **Compliance - Failed mSCP Result List**  
✔ **Compliance - Failed mSCP Results Count**  
✔ **Compliance - mSCP Version**  
✔ **Any additional system info (e.g., serial number, OS version, department, etc.)**  

### **Step 3: Automate Report Delivery**  
- Schedule the report to be **emailed weekly** to a designated mailbox.  
- (Optional) I have additional workflows through Power Automate to take the reports from my inbox and automatically save them to a Network Share (OneDrive) and rename the file the following naming convention:
  ```
  YYYY-MM-DDTHH_MM_SS - Report - Compliance Audit Report.csv
  ```  
- Example filenames:
  ```
  2025-01-01T00_00_01 - Report - Compliance Audit Report.csv
  2025-01-07T05_22_01 - Report - Compliance Audit Report.csv
  2025-01-14T19_21_01 - Report - Compliance Audit Report.csv
  ```

---

## **How the Script Processes Reports**  
Once the reports are collected in a folder, the script:  
1️⃣ **Loads CSV Files** from the specified directory.  
2️⃣ **Parses and Cleans Data** by removing duplicate records and normalizing compliance results.  
3️⃣ **Aggregates Weekly Data** to track trends in security compliance.  
4️⃣ **Exports a Consolidated Monthly Report** in CSV format.  


---

## **Example Output: Monthly Compliance Trend Report**  
*Data below is randomly generated and do not belong to any particular organization or Jamf Pro Server*

| Serial Number         | Computername   |   2025-01-01 |   2025-01-07 |   2025-01-14 |   2025-01-21 |
|:----------------------|:---------------|-------------:|-------------:|-------------:|-------------:|
| RC2DC83ZI6            | MacPro-BMNJ    |          9   |         18   |       36     |       86     |
| W5TFT9L4YR            | MacStudio-YGE8 |         13   |         46   |       82     |       18     |
| BMWGVVRCT0            | iMac-WVK2      |         98   |         79   |       70     |       22     |
| W6ALH0489G            | iMac-P89E      |        116   |         15   |       51     |       24     |
| K5AQTAIF7R            | MacStudio-2P2Z |         81   |         29   |       63     |       13     |
| 3MTXVAYCMM            | iMac-VG9X      |         33   |          1   |       86     |       69     |
| MEBIDFLI53            | MacStudio-PUFR |         80   |         31   |       70     |       28     |
| 8IU1MZWJPS            | iMac-AR9N      |         86   |         97   |       19     |       81     |
| Average Failed Checks |                |         64.5 |         39.5 |       59.625 |       42.625 |

In addition to this, the individualized reports (ie. 2025-01-01T00_00_01 - Report - Compliance Audit Report.csv) are all included as individual sheets within the overall Excel workbook so that teams can drill down to see what mSCP rules a given system is failing.
---

## **Benefits of Using This Trend Report**  
✔ **Improved Visibility**: Track compliance changes weekly/monthly.  
✔ **Data-Driven Decisions**: Justify security investments and adjustments.  
✔ **Historical Compliance Tracking**: Easily compare against past reports.  
✔ **Audit Readiness**: Provides documented compliance trends for external reviews.  

---

## **Next Steps**  
**Download and Try the Script** from GitHub: [Jamf Compliance Report Monthly Script](https://github.com/tonyyo11/MacAdministration/blob/main/Scripts/jamf-compliance-report-monthly.py)  
Be sure to edit the script based on your organizational needs. Change `input_directory` to the Folder Name where you will store reports. `Change output_file` based on your baseline and naming needs.
**Provide Feedback!**  
- Feel free to contribute to the script, suggest improvements, or report issues.  

---

## **Final Thoughts**  
By implementing **automated compliance trend reporting**, IT teams can be empowered to **proactively manage security** and demonstrate **continuous improvement in macOS compliance**.  

This approach bridges the gap between **point-in-time security snapshots** and **long-term compliance tracking**, enabling organizations to make **better, data-driven security decisions**.  

Have you implemented similar compliance tracking solutions? Let me know on the **Mac Admins Slack**: @tonyyo11\ or reach out on GitHub! 🚀
