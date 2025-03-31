---
title: "Shaping the Future of macOS Security Compliance: Highlights from the First mSCP Developer Conference"
date: 2025-03-31
description: "A Mac Admin Community Perspective on Collaboration, Recognition, and the Road to mSCP 2.0."
tags: [macOS, Compliance, Security, CIS, DISA STIG, Conference, NIST, mSCP]
categories: [Mac Management]
pin: false
---
![Banner Image for Shaping the Future of macOS Security Compliance Post](/assets/img/postimages/2025-03-31-mSCP-Conference-Recap-Banner.png)
## A Mac Admin Community Perspective on Collaboration, Recognition, and the Road to mSCP 2.0

This month (March, 2025), I had the opportunity—and true honor—to attend the inaugural **macOS Security Compliance Project (mSCP) Developer Conference** hosted by the **National Institute of Standards and Technology (NIST).** Taking place at the National Cybersecurity Center of Excellence (NCCoE) in Rockville, Maryland, this was the first official developer event centered around mSCP. It brought together a diverse and passionate group of attendees including MDM vendors, security experts, security product vendors, government representatives and officials, and everyday users of the project like myself. The event left me both inspired and deeply reflective about the future of macOS security compliance and the role our community plays in it.

The conference wasn’t just a retrospective—it was a look forward into the projected multi-year roadmap of mSCP 2.0 and what it means for developers, vendors, and Mac Admins like you and me.

![Bob Gendler of NIST presenting at the mSCP Developer Conference](/assets/img/postimages/BobGendler_Speaker.jpg)

---

## What's Coming in mSCP 2.0?
![Section Banner - What's Coming in mSCP 2.0](//assets/img/postimages/GettingToV2.png)

Here’s a taste of what’s ahead:

- **GitHub Branch Unification**: No more switching between Sequoia and Sonoma branches to build platform-specific compliance scripts. Future releases aim to streamline this process, saving time and effort.
- **YAML Structure Improvements**: For those who actually read through the YAML files within the project, expect improvements in how YAML rules and categories are defined. There was serious energy around how the data structure can better serve admins and tool developers.
- **Vendor Manifest Export File**: This will allow third-party vendors to integrate mSCP logic directly into their tools—simplifying audit and remediation workflows and opening the door for vendors to support highly customized and tailored organizational baselines and benchmarks possibly.
- **Localization Support**: With downloads in over 28 countries in just March 2025, internationalization is becoming a focus. The team highlighted the success of the German *Bundesamt für Sicherheit in der Informationstechnik* (BSI) Indigo baseline for iOS and the opportunity to include more global standards and languages moving forward.
Side Note: If you're wondering, *“Federal Office for Information Security”* is what BSI translates to in English, per Wikipedia**.** The conference discussed the best way to implement localization support into the project.
- **Simplification**: Easier to follow updates and unified configuration will lead to improved consistency, reduced costs, and faster time to market.
- **Dependency Cleanup**: Removing ruby from the project and moving to from asciidoc to markdown for documentation creation will add to the simplification and consolidation within the structure of the project.
---

## Impact on Mac Administration

You might be thinking: “*I’m not a vendor or developer; I’m just a Mac Admin using mSCP in my environment—what does this mean for me?*”

That was the exact perspective I made sure was heard on Day One.

The short answer? **This means a lot for us.**

The most powerful thing we can do right now is advocate. Advocate within our organizations, our peer groups, and especially with our security vendors. Whether it’s Microsoft, CrowdStrike, Qualys, Jamf, Tenable, Splunk—you name it—start planting the seeds:

> “*It would be extremely beneficial if your tools could directly support and integrate with the macOS Security Compliance Project*.” 

Vendors can contact the project directly by reaching out to [applesec@nist.gov](mailto:applesec@nist.gov) and begin now to invest in its future and plan how they can build their current or future projects and tools based on version 2.0.

**Standardization is coming.** But that will only happen if vendors understand the demand from us—the practitioners in the trenches. The more they hear it, the faster mSCP can become a universal framework for secure, auditable Apple device management.

The current roadmap has mSCP 2.0 in alpha testing, with beta coming in Fall 2025. The official **2.0 release is scheduled for June 19th, 2026**, marking the 6th anniversary of the project's initial release (v0.9).
--

## Implementation & Recommendations
![Section Banner - Implementation & Recommendations](/assets/img/postimages/mSCP_DevConf_Recommendations.png)

If you’re a Mac Admin who uses mSCP but hasn’t yet dipped a toe into the development or community side of the project, here’s how to get involved:

- **Encourage Vendor Participation**: Talk to your security and compliance vendors about supporting mSCP.
- **Join the Discussion**: Whether it’s **[MacAdmins Slack](https://www.macadmins.org)** in the *#macos_security_compliance* channel, or [GitHub Issues/Discussions](https://github.com/usnistgov/macos_security/discussions), your feedback will help to shape the roadmap.
- **Help Localize**: International admins—this is your time to shine. If your country or organization relies on specific standards, I encourage you to volunteer to contribute to mappings or translations.
- **Test New Tools**: The community is already seeing exciting developments like [Jamf Compliance Benchmarks](https://m.youtube.com/watch?v=gCj9Dx07Les&t=871s&pp=2AHnBpACAQ%3D%3D) (currently in beta), [Zentral Compliance Checks](https://www.zentral.com/how/compliance-checks/), and the [Addigy Compliance Library](https://addigy.com/compliance/).

> Grassroots got it done. Grassroots will get it done again. 
> - Stephen Quinn, ITL
---

## A Personal Highlight

I’ll be honest—I wasn’t expecting this: during the opening remarks, **Hannah Brown**, CIO of the NIST Office of Information Systems Management (OISM) gave me a direct call-out and thank you for my advocacy and community work around mSCP.  **Blair Heiserman**, NIST CIO within the Information Technology Security & Networking Division seconded that thanks during his talk.

To be publicly recognized by NIST leadership for what often feels like “just doing my job” on Slack and in the trenches of macOS security was a deeply meaningful moment—one I’ll remember forever. That acknowledgment reminded me how much of a difference our community support truly makes.

And, to top it off—I finally got to meet so many of you in person! It was a day full of folks walking up to me, going, “You’re Tony?! I see you in Slack all the time!” That was the cherry on top of an already memorable day.

## Conclusion & Next Steps

The mSCP Developer Conference was a pivotal moment—not just for the project but for our community. It signaled a transition from a tool created by a few to a framework shaped by many. With mSCP 2.0 on the horizon, now is the time to get involved, raise your voice, and shape the future of Apple device security.

![Photo taken showing room of mSCP Developer Conference Attendees ](/assets/img/postimages/mSCP_DevConf_Attendees.jpg)

Thanks again to everyone who helped make the conference special. To the entire mSCP team, the Mac Admins Foundation, and all the friends, new and old, I met in person: **I appreciate you all.**

Let’s keep building this together.
