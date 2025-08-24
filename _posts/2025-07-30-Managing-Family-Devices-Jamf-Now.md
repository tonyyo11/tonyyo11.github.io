---
title: "Managing Family Apple Devices with Jamf Now"
date: 2025-07-30 13:00:00 -0400
description: "How I keep my kids safe, apps up-to-date, and sanity intact by effortlessly manage every iPhone, iPad, and Mac in my home with Jamf Now."
categories: [Mac Management]
tags: [Family, macOS, iOS, iPadOS, Jamf Now, Jamf, Jamf Protect]
---

![Managing Family Apple Devices with Jamf Now Banner](/assets/img/postimages/ManagingFamilyAppleDevices_Cover.png){: lqip="/assets/img/postimages/ManagingFamilyAppleDevices_Cover.png" }

If you’re the “tech support” for your household (and maybe your extended family), you know that keeping iPads, Macs, and iPhones updated, secure, and kid-appropriate can be a full-time job. Over the years, I’ve juggled everything from my children’s iPads to my wife’s iPad, my own Mac Studio and Mac mini, and even more devices as the family grows. As a parent of three kids under 10, I find myself constantly navigating what it means to raise emotionally healthy, curious, and safe children in a tech-saturated world. Each of our children has their own iPad. Two of them also bring school-issued iPads home. We share a Nintendo Switch in the living room for family gaming time, but for the most part, our home is firmly rooted in the Apple ecosystem. I settled on a solution that bridges the gap between “family IT” and enterprise-level management: [Jamf Now](https://www.jamf.com/products/jamf-now/).

# **Why Jamf Now?**

Jamf Now is a cloud-based Apple device management platform built for small businesses, but it’s also ideal for power users, tinkerers, or anyone who wants true control over their family’s Apple ecosystem without the bulk of full-blown enterprise tools. While Jamf Pro is the “big sibling”—used by companies to manage thousands of Macs and iOS devices—Jamf Now hits a sweet spot for home and small business use. But for me personally, it was the added options of Jamf Protect and Internet Security that made the monthly price worth it.

Key differences between Jamf Now and Jamf Pro:

- Simplicity: Jamf Now has a web dashboard that anyone can figure out. No command-line, no policy scripting, and no app packaging required. Jamf Pro, by contrast, has deep customization options but a steeper learning curve.
- Features: Jamf Pro offers granular device management, custom scripts, and detailed reporting, but Jamf Now focuses on practical essentials: remote wipe, passcode enforcement, Wi-Fi and email configuration, basic app deployment, and security settings.
- Cost: Jamf Now is free for your first three devices. After that, it’s $4/month per device—far less expensive than having to deal with identity theft, malware attacks, and more.

# **How I Use Jamf Now for My Family**

I manage all the family iPads—including my wife’s and kids’—plus my own Macs through Jamf Now. Here’s how it works for us:

- Easy Enrollment: Each device gets an enrollment link and the enrollment process is super quick.
- Security Baseline: I can enforce passcodes, disable Siri or AirDrop if needed, and remotely lock or wipe lost devices.
- App Management: For macOS Devices, I utilize Jamf App Installers to push essential apps, as well as keep those apps up to date.
- Separation of Duties: I rely on our Eero router for content filtering and safe browsing at home. I also take advantage of iCloud+ Private Relay which also helps protect my identity while online. But when I leave home, my Eero router cannot come with me, so Jamf Web Protection helps with filtering in this scenario. My children love taking iPads for car rides, and its vital to keep them safe while on the go.
- Mac Security: For the Macs, I supplement Jamf Now’s management with Jamf Protect—the security tool included with every Mac. This gives us extra protection against malware, phishing, and suspicious activity.

# **How Did I Set Up My Devices?**

Honestly, the first time was pretty painless. Here’s how it went:

1. Signed up for [Jamf Now](https://www.jamf.com/products/jamf-now/) (the free tier covers three devices).
2. Created a “Blueprint” for each type of device—one for the kids, one for adults.
    
    ![Showing the two Jamf Now Blueprints within my tenant](/assets/img/postimages/JamfNowBluePrints.png)
    
3. Used Apple Configurator to Restore the children's’ iPads. This was required for the devices to enroll as supervised, and required wiping the iPads and setting up as new. 
    
    ![Used Apple Configurator to reset my children's iPads in order to become supervised.](/assets/img/postimages/AppleConfiguratoriPads.png)
    
4. Add devices to AppleCare One under my account.
    1. As of July 24th, 2025, Apple has made available a new “[AppleCare One](https://www.apple.com/newsroom/2025/07/apple-introduces-applecare-one-streamlining-coverage-into-a-single-plan/)” subscription plan. With this plan, you can cover up to three devices under AppleCare+ for $19.99/month and additional device for $5.99/month each. Unfortunately AppleCare One is not designed currently for family sharing, with Apple calling out “*AppleCare One plans can cover devices that are on the same Apple Account as the subscriber.*” To get around this, I temporarily signed into iCloud for my children’s devices, added the iPads to AppleCare One, then signed out and switched to their Apple Accounts. Note: Doing this disables Theft and Loss coverage under AppleCare One.
        
        ![Showing devices added to my AppleCare One subscription](/assets/img/postimages/macOSAppleCareWarrantySection.jpeg)
        
5. Enrolled each device by navigating to the enrollment URL in Jamf Now.
    
    ![Within Jamf Now, I have configured Open Enrollment Settings and captured my Enrollment URL for devices.](/assets/img/postimages/JamfNowOpenEnrollment.png)
    

It felt surprisingly empowering to see all my family’s devices in a single dashboard—and to know I could remotely lock, wipe, or configure them if needed.

![Showing my Jamf Now Dashboard](/assets/img/postimages/JamfNowDashboard.png)

# **Pros, Cons, and Considerations**

- Pros:
    - Simple setup and user-friendly dashboard.
    - Free for the first three devices.
    - Affordable for larger families ($4/month/device).
    - Remote lock and wipe can be a lifesaver for lost or stolen devices.
    - Works for both iOS/iPadOS and macOS.
    - Jamf Malware Prevention with Jamf Protect for macOS provides additional security and peace of mind from macOS malware.
    - Jamf Web Protection for iOS devices gives peace of mind when iOS/iPadOS devices leave home and any network based security enabled on home WiFi networks.
- Cons:
    - Limited advanced options (no scripting or custom policies like Jamf Pro).
    - Full supervision required for the most granular iOS controls (difficult for most families with devices not tied to an Apple Business Manager Account).
    - Web Protection only available for supervised iOS devices.
    - Both Jamf Protect’s Malware Prevention and Web Protection are not customizable and are managed by Jamf Security only.
        - You can optionally pay for a full license of Jamf Protect separately, but minimum device counts are required, and are may not be the best option for most families.

# What about Built-in Parental Controls?

Apple provides native parental controls via [Screen Time](https://support.apple.com/en-us/108806). With Screen Time, you can view time spent on your devices, schedule time away from the screen, and set time limits for app use — for yourself or for a child in your Family Sharing group.

The same devices that fuel creativity, learning, and fun also open doors to content we’re not ready for our children to walk through alone. That’s why Apple’s built-in Screen Time features have become a cornerstone of our digital parenting approach.

We use Screen Time to:

- Set daily time limits for games and entertainment apps
- Enforce downtime during meals, family time, and bedtime
- Restrict content by age, so only age-appropriate apps, movies, and websites are accessible

In-app settings provide another layer of control. YouTube is a tightly controlled experience—only specific channels and videos are allowed via the “approved content only” setting. Services like Disney+ are configured with individual kid profiles tied to age ratings to limit what can be watched.

This isn’t about being overbearing—it’s about protecting their minds, guiding their development, and letting them explore the digital world with guardrails in place.

Jamf Now adds additional tooling to ensure we are taking advantage of every possible measure available to protect our children.

# **What’s Next?**

This fall, my wife will be heading back to school for a Masters in Art Teaching and had to recently pick up a new MacBook. I enrolled it immediately after unboxing, giving her a managed, secure setup right out of the box. Over time, I plan to offer device enrollment to my extended family as well—just for the peace of mind and remote troubleshooting options Jamf Now provides.

# **Final Thoughts**

For tech-savvy families, Jamf Now bridges the gap between basic parental controls and full-scale enterprise management. If you want to keep your family’s devices secure, up-to-date, and easy to troubleshoot (even remotely), it’s hard to beat the convenience and peace of mind Jamf Now brings—especially for the price.

Some lessons learned from myself? Don’t overthink it. Apple Admins might find it “overly simple” if coming from Jamf Pro, and thats on purpose. Jamf Now is intentionally simple. Consider going the extra mile to supervise your iOS devices, especially children devices, in order to take advantage of the Internet Security functionality.

Jamf Now turned my “home IT” job from a headache into something I can manage with a few clicks. It’s not just about locking down devices—it’s about keeping my family safer, troubleshooting less, and knowing I can help when (not if) someone forgets their passcode again.

Have questions about setting up Jamf Now for your family, or want to know how it compares to Apple’s built-in parental tools? Leave a comment or reach out!
