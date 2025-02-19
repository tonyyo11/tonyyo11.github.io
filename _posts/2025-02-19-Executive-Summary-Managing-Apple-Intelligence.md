---
title: "Executive Summary: Apple Intelligence Management in macOS 15 Sequoia"
date: 2025-02-19
tags: [jamf, macos, configuration profiles, security, apple intelligence, genai]
categories: [Mac Management]
pin: false
---
## Executive Summary: Apple Intelligence Management in macOS 15 Sequoia  

With the launch of **macOS 15 Sequoia**, Apple Intelligence introduces a range of AI-enhanced features designed to boost user productivity and improve the overall experience. Nevertheless, organizations aiming to **completely turn off Apple Intelligence** will not discover a universal **“disable all”** key within Mobile Device Management (MDM). Instead, they need to implement individual settings to limit particular functionalities like **image generation, writing support, email summarization, and external AI integrations** like ChatGPT.  

Despite applying these controls, the **Apple Intelligence toggle in System Settings remains visible** to users, who can toggle the feature suite on. 

![AppleIntelligence_Enabled](/assets/img/postimages/AppleIntelligence_Enabled.png)

This can lead to a perception that Apple Intelligence is enabled when, in reality, all critical features have been **functionally disabled** through MDM policies. If a user attempts to turn on Apple Intelligence, the system will appear operational but will lack access to AI-powered capabilities due to the organization’s enforced restrictions.  

From a security and compliance standpoint, IT administrators **retain control over Apple Intelligence** through MDM, ensuring that sensitive data does not interact with unauthorized AI services. Organizations attempting to enforce and comply with cybersecurity standards and benchmarks such as DISA STIG, the NIST 800-171, and others, can properly follow the guidance of these security organizations relating to the disablement of Apple Intelligence functionality.While Apple’s design decision to leave the toggle accessible may cause confusion, **it does not override enterprise restrictions**. End-users may perceive they can enable Apple Intelligence, but in practice, **the AI features remain inactive** across managed devices.  

The **Mac Admins community** has been instrumental in **exploring and documenting the limitations of Apple Intelligence management**, with technical insights provided in two recent blog posts by Bob Gendler: [*"Raising Your IQ on Apple Intelligence"*](https://boberito.medium.com/raising-your-iq-on-apple-intelligence-380933894340) and [*"From Smart to Smarter: Elevating Apple IQ Even More"*](https://boberito.medium.com/from-smart-to-smarter-elevating-apple-iq-even-more-c864cebb70c9). These findings highlight the ongoing work of IT professionals in **understanding and mitigating the challenges posed by new Apple platform changes**.  

To address potential concerns from stakeholders or security teams, IT can provide visibility into **which Apple Intelligence settings have been disabled, ensuring compliance with corporate policies**. Additionally, employee communication may be necessary to clarify that Apple Intelligence remains non-functional within the organization even if the toggle can be switched on.  

This approach allows organizations to benefit from **granular control over Apple Intelligence** without compromising security or compliance. It ensures that AI-powered features remain restricted while maintaining a seamless user experience.

---

### **Template Usage for Mac Admins**  

This executive summary may be used as a **template** by Mac Admins to communicate with **executives, security teams, and other stakeholders** regarding their organization's approach to managing Apple Intelligence. Admins are encouraged to **customize** this summary with organization-specific details, such as **security policies, compliance requirements, or additional technical justifications**. By leveraging this template, Mac Admins can ensure that leadership teams clearly understand how Apple Intelligence is being managed—despite the visibility of the toggle in **System Settings**.

ℹ️ **Please Note:** You can attempt to block the System Setting Pane and prevent users from even being able to reach the switch to enable Apple Intelligence which is managed via `com.apple.systempreferences` but as Bob Gendler calls out in his posts:
> It turns out the System Settings Pane for Apple Intelligence & Siri will block the first time launched after the profile is installed, but not after that. While this has been deprecated since macOS 13.0, this has been a critical tool for Mac Admins to restrict access to certain settings for over a decade or more. This in fact still works fine for other System Settings panels, just not Apple Intelligence & Siri. HOWEVER, System Settings Panes are individual binaries that can be blocked. Restricting the exact process SiriPreferenceExtension using something like Restricted Software in Jamf or Application Blocking in Kandji will block it.
![jamf_Restrictions_AI](/assets/img/postimages/jamf_Restrictions_AI.png)

![jamf_Deployed_AIRestrictions](/assets/img/postimages/jamf_Deployed_AIRestrictions.png)

