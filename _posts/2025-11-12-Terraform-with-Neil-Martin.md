---
title: "Learning Terraform for Jamf with Neil Martin"
date: 2025-11-12 12:00:00 -0500
description: "A Q&A with Neil Martin on Terraform providers, patterns, and how Mac Admins can start managing Jamf Pro as code."
categories: [Mac Management, Inteviews]
tags: [Jamf, Jamf Pro, MDM, DDM, macOS, IaC, Infrastructure as Code, Terraform, Jamf Platform, Platform API, Neil Martin, Q&A]
toc: false
image:
  path: /assets/img/postimages/Learning_Terraform_for_Jamf_with_Neil_Martin.png
  lqip: /assets/img/postimages/Learning_Terraform_for_Jamf_with_Neil_Martin.png
  alt: Learning Terraform for Jamf with Neil Martin
---

> **Jump to:**  
> [Origin story](#why-terraform-at-jamf) ·
> [Starter repos & first steps](#starter-repos-and-first-steps) ·
> [Change management & GitOps](#change-management-and-gitops-mindset) ·
> [Where IaC is headed](#where-iac-is-headed-for-mac-admins)

> **Sections:**  
> [The Spark](#the-spark) ·
> [The Mission](#the-mission) ·
> [The Approach](#the-approach) ·
> [Collaboration & Community](#collaboration--community) ·
> [The Tech Side](#the-tech-side) ·
> [For the Learners](#for-the-learners) ·
> [The Big Picture](#the-big-picture)

> **Previously in this series:**
> [Last Month: Prepping for Learning Terraform and an Exercise on Automation (October 2025)](https://tonyyo11.github.io/posts/October-Learning-Terraform/) 
> [Prepping for Learning Terraform and an Exercise on Automation – Part 2](https://tonyyo11.github.io/posts/Learning-Terraform-Part2/)

Versioning changes, reviewing diffs, and rolling back safely are table-stakes for modern fleet management, which is why Infrastructure as Code has started to land in the Apple admin world. At JNUC 2025, Jamf highlighted how Terraform can model both Jamf Pro objects and newer Jamf Platform capabilities, enabling teams to promote changes through Git-based workflows rather than ad-hoc edits. 

If you're brand new to the concepts, Jamf's own primer, [**Managing Jamf Configuration with Terraform: An Introduction**](https://trusted.jamf.com/docs/managing-jamf-configuration-with-terraform-an-introduction), is an excellent starting point before you dive into the Q&A.

As more Mac Admins start exploring Infrastructure as Code (IaC), one name that is very present in the Terraform channels in the Mac Admins Slack is [**Neil Martin**](https://www.linkedin.com/in/neilmartin83/). His work within both Jamf and the Mac Admins community has given him one of the clearest voices helping Mac Admins get started.

At the Jamf Nation User Conference (JNUC) 2025 in Denver, Colorado, Neil hosted a session alongside Tristan Valente of Netopie titled Automating Apple Endpoints Management: Git, CI/CD, and Terraform for an Efficient Jamf Pro Administration. As of the time of this posting, the video recording of that session has not yet been shared publicly from Jamf, but Jamf has made a [blog post to recap](https://www.jamf.com/blog/automate-apple-endpoints-terraform-gitlab-cicd-jamf-pro/) the session. You can also get the presentation slides on Neil's blog, [Sound Mac Guy](https://soundmacguy.wordpress.com/2025/10/11/infrastructure-as-code-jamf-highlights-and-resources-from-jnuc-2025/?utm_source=chatgpt.com).

His GitHub repositories, [*terraform-jamfpro-starter*](https://github.com/neilmartin83/terraform-jamfpro-starter) and [*terraform-jamfplatform-examples*](https://github.com/neilmartin83/terraform-jamfplatform-examples), are go-to resources as I'm learning to manage Jamf environments declaratively. Neil has been steadily lowering the barrier for Jamf admins who want to automate without needing a DevOps background, providing structure, examples, and guidance that make Terraform approachable from day one. Please continue reading to learn more about Neil's work, goals, and how he sees Terraform shaping the future of Mac Administration.

As I continue my journey learning Infrastructure as Code with Terraform and Jamf Pro, I reached out to Neil to pick his brain and ask some questions.

What follows is an unedited, text-based interview with Neil covering the origin story, the provider landscape, and mindsets for adopting GitOps with Jamf.

---

## About Neil

Pulled directly from Neil's own LinkedIn profile:
> The guy behind the [Sound Mac Guy](https://soundmacguy.com/) blog, and a member of the Admin Team on the MacAdmins Slack community, Neil works with Jamf to help businesses and educational institutions succeed with Apple technology.  
> His prior experience is in the field of higher education, managing 3rd line, institution-wide endpoint provision.  
>  
> Neil has extensive Apple systems administration experience within the enterprise environment and is familiar with the deployment and management of a range of macOS systems; both servers and clients.  
> He has managed Macs with Jamf Pro (formerly Casper Suite), DeployStudio, and Munki, and has been a GNU/Linux advocate since first using Red Hat in 1998 and later Fedora, Debian, and Ubuntu.  
> – [Neil Martin on LinkedIn](https://www.linkedin.com/in/neilmartin83)

---

## **The Spark**

### Why Terraform at Jamf?

**What first inspired you to start building out Terraform resources for Jamf Pro? Was this something you had an interest in, or did it originate from a directive from Jamf?**

> Like all great adventures, it happened by a combination of fortune and accident. Our team was involved in an initiative to create a GUI-based "wizard" for MSPs to apply a rich and complete Jamf Pro configuration in a brand new instance. Imagine someone with a Jamf 400 had done it using what we'd consider to be best practices. That was the goal. The idea was that it would ask a bunch of questions, then inject the resulting configuration using the API. We wanted it to do everything and the kitchen sink; IdP integration (SSO/LDAPS/Jamf Connect), PreStages, apps, profiles for things like Wi-Fi etc… So an MSP could spin up a configured instance for a customer in minutes with as little interaction with the Jamf Pro web UI itself as possible. For MSPs, they need to do things at scale in a consistent, repeatable way. The more we can automate and standardise, the better.
>
> Anyway! We got wind that colleagues across the pond in our SE team had been building something called Onboarder. This was serving a similar mission, but for an SMB/partner audience. They were using this Terraform thing (I had never heard of it at that point). My teammate Graham Pugh had a closer look and it got him excited. He enthusiastically came to myself and colleague Kyle Hoare, strongly suggesting we check it out. I was actually a little reluctant to start exploring a new tool as we were quite a way into this journey already, so it would mean quite a big pivot…
>
> So to answer your original question; there was a loosely related directive in Jamf we were working on, I was not interested in Terraform (isn't that something you use to build stuff in AWS?), and I'd only heard of this "Infrastructure as Code" thing in passing.
>
> But… Graham and Kyle's enthusiasm started to become infectious and I approached Terraform with an open mind. Then one thing led to another…
> 
> Many colleagues at Jamf across different teams and countries are involved. It's being taken seriously at an organizational level and I'm really happy to see that.
> 

**Was there a specific challenge or "aha" moment that made you realize Terraform could make life easier for Jamf admins?**

> The funny thing is that we didn't really think about that. But I could see the potential as soon as I did my first plan and apply. The way it tells you what it's going to create, change or destroy. That it can detect changes made in the UI and revert them back to your desired state. The verbosity of it. How HCL works as a language; the way you can look at a resource block and immediately see and understand the UI object it represents. From the start it was obvious that Terraform could go way beyond what we were looking to do with it. Its scope extended much further from just building configuration in Jamf.
> 

## **The Mission**

### Starter repos and first steps

**Your starter and platform repositories are clearly designed with newcomers in mind. What's your core goal behind these projects?**

> Each of those repos has its own unique and separate place. I hastily threw jamfplatform-examples together for JNUC to showcase some capabilities of our new Jamf Platform provider we announced. If you saw Mike and Dan's demo during the opening keynote at JNUC, the repo they used there may have looked quite similar… The other motivations were a little more selfish; I wanted to demonstrate the provider for people at JNUC and share a few examples without having to overly explain or go into detail each time someone asked. Quick answers to questions like "what does a CIS Level 1 benchmark look like in Terraform?".
> 
> jamfpro-starter's goal is quite different and it's using the fantastic jamfpro provider from Deployment Theory as well as ours. The goal here is to show a completely IaC managed Jamf Pro instance with some common, juicy workflows. It came about because of a mistake I made: I assumed those interested in using Terraform/IaC for managing Jamf would already have experience using it for other platforms. They'd just need the provider with a few examples and could run with that. I was shortsighted. The community was clearly into this as much, if not more than we were and that hunger needed satisfying! Admins wanted to know how to start from scratch.
> 
> So I tried to put myself in their shoes and ask what would help me if I were someone who knows Jamf Pro inside and out?. Someone who's comfortable with scripting and has some experience with APIs. Maybe I've used GitHub or another VCS and get the general idea about what they're about. But when it comes to Terraform and Infrastructure as Code (IaC), these are totally new concepts to me. The goal behind jamfpro-starter is to show one take on how this might be achieved in a holistic sense. From structuring the project and building to code, to understanding and implementing GitOps workflows when it comes to managing lifecycle and change. It's a totally different mindset.
> 

**Do you picture them as something for solo Jamf admins to experiment with, or for larger teams looking to modernize how they manage Jamf?**

> They are for anyone who might be interested in exploring how to manage Jamf in this unique and interesting way. And for anyone who isn't. Because I wasn't interested at first either… Just be careful, you might end up going down the rabbit hole and writing your own provider! Go is quite like Python, and as someone who's dabbled with that and is also familiar with Bash, it drew me in…
> 

> **Getting started:** If you're just beginning your Terraform journey, follow my earlier posts where I walk through some of the same steps Neil mentions – from installing Terraform to running `terraform apply`:
> 
> - [Last Month: Prepping for Learning Terraform and an Exercise on Automation (October 2025)](https://tonyyo11.github.io/posts/October-Learning-Terraform/)  
> - [Prepping for Learning Terraform and an Exercise on Automation – Part 2](https://tonyyo11.github.io/posts/Learning-Terraform-Part2/)
> 
> Those posts cover the hands-on setup before diving into the concepts Neil expands on in this Q&A.
{: .prompt-info }

## **The Approach**

**Many of us come from a sysadmin or IT operations background rather than DevOps. What do you see as the best "on-ramp" for Mac Admins learning Terraform?**

> There's a lot more resource out there beyond what I'm doing - explore as much as possible! I know you've been doing the Google training yourself - how has that been? The community has also stepped up big time. Jump into #terraform-provider-jamfpro and #terraform-provider-jamfplatform on the MacAdmins Slack and join the discussion. Check out Scott Blake's 101 blog series, it's a great primer. Deployment Theory have some fantastic training material they've open sourced too; they also have their own demo repo as an alternative approach to mine. Jamf are publishing content on trusted.jamf.com and have the terraform-jamf-platform repo (a third approach to what a structured deployment could be - all approaches are valid here!). Dive in. Ask AI when you get stuck - it's surprisingly helpful! Hashicorp also offer their own certification platform but I don't have any experience of it myself. It could be worth checking out for those who want something more official, but it won't be tailored to working with Jamf in mind. And of course, stay tuned for when the JNUC sessions go public on YouTube and watch those!
> 

**How do you decide what to include (or simplify) so the examples stay beginner friendly while still showing what Terraform is really capable of?**

> I was a beginner about 6 months ago. I still am. Learning new things every day! Taking a leaf from Buddhist philosophy, I try to approach these decisions with my beginner's mind. I'm still learning and the starter repo is a reflection of that journey so far.
> 
> I'm aiming to strike a balance between existing familiarity, simplicity, elegance and complexity where it's needed. The structure represents the Jamf Pro UI; modules grouped by their object type; Policies, Settings, Profiles and such, on purpose. Idea being it's familiar to a Jamf admin and comfortable. I wanted to also demonstrate how to achieve new, bleeding edge workflows like Simplified Platform SSO and Set/forget Software Update Blueprints. Real-world, useful things folks want to do. And throw in some more advanced concepts like using for_each to elegantly generate multiple resources of the same type with as little code as possible. Or using my itunessearchapi provider to data source App Store App metadata like icon URLs, bundle IDs etc to feed to the resources that create those apps in Jamf Pro. Passing data around and using it between different providers, data sources and resources is where Terraform's powere is really unleashed. I also want to show what I think are "best practices" - that's really important, doing things "right" (or what my opinion of "right" is - hey it's my repo!). I'm trying to create a blend of examples that I hope will keep people interested and encourage them to learn more.
> 

## **Collaboration & Community**

**You've been active in the Mac Admins Slack, sharing resources and feedback openly. How important has that community collaboration been in shaping your work?**

> It's the most important thing. This work would not exist without it. I am just standing on the shoulders of giants, really!
> 

**Have you seen any particular feedback, contributions, or conversations that changed how you approached your repositories?**

> Feedback has been positive so far. It's early days, but I'm looking forward to receiving more opinions so they can get better. I'd love to hear from anyone who's started their own project from the starter repo, especially if they're extending it with their own workflows and approaches. Pull requests are 100% welcome.
> 

## **The Tech Side**

**You've contributed to Deployment Theory's official Jamf Pro provider. From your perspective, what are some of the most exciting possibilities it unlocks for Jamf admins?**

> They really made this whole thing take off. Dafydd, Joseph, Bobby and team really deserve so much credit here. They built the most feature-complete Terraform provider for Jamf Pro and it's been going from strength to strength. When we identified Terraform as the tool to drive Onboarder for MSP, we also seized the opportunity to give back by adding resources and data sources we needed for that project to succeed. My colleagues in the SE team had also been working with the provider for a while and they began Jamf's contributions to it before I joined in.
> 
> I'm especially grateful to Deployment Theory for their guidance and patience with my pull requests. I deservedly received quite a grilling as I got my chops into learning Go as I went. Now those efforts from everyone have really bared fruit. The provider is at a level where you can pretty much manage most of what you'd want to in Jamf Pro with Terraform, complete with all the GitOps workflows for testing, reviewing and approving changes. If there's an API endpoint for it, the provider can manage it… and if it can't, anyone can get down and dirty with Go and add support! We're using this technology to run web-driven workflows that build configuration in Jamf Pro instances (Onboarder), which is a very different use case from direct GitOps lifecycle management that customers and partners want. Everyone wins. The possibilities of what you can do with this technology are really only limited by your imagination.
> 

**Can you explain the main differences between the different Jamf-related terraform providers that exist out there?**

> There are a growing number of actively developed providers that focus on the Apple admin space. Here are a few I'm aware of and involved with:
> 
- [`terraform-provider-jamfpro`](https://github.com/deploymenttheory/terraform-provider-jamfpro) - manages objects in Jamf Pro instances (on prem or cloud). It works with the Classic and Jamf Pro APIs. It's been around for over a couple of years and* owned/maintained by Deployment Theory/Lloyds Banking Group. We at Jamf are grateful and proudly contribute development effort into it.
- [`terraform-provider-jamfplatform`](https://github.com/Jamf-Concepts/terraform-provider-jamfplatform) - manages the new generation of Jamf Platform microservices using the new beta Platform APIs. That includes Blueprints and Compliance Benchmarks as well as data sources for Unified Inventory. It's owned and maintained by Jamf, with me being a core maintainer. And here's a story: it started as a weekend jolly to scratch an itch when I got early access to those APIs. I had absolutely no idea it was destined to be announced in the JNUC opening keynote. That completely blew me away and I was so humbled. Big shout out to my colleagues in the SE team for all their support in helping to get it published!*
- [`terraform-provider-axm`](https://github.com/neilmartin83/terraform-provider-axm) - my own creation for managing Apple Business and School Manager with their new APIs. Right now it's mostly data sources, with one resource for managing device assignments to device management services. I'm hoping Apple add more endpoints because managing AxM using IaC would be huge.*
- [`terraform-provider-itunessearchapi`](https://github.com/neilmartin83/terraform-provider-itunessearchapi) - gives you a data source for pulling an obscene amount of metadata for content in Apple's ecosystem. It's useful for App Store Apps - fetching names, descriptions, icon URLs and other data you can reference directly when provisioning resources.*
- [`terraform-provider-jsctfprovider`](https://github.com/Jamf-Concepts/terraform-provider-jsctfprovider) - for Jamf Security Cloud. Written by colleagues Dan Cuddeford and Ryan Legg in the SE team. It lets you manage ZTNA, routes, UEM integration and loads more. I haven't had the chance to explore this one yet but I'm really looking forward to doing so…*

**As Jamf continues expanding the "Platform" story – with Blueprints, Compliance Benchmarks, and the Platform API, how do you see the Terraform providers evolving to support that broader ecosystem?**

> The [jamfplatform](https://github.com/Jamf-Concepts/terraform-provider-jamfplatform) provider will continue to grow in lockstep with the Platform APIs as more capabilities are added to them. As an example, the provider already supports for "set and forget" Software Update declarations which were just announced. Basically, when you see something new appear in the Platform API, expect to see it in the provider!
> 
> I don't want to speak too much for the jamfpro provider as it doesn't belong to me or Jamf. We are continuing to contribute so it stays up to date with each new release of Jamf Pro, as new API endpoints are introduced and existing ones change. Both providers can and should be used together to provide the most comprehensive management of a Jamf environment.
> 
> Switching gears, one important note is support and I do want to draw a distinction here. Whilst the jamfplatform provider belongs to Jamf, it is published under [**Jamf Concepts**](https://concepts.jamf.com/child_pages/about.html) and is not actually a Jamf product in any official sense. It's open source, released under the MIT license. It's provided freely, without warranty or any official vendor-level support. That means getting help with it is a little unorthodox and community-focused. Raise an issue in the GitHub repository, submit a pull request to fix a bug yourself, or join the discussion in the [#terraform-provider-jamfplatform](https://macadmins.slack.com/archives/C09HP9V1K5H) channel on MacAdmins Slack.
> 
> As for challenges, a big one I can see right now is how to apply all this to an existing, already-configured Jamf stack. Starting from scratch is always easier. Importing existing configuration into a Terraform project is tough (and I mean tough), but I bet there are some really smart people out there working that problem. I'm very excited to see what happens here in particular.
> 

<div class="prompt-warning" style="overflow:auto;">
  <img src="/assets/img/postimages/terraform.png"
       alt="Jamf Concepts Terraform Logo"
       style="float:right; width:33%; max-width:160px; height:auto; margin:0 0 0.5em 1em;">
  <p><strong>Note:</strong> Whilst the <code>jamfplatform</code> provider belongs to Jamf, it is published under 
  <a href="https://concepts.jamf.com/child_pages/about.html" target="_blank" rel="noopener">Jamf Concepts</a> and is not actually a Jamf product in any official sense.
  It's open source, released under the MIT license and provided freely, without warranty or any official vendor-level support.</p>
  <p style="margin-bottom:0;">Learn more about Jamf's developer resources at 
  <a href="https://developer.jamf.com/jamf-pro/docs/jamf-pro-api-developer-resources#terraform-providers" target="_blank" rel="noopener">Jamf Pro API Developer Resources</a>.</p>
</div>

## **For the Learners**

### **Change management and GitOps mindset**

**For Mac Admins who are starting to learn Terraform, especially those diving in for the first time with the goal of managing their Jamf environments as Infrastructure as Code what advice or perspective would you share to help them build confidence and stay motivated along the way?**

> It goes without saying - Rome was not built in a day. Start small, install Terraform on your Mac, make that first `.tf` file and set a goal to create one resource like a Blueprint or a Category. Get used to how Terraform behaves when you plan and apply. Explore the different examples of how a project might be structured. There is no right or wrong - you might want to build modules that represent workflows, instead of the Jamf Pro object type approach I've made. Do what makes sense to you and your environment! If you're anything like me, you'll probably end up tearing down and rebuilding the whole thing many times.
> 
> The next important thing to remember is that GitOps is a total mindset shift from what you're probably used to. It's the other part of this skillset beyond writing the code itself. No more clicking around the UI and editing/saving policies. Lock that mouse cursor away. Create a branch, edit the code in your policy resource, commit, open a pull request to test and have your changes reviewed. When that pull request is merged, your changes get deployed to the production instance. Made a mistake? The commit history will show you exactly when and where it happened so it can be reverted.
> 
> If that all sounds daunting, don't worry. It takes time to get used to working this way. GitHub is free and you can practice as much as you like. Practice. Give yourself plenty of time and patience; you've never managed a Jamf Pro instance like this before and it's going to be very alien at the start. Even unsettling. You are not alone. The community has your back and we're all learning this together. You might be lucky enough to have access to colleagues in infrastructure teams using Terraform to manage other things in your organisation's stack; they can be a great resource too. Most importantly, enjoy the process.
> 

## **The Big Picture**

### **Where IaC is headed for Mac Admins**

**Stepping back a bit, how do you see Infrastructure as Code reshaping Mac Administration over the next few years?**

> IaC is making big waves in this space. I didn't realise it until during and after JNUC. Case in point; Tristan and I only expected around 50 attendees to our session and were pretty excited about it. Then we saw 250 registrations the day before! Our session was at 9am, Thursday morning, right after that big party the night before. Nobody in their right mind would want to put themselves through that, right? Wrong! 207 people joined us, in varying states of tiredness. It was great!
As for the future, who knows? I'm excited about it though. I think there will be organisations that go all in on this. I also want to ground myself a bit, remembering that this is just another way to do what we do already and have done for years. It's not a replacement, it's an alternative. It's very early days and there are many dragons to slay. We're still figuring this stuff out and it is by no means a silver bullet.
> 
> I'm looking forward to seeing how these new practices grow and mature. It's been great to play a small part in it
> 

**Do you think tools like Terraform will eventually become as essential to Jamf workflows as scripting or configuration profiles?**

> Absolutely. But probably not for everyone. It's easy to get caught up in the hype when a new shiny thing appears. But remember: do what works best for you and your organisation. I won't presume to tell you what that is, or what it should be. It's all about succeeding with this technology, and success comes in infinite forms.
> 
> If anything, it takes us full circle - back to the Terminal, working with text, like those before us did decades ago. And from where I'm looking, it appears to be catching on a bit…
> 

---

Seeing all the work Neil has done recently reminded me why this space feels so energizing right now. There's a genuine sense of shared discovery. Admins, engineers, and community contributors are all figuring out what IaC can mean for Apple management. The tooling is maturing quickly, the documentation is improving, and projects like Neil's are helping bridge the gap between curiosity and confidence. For me, that's what the next phase of my own learning has been all about: moving from exploration to application, and realizing that Terraform isn't just about code – it's about clarity, repeatability, and community collaboration at scale.

> **Coming Up Next:** I’ll be continuing this series with an interview featuring **Dafydd Watkins (ShocOne)** of **Deployment Theory**, one of the maintainers of `terraform-provider-jamfpro` and `terraform-provider-microsoft365`.  
  We’ll explore how community-driven projects are shaping the future of Infrastructure-as-Code for Apple admins, and where Terraform training for Jamf Pro is headed next.  
{: .prompt-info }
---

## Resources

- Jamf – **Managing Jamf Configuration with Terraform: An Introduction**  
  <https://trusted.jamf.com/docs/managing-jamf-configuration-with-terraform-an-introduction>

- Jamf blog – **Automating Apple endpoints management: Git, CI/CD and Terraform**  
  <https://www.jamf.com/blog/automate-apple-endpoints-terraform-gitlab-cicd-jamf-pro/>

- Neil Martin – **Sound Mac Guy**  
  <https://soundmacguy.com/>

- Repos – **terraform-jamfpro-starter**  
  <https://github.com/neilmartin83/terraform-jamfpro-starter>  
  **terraform-jamfplatform-examples**  
  <https://github.com/neilmartin83/terraform-jamfplatform-examples>
