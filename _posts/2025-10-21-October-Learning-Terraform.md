---
Title: "Last Month: Prepping for  Learning Terraform and an Exercise on Automation [October 2025]"
date: 2025-10-21 11:00:00 -0400
description: "October Learning to go from Click-Ops to Infrastructure as Code — exploring how Terraform, Tart, and Jamf Pro can work together to automate Apple device management, testing, and CI/CD workflows."
categories: [Mac Management]
tags: [Jamf, Jamf Nation User Conference, JNUC, Jamf Pro, Jamf Security, API, Conferences, Mac Admins, APIs, Terraform, Platform API, Community, Blueprints, DDM, MDM]
image:
  path: /assets/img/postimages/Prepping_for_Learning_Terraform_Banner.png
  lqip: /assets/img/postimages/Prepping_for_Learning_Terraform_Banner.png
  alt: Prepping for  Learning Terraform and an Exercise on Automation. Photo by Federica Galli on Unsplash
---

# Concept: From Click-Ops to Code

For years, Mac Admins have managed Jamf Pro through what is often called “Click-Ops” — manually creating policies, configuration profiles, and smart groups through the web console. It works, but it doesn’t scale. At the beginning of the year, I told myself I wanted to learn Terraform, but I never got around to it. After attending the Jamf Nation User Conference (JNUC) 2025 in Denver, Colorado, I realized I really needed to take action. 

