---
title: "A Dialog with Scott Kendall - On swiftDialog and Ohio River Valley Ingenuity"
date: 2025-09-03 09:30:00 -0400
description: "An Interview with Scott Kendall on swiftDialog, Jamf Pro, and real-world macOS automation at Giant Eagle—tooling, scripts, and community best practices."
categories: [Mac Management, Inteviews]
tags: [macOS, Jamf, Jamf Pro, swiftDialog, Scott Kendall, Interview, MacAdmins ]
image:
  path: /assets/img/postimages/dialogwithScottKendall.png
  lqip: /assets/img/postimages/dialogwithScottKendall.png
  alt: A Dialog with Scott Kendall Banner Image
---

As a Mac Admin myself, I’ve always believed the most impactful innovations often come from the heart of our community, from admins quietly building solutions and generously sharing their tools with peers. When I discovered the powerful yet approachable GUI scripts created by Scott Kendall, particularly those that leverage the open-source tool swiftDialog, I knew immediately that I wanted to highlight his work. Scott’s practical, user-centric approach makes complex macOS management tasks accessible for admins of all skill levels.

Scott Kendall is currently a macOS Support Engineer at Giant Eagle, managing devices remotely from Columbus, Ohio. Before his role at Giant Eagle, Scott spent over a decade at JPMorgan Chase & Co., where he managed Jamf Pro and more than 6,000 macOS systems. His career highlights include architecting automation utilities such as deploying hundreds of critical applications, enhancing system stability, and dramatically strengthening security posture through innovative policy management and collaboration directly with Jamf and Apple.

Having roots in Pittsburgh myself, I was pleasantly surprised to learn that Scott manages macOS devices for Giant Eagle, a beloved Pittsburgh institution, from his home in Columbus, Ohio. Given his extensive background in both large-scale enterprise environments and regional companies, it felt like a natural fit to reach out, discuss his approach, and explore the personal and professional journey behind his impactful contributions to the Mac Admin community.

In this post, we’ll dive into Scott’s experiences, his approach to automation, the tools he’s built, and even a little friendly rivalry between the Steel City and Columbus. My hope is that Scott’s story and insights inspire other admins to embrace creativity, share their knowledge, and make the Mac Admin community stronger—one script at a time.

## **Background and Introduction**

**Q: Can you tell us a bit about yourself and your journey into Mac administration? How did you get started in this field?**

**Scott:** 

I knew I had a passion for Apple computers ever since my High School got its first Bell & Howell Apple II (anybody remember those units??).  After High School & College (DeVry University), a lot of my experience was self-taught.  Throughout my career, I have worked on macOS (v6.x), Windows (3.0), AT&T Unix (yes, I am that old), and various forms of *nix.  When Apple announced they were moving to a modern “BSD Unix Kernel,” that sold me.  I loved working in the terminal and getting to have Apple’s GUI altogether!

Since Sept 2003, I have worked in positions that required me to “keep the Macs running” and to work with the existing PC infrastructure.  They didn’t want any active work done on them, just keep them in “maintenance” mode…  They also had me work both PC & Mac tickets since I had very good skills on both platforms…not my ideal working environment as I much preferred to use Macs as a “daily driver”, but I was able to keep my Mac skills current while working PC tickets as well.

In August 2013, I worked at JPMorgan Chase, handling both Macs & Windows (again), but I also had the opportunity to work with a variety of departments that furthered my education in networking, help desk, change control, cybersecurity, management structure, & (very limited) macOS server administration.  Around Jan 2022, our help desk team got sourced out to 3rd party organizations (we have probably all gone through that by now), and I was able to join the macOS engineering team (finally! I get to work on Macs full-time!).  During that time, my knowledge of JAMF increased, I started writing more scripts (my passion), and doing a lot of application packaging.

As with all big corporations, I unexpectedly got laid off around Aug 2024 and had to start my job search all over again (was 58 at the time…) and a headhunter contacted one of my former co-workers. He told the headhunter about me losing my job, so the headhunter contacted me about the GE position, and I had three interviews in one week. (They do NOT waste time).  I was supposed to find out shortly thereafter about the position, but that is when the huge Crowdstrike fiasco took down pretty much the entire PC industry.  Needless to say, Giant Eagle IT was so focused on fixing what Crowdstrike broke that I didn’t hear back for a while.

