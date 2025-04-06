---
title: "macOS Compliance Documentation: A Workflow with the macOS Security Compliance Project and Jamf Compliance Editor"
date: 2025-04-06
description: "Learn how to document macOS compliance baselines using mSCP and Jamf Compliance Editor. Build audit-ready workflows with tailored spreadsheets and guidance."
tags: [macOS, Compliance, Security, CIS, DISA STIG, Conference, NIST, mSCP]
categories: [Mac Management]
pin: false
---
![Banner Image for macOS Compliance Documentation](/assets/img/postimages/Banner_msCP_Documentation.png){: lqip="/assets/img/postimages/Banner_msCP_Documentation.png" }

# A Practical Workflow for Tailoring and Documenting macOS Compliance with mSCP and JCE

Many organizations are required to implement and enforce a cybersecurity benchmark or baseline. Often, these benchmarks—such as CIS or NIST 800-53—require tailoring to meet specific organizational needs. The [macOS Security Compliance Project (mSCP)](https://github.com/usnistgov/macos_security) provides a framework for creating and managing security baselines for macOS, and tools like [Jamf Compliance Editor (JCE)](https://www.jamf.com/blog/macos-security-compliance-project-made-easy/) make the process more accessible for Mac Admins.

While resources abound for customizing and implementing baselines using mSCP, I wanted to add a perspective focused specifically on **macOS compliance documentation** for internal use, security reviews, and audits. This post outlines my documentation workflow for macOS compliance policies using the macOS Security Compliance Project (mSCP), Jamf Compliance Editor (JCE), and a structured **macOS compliance spreadsheet** process.

> Assumption: This post assumes you're familiar with mSCP and have reviewed Apple's [Mac Security Compliance Training](https://it-training.apple.com/tutorials/apt-compliance/). I’ll also be using Jamf Compliance Editor for demonstration.
{: .prompt-warning }

Also note, this blog is meant to show **one** way to address documenting changes, but it is not intended to be a definitive *best way*. It is highly recommended to review the Apple Training, and other resources that are available relating to the macOS Security Compliance Project, and take into account how your own organization operates.
> 

---

## Baseline Customization Overview

Apple’s training (Chapter 2: *Implementing Your Compliance Strategy*) contains two sections that are especially useful:

- **Customizing Values in a Baseline Approach**
- **Customizing Rules in a Risk-Based Approach**

Organizations often make **Risk-Based Decisions (RBDs)** to deviate from benchmarks by choosing not to enforce certain rules. When doing so, it's essential to document which rules are excluded and why.

I start by generating a default, non-customized baseline in JCE—for demo, I’ll be using CIS Level 1 & 2—as a working draft. This serves as the foundation for collaboration with internal security and compliance teams.

---

## Using Jamf Compliance Editor for Tailored Baselines

### Step 1: Share the Baseline Spreadsheet

Jamf Compliance Editor (JCE) automatically generates a macOS compliance spreadsheet for each baseline. I share this spreadsheet with stakeholders, allowing them to:

- Mark rules as "Go" or "No-Go"
- Provide justifications
- Use color coding, comments, and notes

![Sharing the JCE-generated spreadsheet with the security and compliance teams.](/assets/img/postimages/mSCP_Excel_Baseline.png)

### Step 2: Customize in JCE

Once decisions are finalized:

1. Open JCE for the baseline of your choice.
2. (Optional) Within JCE, select "Show All" to include optional rules not present in the benchmark (e.g., iCloud restrictions).

![Jamf Compliance Editor showing optional rules under ‘Select All’](/assets/img/postimages/Customize_JCE_01.png)

When viewing all rules in a CIS-based baseline, you'll notice that non-CIS rules are identifiable by the absence of a CIS ID number in their titles. 

1. Deselect rules the organization has opted not to enforce.

![Deselecting rules from the tailored baseline within JCE.](/assets/img/postimages/Customize_JCE_02.png)

1. Click **Create Guidance** and name the tailored baseline aligned with your organization’s CIS compliance strategy:
    `patchnotes_cis_lvl2_**tailored**` 
    
    ![Creating tailored guidance using JCE.](/assets/img/postimages/JCE_Save_Tailored.png)
    
2. Save settings as a `.jce` file for future editing:
     `patchnotes_cis_lvl2_tailored.jce` 
    
![Saving the tailored baseline as a .jce file](/assets/img/postimages/JCE_Save_Button.png)

When saving your .jce file, ensure that you place it in a folder where you can easily find it. I place mine in the primary project directory, in this case, the macos_security-sequoia folder. 

JCE generates the scripts, profiles, and `.plist` files required for deployment.

However, I want to ensure I document the rules we did not include nicely and cleanly so documentation can be provided to auditors and other stakeholders. In my opinion and experience, sharing a spreadsheet with all available data has worked best, compared to sharing a PDF or HTML page.

---

## Creating an Exempted Baseline for Documentation

After tailoring your macOS security baseline, it’s equally important to document the rules your organization chooses not to enforce. This step helps with audit readiness and internal clarity.

To document rules that were excluded, I reverse the process:

![Deselecting all enforced rules for the exempted baseline.](/assets/img/postimages/Customize_JCE_03.png)

1. Start by deselecting all the previously **enforced** rules.  *(Highlighted in red)*.
2. Then, reselect only the *excluded* rules you want to document. *(Highlighted in yellow)*.
3. Create a new baseline:
    `patchnotes_cis_lvl2_**exempted**`
    
4. Save the file as:
    `patchnotes_cis_lvl2_exempted.jce` 
    
![Naming the exempted baseline settings in JCE.](/assets/img/postimages/JCE_Saveed_Finder_Exempted.png)

> Note: This post focuses on broad enforcement decisions and organizationally wide exemptions or exclusions. If you’re managing exemptions for specific departments or user groups—rather than organization-wide exclusions—check out the linked resources at the end of this post. Several articles walk through scoped exemptions and targeted configurations using JCE and mSCP.
{: .prompt-info }

Now I have two macOS compliance baselines—one showing enforced rules, and another detailing exempted controls for audit purposes.

![Finder window showing the complete build for both tailored and exempted baselines.](/assets/img/postimages/Finder_build_directory.png)

---

## Combining and Annotating Compliance Spreadsheets

I open both spreadsheets side-by-side:

- Rename **Sheet 1** in each:
    - Tailored spreadsheet ➝ `Enforced`
    - Exempted spreadsheet ➝ `Exempted`
- Move the `Exempted` sheet into the `Tailored` spreadsheet, creating a unified Excel file.

![Selecting ‘Move or Copy…’ within Excel in order to move from Exempted to Tailored.](/assets/img/postimages/mSCP_Excel_MoveOrCopy.png)
_Select ‘Move or Copy…’ within Excel in order to move from Exempted to Tailored._

![Doing this will result in a combined excel spreadsheet that shows the enforced and non-enforced rules in one document.](/assets/img/postimages/mSCP_Excel_MoveToEnd.png)
_Doing this will result in a combined excel spreadsheet that shows the enforced and non-enforced rules in one document._

Within now combined spreadsheet the `Exempted` sheet, I annotate:

- Decision justifications
- Reviewer names
- Dates
- Rule modification notes

![Spreadsheet showing example ‘justifications’ for exempted rules within the ‘Modified Rule’ column](/assets/img/postimages/mSCP_Excel_ModifiedRule.png)

This results in a polished, auditor-friendly **macOS compliance documentation file** that supports security reviews and audit readiness.

---

## Optional: Multi-OS Version Tracking

In some cases, I combine **multi-version macOS baselines** (e.g., Ventura, Sonoma, Sequoia) into a single spreadsheet by:

- Adding columns for each OS version in both the `Enforced` and `Exempted` sheets, indicating applicability per rule
- Mapping rules to their corresponding CIS IDs manually.
*Example `os_anti_virus_installed` is CIS 5.11 in Ventura, but CIS 5.10 in Sonoma and Sequoia. The same applies for other baselines like DISA STIG.*

While manually mapping these differences across OS versions is time-consuming, it greatly simplifies audit reviews by consolidating compliance evidence into one unified document. I feel the effort is well worth the outcomes and leads to fewer questions from auditors when they also reference documentation direct from CIS.

The good news is that the macOS Security Compliance Project (mSCP) Team is working on version 2, where unifying baselines and benchmarks across operating systems versions is a significant priority. That will hopefully make documenting baselines across all of your supported OS versions a single process automatically built into the creation of your compliance baseline! You can read more about version 2.0 in my recent blog post: [Shaping the Future of macOS Security Compliance: Highlights from the First mSCP Developer Conference](https://tonyyo11.github.io/posts/mSCP-Developers-Conference-Recap/#whats-coming-in-mscp-20) 

---

## Final Thoughts & Community Engagement

Over the years, I’ve helped organizations from the U.S. and Canada, to the UK, Australia, and New Zealand build and tailor **macOS Security Compliance Project (mSCP)** baselines using **Jamf Compliance Editor (JCE)**. I’ve seen many approaches to **macOS compliance documentation**, and I’m always eager to learn new ones.

How do you document compliance policies in your organization? Did this workflow spark any new ideas or improvements for your process?

Let me know in the comments (GitHub account required) or reach out on the [Mac Admins Slack](https://macadmins.slack.com/).

---

## Resources

- [NIST SP 800-219: Automated Secure Configuration Guidance](https://csrc.nist.gov/publications/detail/sp/800-219/final)
- [mSCP Exemptions Wiki](https://github.com/usnistgov/macos_security/wiki/Exemptions)
- [Apple’s official guide to the macOS Security Compliance Project (mSCP)](https://support.apple.com/guide/certifications/macos-security-compliance-project-apc322685bb2/web)
- [Apple IT Training: Preparing to Use mSCP](https://it-training.apple.com/tutorials/compliance/sec010/)
- [Year of The Geek: Running audit and remediation on multiple OSes with mSCP and Jamf Pro](https://yearofthegeek.net/posts/running-audit-and-remdiation-on-multiple-os/)
- [Jon Brown: JCE Exemptions Setup](https://jonbrown.org/blog/how-to-setup-exemptions-with-jamf-compliance-editor/)
- [Jamf: mSCP Made Easy](https://www.jamf.com/blog/macos-security-compliance-project-made-easy/)

---

### 💡 mSCP Tip of the Day

**Auditing your own system** – After you’ve built your tailored baseline, I encourage you to run the compliance script on your own corporate Mac without any flags. This allows the **macOS Security Compliance Project (mSCP)** to run in interactive audit mode, where you can “Run New Compliance Scan” to audit your own system’s state of compliance before deploying any type of configuration profiles or enforcing the remediation script. 

![image.png](/assets/img/postimages/Terminal_mSCP_Menu.png)

Within the `scripts/util` folder of the macOS Security Compliance Project, you will find a `mscp_local_report.py` script, which will generate an Excel and HTML document that shows a pie chart based on your current **macOS security configuration** and a table with the rule name and result for your specific system. This can show you, percentage-wise, where your system stands within your baseline before you begin deployment of profiles or fixing rules via the compliance script; then, you can repeat the process once all items are deployed to get a before and after snapshot!

![image.png](/assets/img/postimages/mSCP_Report.png)

---

Happy auditing!