So, I set a goal: Over the next year, I will master Terraform and learn how to use it to manage everything within Jamf — from policies, configuration profiles, and App Installers to Blueprints and compliance benchmarks. With Jamf’s announcement of the Platform API and a new Terraform provider for it, this journey now extends well beyond Jamf Pro alone. In my [JNUC recap post](https://tonyyo11.github.io/posts/Elevate-with-Jamf-Lift-Off-at-JNUC-2025/#additional-resources), I shared a list of resources related to Terraform at the end. 

While at JNUC 2025, I attended a session specifically talking about Terraform. You can read about that session on Jamf’s blog: **Automating Apple endpoints management: Git, CI/CD and Terraform for an efficient Jamf Pro administration -**[https://www.jamf.com/blog/automate-apple-endpoints-terraform-gitlab-cicd-jamf-pro/](https://www.jamf.com/blog/automate-apple-endpoints-terraform-gitlab-cicd-jamf-pro/)

# Building a Terraform Playground

Over the past few weeks, I’ve been planning on how exactly I will handle my learning path. To prep for my Terraform learning, I needed a dedicated testing environment. Luckily, I’ve maintained a beta Jamf instance for personal learning and testing, where I can safely experiment with new Jamf Pro features before they’re generally released.

Historically, I used **UTM** to run macOS VMs, but after upgrading to macOS *Tahoe 26*, UTM became unstable. That’s when I discovered **Tart**, a lightweight virtualization tool built specifically for Apple Silicon that integrates beautifully with CI/CD workflows.

A few resources on using Tart to build and manage testable VMs can be found here: 

- [**The Cookbook: Baking Up Your Perfect Jamf Pro Test VM**](https://www.notion.so/Making-Your-Business-Case-for-Jamf-When-Your-Boss-Is-Asking-You-to-Explore-Other-Options-28a8d34e2344805b8f9dee120cdb7d52?pvs=21)
- **Silicon sandbox: Mastering Mac virtualization for Jamf workflows - Rob Potvin:** 
{% include embed/youtube.html id='7DqS9bG3bkg' %}

## Why Tart Matters for Terraform

Tart fits perfectly into my long-term Terraform plans. With Tart, I can create disposable, repeatable macOS VMs that enroll automatically into my Jamf environment. These VMs become the test subjects for Terraform-driven configuration changes, OS updates, and patch automation — without ever touching physical hardware or production systems.

## Setting up Tart and External Storage

So I got started. Following Rob Potvin’s example, I admit that I struggled trying to get Tahoe to put an MDM enrollment profile within Tahoe automatically, so I reverted to just storing a web shortcut on the desktop on all newly built VMs to enroll into Jamf Pro quickly. I did have to ensure that I set `tart` to store VMs onto an external hard drive because my 1TB Mac Studio is already becoming full. 

![Daisy Disk Screenshot Showcasing Storage Space on Internal and External Drive](/assets/img/postimages/DaisyDiskStorage.png)

Here’s how I configured Tart to live entirely on an external drive, freeing up local SSD space and ensuring I can scale as needed.

```bash
brew install cirruslabs/cli/tart
brew install cirruslabs/cli/orchard
brew tap hashicorp/tap
brew install hashicorp/tap/packer
export TART_HOME="/Volumes/Coruscant Dusk/.tart"
mkdir -p "$TART_HOME/vms" "$TART_HOME/cache/OCIs"
sudo chown -R "$USER":staff "$TART_HOME"
chmod 700 "$TART_HOME"
chmod 755 "$TART_HOME/vms"
```

## Creating Testable macOS Templates

I downloaded the [vanilla-tahoe.pkr.hcl](https://github.com/cirruslabs/macos-image-templates/blob/main/templates/vanilla-tahoe.pkr.hcl) file locally to my system and updated the `disk_size_gb` from 50 to 120 since my external drive has over 1TB of free space to play with. I’ve standardized all of my VM templates to **100GB minimum**. This provides enough disk space to test full macOS updates, including *Super* patch enforcement, while keeping performance stable over time.
Here’s a snippet of the updated `.hcl` file to help build a template VM.

```haskell
source "tart-cli" "tart" {
  from_ipsw    = "https://updates.cdn-apple.com/2025FallFCS/fullrestores/093-37622/CE01FAB2-7F26-48EE-AEE4-5E57A7F6D8BB/UniversalMac_26.0_25A354_Restore.ipsw"
  vm_name      = "tahoe-vanilla"
  cpu_count    = 4
  memory_gb    = 8
  **disk_size_gb = 120**
```

I’ve built a template VM for the last three major versions of macOS: Tahoe 26.x, Sequoia 15.x, and Sonoma 14.x. These base images are my “vanilla” macOS builds — clean, unenrolled, and identical. I can quickly clone them into test VMs for Terraform validation, Jamf automation testing, or super update evaluation. Each one includes a .webloc shortcut to my beta Jamf Pro server for fast manual enrollment when needed. Then, it was as simple as cloning the templates to build the testable VMs. 

Starting up the VM: `TART_HOME="$TART_HOME" tart run tahoe-jamf-01`

![Screenshot of a VM generated in Tart](/assets/img/postimages/tart-tahoe-jamf-01-vm.png)

I select the `.webloc` file to enroll in my Jamf Pro Server and it's off to the races.


# Preparing Jamf Pro for IaC

A clean environment helps reveal the real impact of each Terraform resource you apply. One should build their test environment to mirror production, deploying App Installers, a baseline of configuration profiles, and even compliance frameworks like CIS Level 1 & 2 — all while documenting what Terraform will eventually replace.

For the sake of learning, I cleared out my Jamf Pro (Beta) Server that I use as an environment for testing and validation without the use of corporate data and infrastructure. I highly advise all admins who want to stay as up to date as possible on Jamf Pro to have a beta server separate from your Dev and Production environments, where you can test with features you may not use within your own role, and have the freedom to break things and reset them as often as you may need.

With a nearly empty Jamf Pro server, I began building from the ground up for the purpose of replicating a production environment. I’ve selected a few Jamf App Installers to deploy, built a Device Compliance baseline on a tailored version of NIST 800-53 Moderate, deployed configuration profiles, and created a handful of simple policies.

I’ve built and deployed a few Jamf Blueprints, especially relating to Restrictions. You can read the Jamf Blog post that stems from a JNUC Session, **Configuration Profiles 3.0: Everything Everywhere All at Once**

[https://www.jamf.com/blog/configuration-profiles-3-0-blueprints-declarative-management/](https://www.jamf.com/blog/configuration-profiles-3-0-blueprints-declarative-management/)

The intention is to have as many automated items within Jamf Pro so that the server is regularly “doing something”. Apps need to be updated, OS updates need to be deployed, and so forth. I also have Workbrew set up to deploy Homebrew to the VMs. Both so I can remain up to date in learning the WorkBrew console, but also again, so that there is activity going on in the VMs to replicate a production environment without the need to deploy company applications and resources. 

Now I am ready for any potential testing and learning in a safe environment where if something drastically breaks, it’ll be okay. 

My goal isn’t just to automate Jamf — it’s to understand how each resource behaves when provisioned via API so that Terraform can manage it declaratively. With the foundation in place — Tart for virtualization, and Jamf Beta as the sandbox — I finally had the full stack ready to start experimenting with Terraform.

---

# The Learning Stack: Terraform + Jamf + Community

Learning Terraform can be intimidating at first, but the Mac Admins community is making it easier every day. These are the foundational resources guiding my journey. 

For resources on getting started with Terraform, I want to call out a series of blog posts by Scott Blake on the topic:

- Terraform 101: Introduction - [https://macadminmusings.com/blog/2025/10/14/terraform-101-introduction/](https://macadminmusings.com/blog/2025/10/14/terraform-101-introduction/)
- Terraform 101: Getting Started - [https://macadminmusings.com/blog/2025/10/14/terraform-101-getting-started/](https://macadminmusings.com/blog/2025/10/14/terraform-101-getting-started/)
- Terraform 101: Variables and Secrets - [https://macadminmusings.com/blog/2025/10/20/terraform-101-variables-and-secrets/](https://macadminmusings.com/blog/2025/10/20/terraform-101-variables-and-secrets/)

The posts are extremely timely, coming out the exact same day I decided to dive into learning Terraform. 

On Slack, more timely messages have popped up, this time in [#terraform-provider-jamfpro](https://macadmins.slack.com/archives/C06R172PUV6/p1760609979757959).

On Slack, Gordon Deacon (Lloyds Banking Group) shared open-source Terraform training materials for Jamf admins. The project aims to help teams move from “understanding Terraform basics” to “structuring Jamf resources and migrating instances.” You can find the training directly on GitHub, but note it is still a work in progress. [https://github.com/deploymenttheory/terraform-training-jamfpro](https://github.com/deploymenttheory/terraform-training-jamfpro)

I’m following these along with Coursera’s IaC and Terraform courses. While most content focuses on AWS or Google Cloud, the fundamentals translate well — especially when paired with hands-on Jamf experimentation.

Now that I have a few fresh VMs enrolled in my Jamf Pro Server, I am ready to conduct further tests, including evaluating the latest version of Super 5.1.0-rc1 and exploring Terraform. Note that for the majority of my testing, I’ll end up running a singular VM in GUI mode, while having other VMs running on a timer in headless mode, automatically starting and stopping on a script. This can be accomplished by having automatic login enabled to make things more streamlined and ensure VMs don’t become stale within Jamf Pro.

![Screenshot of multiple VMs generated in Tart](/assets/img/postimages/tart-multi-vm-view-example.png)

# Dipping my toes into the Terraform Waters

As mentioned, I have subscribed to Coursera Plus for a whole year, which allows me to take a plethora of courses that are relevant to learning Terraform and Infrastructure as Code (IaC). I found a few quick projects that take a little under two hours to complete. I decided to do these to get some quick hands-on experience with running Terraform commands from a fundamental perspective before learning the full ins and outs of things.
The first was [Terraform Fundamentals](https://www.coursera.org/projects/googlecloud-terraform-fundamentals-hkxku), which was built by Google Cloud Training. This took about half an hour to complete and ran through a few beginner Terraform commands and the structure of Terraform. As I continue to go through longer courses, I plan to split my time going through smaller mini-projects as I learn along the way.

## Quick Reflections

My first big lesson: learning Terraform isn’t just about syntax — it’s about **thinking declaratively**. It forces you to describe what your environment *should be*, not the steps to get there. That’s a huge mindset shift for anyone used to scripting or clicking through Jamf Pro. I’ve also learned the value of small wins. Completing 30–60 minute labs gives you visible progress while reinforcing core concepts before tackling full infrastructure builds.

## What’s Next: Scaling for Jamf Pro Deployments

With Tart now stable and my Jamf Beta instance nice and tidy, I’m ready to start managing real resources through Terraform. My focus for the next few months will be:

- Defining Jamf Pro resources in code (policies, profiles, App Installers, groups).
- Exploring the new Jamf Platform API provider.
- Building a proof-of-concept CI/CD pipeline to automatically apply and validate Terraform changes.

The long-term vision is to assess the feasibility of integrating these principles into my team’s daily workflow, transitioning from “Click-Ops” to fully auditable, version-controlled automation.

I’ll be documenting every step along the way and sharing sanitized examples of my configurations on GitHub to help other admins accelerate their own Terraform journeys. Every automation journey starts small — the next step in mine happens to begin with Tart, Terraform, and a goal to turn Jamf management from Click-Ops into Code.

---

# Mac Tip of the Day

Use `tart clone <template> <new-vm-name>` instead of rebuilding — it’s nearly instant and preserves your macOS updates, making it perfect for disposable test VMs.