This position, I found out, is a “one man” show.  I do everything from Help Desk level 1 support all the way up to macOS server administration.  I had to learn EVERYTHING about JAMF there is to know (and still learning!), that I never got exposed to before, so it was a very steep learning curve.  One year later, I have finally gotten all the Macs up to the level of integration, cross-platform usability, and administration, and the head of IT has now told users that when they get new systems, they can either get a PC or a Mac!

I really pushed cross-platform compatibility during my interviews, and I found out later that that is what got me this job.  I wanted to make sure that no matter what system a user gets, they can be able to use all the tools on their respective machine and it “just works”.  Trying to make the experience as seamless as possible….

**Q: You’re currently at Giant Eagle, a well-known regional grocery chain here in Pittsburgh. What unique challenges does a grocery store, or more specifically, a corporate retail environment like Giant Eagle, present for managing macOS devices?**

**Scott:**

GE has a lot of Macs at the Market District stores, mostly, so I have multi-user Macs (iMacs or Minis) at each location. Everyone works with Adobe products to create the flyers/banners that you see scattered around the stores.  All our Home Office Graphics design teams are all Mac-related, so keeping them current with macOS & Adobe releases is a constant battle.  We are mostly a Microsoft organization as well, so Microsoft Office / Teams / OneDrive, etc, are seamless working across PC or Macs.  (Again, going for the seamless cross-platform experience.)

## **Embracing SwiftDialog**

SwiftDialog is an open-source, SwiftUI-based utility that enables Mac administrators to create custom dialogs and user interfaces within their scripts. 

One of Scott Kendall’s most impressive contributions is his growing collection of **Jamf Pro automation scripts** (available on his [GitHub](https://github.com/ScottEKendall)) that leverage SwiftDialog to provide a simple GUI for complex tasks. These scripts function like mini-applications, enabling Mac administrators to interact with Jamf Pro’s API or macOS system commands through friendly dialog windows, rather than the command line. 

**Q: What initially drew you to swiftDialog, and how did you first start using it in your projects?**

**Scott:**

I was first exposed to swiftDialog while working at Chase and fell in love with the interface and that everything can be done through shell scripting!  I immediately presented the idea of switching our notification systems to using swiftDialog, and we could create a consistent branding experience for the Mac users.  My manager loved the idea and had me working on learning more about it, figuring out how it works, and how we can adapt it for our use.  I spent weeks learning everything I could about SD and wrote several sample scripts to test various features, capabilities, and other aspects. I also developed a quick “shortcut” guide for in-house use.

The more research / Googling I did on SD, the more I found that there were tons of scripts/interfaces that people were using SD for.  At the time, we were using another DEP enrollment system at Chase. I stumbled across Dan Snelson’s excellent “Setup Your Mac” script and was about to present it to my manager at Chase when I got laid off… so needless to say, I implemented that concept at Giant Eagle. My new boss loved it (they had been using DEP notify before), so this was a huge change.

**Q: Could you describe some common use cases at Giant Eagle where swiftDialog has significantly improved your workflows or end-user experience?**

**Scott:**

A lot of notifications, mostly.  I send pop-up notifications to their screens about upcoming network changes, OS updates, EntraID configuration issues, password expirations, and JAMF Connect login issues. Users are more likely to see that notification vs a mass email distribution.  I also have various scripts to check battery status, Secure Token status, retrieve IP addresses, check/repair Microsoft InTune Registration status.. I post all of my scripts on my Github repo that I have created. I have over 50 utilities so far.

**Q: Are there any specific scripts or projects you’ve created using swiftDialog that you’re particularly proud of or find especially impactful?**

**Scott:**

I have really started to dig into the JAMF API system recently and created my own JAMF utilities, which can do things like:

- Backup Self Service icons.
- Send email to members of static/smart groups (use that one a lot!)
- Clear failed MDM commands.
- Backup configuration profiles.
- System Usage reports based on start/end dates.
- Find users with multiple Macs.
- Compare configuration profiles.

This grew out of necessity, pretty much as I had to figure out what sort of systems were in use at Giant Eagle and how they were configured, and to “wrap my head” around the environment when I started.  I might mention that some of the scripts are also more “modernizations” of older scripts that I found.  I rewrote them to take advantage of new JAMF options, or changes to macOS…(while giving full credit to the original author)

**Q: How does swiftDialog compare to other user-interaction tools you’ve previously used or evaluated (e.g., Jamf Helper)? What makes swiftDialog stand out?**

**Scott:**

I love the native macOS-style interface that swiftDialog presents to users, and the flexibility of scripting it.  I was exposed to an older version of Nudge and didn’t like the way it looked either; it felt out of place.  Chase (at the time when I started) was using CocoaDialog…didn’t like that interface one bit, (but I think that was pretty much the only notification method out there…)

**Q: Can you share any lessons learned or best practices you’ve discovered when integrating swiftDialog with Jamf Pro?**

**Scott:**

The parameter passing in scripts gives you a lot of flexibility.  I have been able to create a “generic” Notify Dialog prompt for the users, and I took advantage of the script parameters that you can pass with JAMF and was able to reuse my same Notification script multiple times just by changing parameters (my GitHub repo shows how I took advantage of that).  Additionally, leveraging EAs to create smart groups that display prompts about passwords and Jamf Connect issues is also handy.

**Q: For Mac Admins just starting out with swiftDialog, what would your top recommendations be for resources, strategies, or initial projects to tackle?**

**Scott:**

Bart Reardon’s GitHub wiki for swiftDialog is great, but can be overwhelming at times.  Lots of info to take in.  What I did was to look at other people’s code and get an idea of the basic construct of a command and what can be done with it, and then expand on it from there.  If you look at my GitHub repo, you will see that pretty much all my scripts have the same basic layout, variables, and methods that make creating new scripts a snap.

Use Slack!  The #Macadmins channel is chock-full of ideas and people willing to help solve issues or share their thoughts.  Some of my ideas came from issues that others were posting about.

## **Community Contributions and Open Source**

Collectively, Scott’s SwiftDialog-enhanced scripts turn common Mac administration jobs into straightforward workflows. They lower the barrier to entry for performing advanced tasks on macOS devices. By abstracting away the coding and API calls behind friendly buttons and dialogs, Scott is helping fellow admins help themselves. His work exemplifies the power of sharing within the IT community: each script he publishes is another “tool in the toolbox” for Mac admins everywhere to use and learn from. It’s no wonder his GitHub projects have garnered interest – these scripts fill practical needs and are built with an obvious passion for solving problems.

**Q: You’ve been actively contributing to the Mac Admin community through your GitHub repository. What motivates you to open-source your scripts and tools?**

**Scott:**

I have been creating scripts that I find necessary for me to do my work more efficiently, and then I figured that if I have a need for it, others might too… trying to make life easier for all Mac Admins (especially for the new ones just getting started).

**Q: Have you received feedback or contributions from the community that have helped improve your projects? Any particular interactions stand out?**

**Scott:**

I have fun helping users integrate my scripts into their environment.  Some have very little programming, so I try to make it as easy as possible.  I had one user from Poland reach out to me about the possibility of adding dual language support for my DialogMsg script.  Turns it out was easy to implement.

**Q: What’s your perspective on the current state of open-source collaboration within the Mac Admin community? How has it changed or evolved during your involvement?**

**Scott:**

It turns out that the Mac Admin community is small, but mighty!  We all contribute to help each other out.  Like me, I was pretty much a newbie to all aspects of admin duties, and I have heard from multiple people that got “thrown” into their role as well…they may not know what they are doing, so we try to assist each other as much as possible.

**Q: What’s your go-to resource or community (Slack, forums, blogs, etc.) when you’re stuck or need inspiration?**

**Scott:**

Slack #MacAdmins community is my go-to for everything from enrollment issues, to Entra Platform SSO, Swift dialog programming, JAMF Connect…you name it.  I would say I am a part of about 50 channels on there.

**Q: Any personal workflows or productivity hacks you’ve discovered that make your Mac Admin day smoother?**

**Scott:**

I use the Smart groups on my dashboard extensively!  I setup smart groups in such a way that tells me if a user doesn’t have a secure token, FileVault keys are not set or not escrowed to the server, JAMF connect not syncing properly, etc.  If my smart group dashboard shows 0, then that is good news.  If anything other than 0, then there is a problem, and I can investigate further.   I can quickly glance at my dashboard to see the status of key areas.

I had one issue that last week that was showing Cisco AnyConnect application was not functioning properly on some user’s macs.  Was able to fix it in less than 2 hours, and the users were none the wiser. They didn’t even know that something was wrong…

## **Columbus, Pittsburgh, and The Greater Ohio River Valley**

While Scott’s technical expertise is evident in his scripting and automation, his role at Giant Eagle also highlights the unique dynamics of working remotely between two cities with strong regional pride. Managing Macs for a Pittsburgh-based company while living in Columbus brings both cultural quirks and professional adjustments.

**Q: You’re currently working remotely out of Columbus, Ohio. What has been your experience like managing Mac infrastructure remotely for a Pittsburgh-based company?**

**Scott:**

That took a little getting used to… full-time Work from Home. For the first few months, I was making monthly visits to our HQ in Cranberry Heights to have some face-to-face contact with users, but a lot of the users are WFH (or even in other areas of the globe), so most of my work is done via Teams (video) chat.  The biggest thing that I miss is probably the team that I had while working at Chase.  We had a team of six, and we could bounce ideas off each other or have someone else look at my Change Control to make sure I didn’t miss anything.  At Giant Eagle, I am a team of One…so that is kind hard…I depend on Slack to be my other teammates sometimes.

**Q: Are there any favorite local spots or hidden gems you’d recommend to fellow tech enthusiasts visiting or new to Columbus?**

**Scott:**

The Short North area of Columbus has some great museums, festivals and lots of great restaurants, it has been built up over the years and is a great way to spend a Friday or Saturday night hanging out with friends

**Q: Both Pittsburgh and Columbus have vibrant tech communities and strong regional identities. Have you found that working for a Pittsburgh-based company influenced your professional interactions or creative approach while working in Ohio? Any Yinzerisms rub off on you?**

**Scott:**

So... when I told my friends that I was going to be working for a company in Pittsburgh, the first thing they told me was that everyone calls it “Da Burgh”, not Pittsburgh, and I also had to go to Primanti Bros…a ‘Burgh’ exclusive….  I actually had to look up the term “Yinzer”…never heard of that before…LOL

**Q: Have you found any interesting tech or professional communities in Ohio outside of Mac Admins that have influenced your work or inspired you?**

**Scott:**

I wish there were a Columbus-based Mac Admins group, but I haven’t found one yet.  The JAMF Nation is a good resource as well..I also had the opportunity to go to my first JNUC (JAMF Nation User Conference) last year!  Loved it and will try to go more often…great way to network and the contacts I found there I also found them on Slack so we can work together on stuff.

**Q: On a lighter note, when it comes to sports rivalries, how do you handle dealing with colleagues in the Pittsburgh region who speak on the Pittsburgh Penguins versus the Blue Jackets, or the Steelers always having the winning record against the Cleveland Browns?**

**Scott:**

Well…My wife and I are not huge sports fans, so that is not a big issue for me…and just for the record, just about anyone has a better record than the Browns ;-) ….don’t hate me…

## **Looking Ahead**

As much as Scott has accomplished with swiftDialog and Jamf Pro, he’s already thinking about what’s next. From upcoming changes in macOS to wish-list features and new skills, his focus is firmly on the future of Mac administration.

**Q: What future plans or ideas do you have in mind for using swiftDialog or any other tools in your environment or personal projects?**

**Scott:**

As we are on the verge of Apple’s completely redesigned macOS 26 (Tahoe), I am eager to see what changes will be made for swiftDialog.  I am not a swift programmer, but I would love to learn and maybe even someday help contribute to Bart’s app.

**Q: If you had a wish list for swiftDialog enhancements or features, what would you love to see implemented?**

**Scott:** 

The biggest thing I can think of is multi-column lists.  I could have used that in my JAMFUtilities app, but I had to settle for a scrolling list view for users to select options.  Not the most ideal GUI, but it works.

**Q: Lastly, any advice for fellow Mac Admins considering contributing their scripts and tools back to the community?**

**Scott:**

If you think you have any idea that helps you, then chances are someone else might also have the same need.  Don’t be afraid to post ideas or code…most users will offer constructive feedback, which will help you further your programming experiences.  I did have to learn how to use GitHub…that was a little daunting, but don’t be afraid of it.  I literally just posted my first Pull Request for someone else’s code a few weeks ago.   That was a totally new experience for me, but it was fun..I learned a lot…

## **Fun Additional Questions**

We’ve covered the serious stuff—now for a few rapid-fire questions to round out Scott’s story.

**Q: If you could wave a magic wand and change one thing about macOS or Jamf Pro, what would it be?**

**Scott:**

For Apple to stop changing the enrollment screens all the time!  During macOS 15 rollout, it seems that every .X update brought about a new (or changed) enrollment screen, which caused us admins to constantly stop the new screens from disrupting the enrollment process!

**Q: What’s your preferred work-from-home setup like? Any special gear or workspace arrangements you’re proud of?**

**Scott:**

Mine is nothing special: two external 27” monitors, as well as my 14” MacBook Pro. It’s hard to go portable sometimes with having a “triple monitor” setup at home. I do like the new Thunderbolt docks. One cable to rule them all (monitors, power, USB, audio, Ethernet, etc).    I also have a nice Ubiquiti Dream Machine SE setup at home with a Wi-Fi 7 access point, so I tinker with creating VLANS for my work, home, IoT devices, etc.

To be totally geeky, I have:

- Dream Machine Pro SE
- 16 Port 2.5gb Etherlight switch Pro-Max-16
- 10GB aggregate switch
- 2 uNAS Pro
- 4 UW-7 ProXG
- Hoping to get a Unifi Talk setup shortly so I don’t have to use my cellphone for support

**Q: Is there one bit of advice you’d give to someone who’s just starting their journey as a Mac Admin?**

**Scott:**

Don’t be afraid to ask for help!  Don’t feel like you must do it all on your own.  Build your network of contacts to support you throughout your admin journey.  For starters, here’s mine on Slack: **@Scott Kendall**

---

Taken together, these lighter moments underscore the same theme that runs through Scott’s work: curiosity, practicality, and a willingness to share. Whether it’s refining swiftDialog workflows, publishing Jamf utilities, or helping a stranger on Slack, Scott’s approach turns individual problem-solving into community progress. With macOS “Tahoe” on the horizon and new ideas ready to be created each day, his story is a reminder that the best tools start with listening, iterating, and giving back.

## **Conclusion**

In a short time, Scott Kendall has grown from a swiftDialog learner to a respected contributor in the Mac Admins community. Embracing SwiftDialog was a catalyst for that growth – it pushed him to learn new skills, rethink automation with user interaction in mind, and ultimately produce a suite of helpful GUI tools for others. 

By developing and sharing his SwiftDialog-powered Jamf Pro scripts, Scott has not only expanded his own capabilities but also made life easier for countless Mac admins and support folks. From redeploying the Jamf agent with a click to retrieving encryption keys securely, Scott’s solutions exemplify the best of IT ingenuity. As he continues to iterate on his tools and possibly invent new ones, there’s no doubt his journey will inspire other admins to get creative, automate the mundane tasks, and help each other out. In the spirit of Pittsburgh, Scott Kendall proves that with the right mindset and community, you can turn curiosity into impact – one script and one friendly dialog at a time.

**As referenced earlier in this post, you can find Scott in the following places:**
Linkedin - [https://www.linkedin.com/in/scott-kendall-a32a642/](https://www.linkedin.com/in/scott-kendall-a32a642/)
Github - [https://github.com/ScottEKendall/](https://github.com/ScottEKendall/JAMF-Pro-Scripts)
MacAdmins Slack: @Scott Kendall | [https://www.macadmins.org](https://www.macadmins.org/)
