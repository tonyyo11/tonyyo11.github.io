---
title: "Prepping for Learning Terraform and an Exercise on Automation [Part 2]"
date: 2025-11-06 16:00:00 -0400
description: "Somewhere Between a Jamf Policy and a Plan File: My Journey on Learning Terraform as a Mac Admin"
categories: [Mac Management]
tags: [Jamf, Jamf Pro, MDM, DDM, macOS, IaC, Infrastructure as Code, Terraform]
image:
  path: /assets/img/postimages/Prepping_for_Learning_Terraform_Part2.png
  lqip: /assets/img/postimages/Prepping_for_Learning_Terraform_Part2.png
  alt: Prepping for Learning Terraform and an Exercise on Automation [Part 2]. Photo by Google DeepMind on Unsplash
---

In the [first part](https://tonyyo11.github.io/posts/October-Learning-Terraform/) of this series, I talked about dipping my toes into Infrastructure as Code (IaC) and starting to understand why Terraform has been gaining so much traction across IT and DevOps circles. This time, I’m diving deeper into what that means for me as a Mac Admin and someone more accustomed to managing policies and profiles through Jamf Pro than writing infrastructure configuration files.

Because here’s the truth: **Mac Admins spend a lot of time clicking.**

We click through configuration profiles, policies, scripts, and patch workflows, often making small, repetitive updates across environments. Terraform changes that. 

This post is not meant to serve as any sort of how-to. There are far better resources for that. But I hope to share my learning journey and progress as I become more fluent in Terraform as a whole. 

---

## **Learning Roadmap: Building a Foundation for IaC**

After completing the quick [**Terraform Fundamentals**](https://www.coursera.org/projects/googlecloud-terraform-fundamentals-hkxku) project on Coursera (a one-hour Google Cloud exercise), I realized I needed a more structured approach to learning. I wanted to build toward something tangible. A way to manage Jamf Pro through Terraform and not just play with cloud examples I’d never use in production.  I began looking at what longer-form course to take. Because I have various Google-issued course certificates and specializations, I’ve decided, for now, to stick with their content on the Coursera Platform. 

To plan my path forward, I created a shortlist of courses organized by depth and learning style:

**Quick Wins (Hands-On Projects)**

- [*Terraform for Absolute Beginners*](https://www.coursera.org/projects/terraform-for-absolute-beginners) – Coursera Project Network (2 hours)
- [*Infrastructure as Code with Terraform*](https://www.coursera.org/projects/googlecloud-infrastructure-as-code-with-terraform-jwta0) – Google (2 hours)

**Skill Builders (Deeper Labs & State Management)**

- [*Getting Started with Terraform for Google Cloud*](https://www.coursera.org/learn/getting-started-with-terraform-for-google-cloud) – Google (6 hours)
- [*Managing Terraform State*](https://www.coursera.org/projects/googlecloud-managing-terraform-state-a2qrq) – Google (1 hour)
- [*Interact with Terraform Modules*](https://www.coursera.org/projects/googlecloud-interact-with-terraform-modules-7tizt) – Google (1 hour)
- [*Automating Network Deployments with Terraform*](https://www.coursera.org/projects/googlecloud-automating-the-deployment-of-networks-with-terraform-ukngs) – Google (1 hour)

**Long-Term Growth Tracks (Specializations & AI Integration)**

- [*Preparing for Google Cloud Certification: Cloud Engineer* – Google](https://www.coursera.org/professional-certificates/cloud-engineering-gcp) (multi-week)
- [*Terraform Masterclass: From Beginner to Advanced*](https://www.coursera.org/specializations/packt-terraform-masterclass-from-beginner-to-advanced) – Packt (4 weeks)
- [*Accelerate Terraform Development with GitHub Copilot and AI*](https://www.coursera.org/learn/packt-accelerate-terraform-development-with-github-copilot-and-ai-hlcf8) – Packt (8 hours)

Now, am I going to complete each and every single one of these courses? Probably not. But mapping this out gave me a way to see how these courses fit together and where they could eventually support my Jamf use cases. Plus, since I already use Coursera Plus, I can explore beyond Terraform into adjacent areas like AI and leadership. 

I completed “*Getting Started with Terraform for Google Cloud,*,” which included a video series guiding me through seven modules (or lessons). This one took some time to get through, and I felt the content was great at establishing definitions and use cases. While it focused on Google Cloud Platform, the course was still general enough that you won’t get lost if you’ve never used it before. 

The thing I’ve loved the most is that Terraform is not a programming language but just a configuration syntax like JSON. While there are a ton of fundamentals one should know before getting into Terraform (like understanding Git, CI/CD concepts, the platforms intended to be managed, etc.), it’s not a brand-new coding language that now has to be learned. Picking up JSON, for me, was 1000x easier than trying to learn a scripting language and the like. That also gives me far more hope that training more junior-level engineers to submit changes in the Terraform format won’t be as

And speaking of Jamf-specific learning, **Deployment Theory** is currently developing a full [*Terraform for Jamf Pro* training series](https://github.com/deploymenttheory/terraform-training-jamfpro). When that is flushed out and completed, I am sure it will be a go-to resource for Mac Admins who want to learn this the right way. And on top of that, there are already great resources out there for admins such as [**deploymenttheory/terraform-demo-jamfpro-v2**](https://github.com/deploymenttheory/terraform-demo-jamfpro-v2), [**neilmartin83/terraform-jamfplatform-examples](https://github.com/neilmartin83/terraform-jamfplatform-examples),** and **[nielmartin83/terraform-jamfpro-starter](https://github.com/neilmartin83/terraform-jamfpro-starter).**

---

## **Hands-On Practice: My Jamf Beta Lab**

Theory is great, but it’s only when you start applying Terraform to something you care about that the real learning happens.

I maintain a **dedicated Jamf Pro Beta server** that is completely isolated from my corporate infrastructure. This is my playground: a place where I can destroy everything, start over, and not worry about breaking anything important. It’s the perfect space for experimentation as I learn.

Inside Jamf, I created several API roles to use with Terraform.

> **Note:** This setup is *not* production-grade. When learning Terraform, always research the specific permissions required by the provider and apply the principle of least privilege. My goal here was exploration, not enforcement.
{: .prompt-danger }

<!-- Two-column image grid (responsive) -->
<style>
  .img-grid {
    display: grid;
    grid-template-columns: repeat(2, minmax(0, 1fr));
    gap: 14px;
    margin: 1rem 0 1.25rem;
  }
  @media (max-width: 720px) {
    .img-grid { grid-template-columns: 1fr; }
  }
  .img-grid figure {
    margin: 0; 
    display: flex; 
    flex-direction: column; 
    gap: 6px;
  }
  .img-grid img {
    width: 100%;
    height: auto;
    border-radius: 6px;
  }
  .img-grid figcaption {
    font-size: 0.9rem;
    color: #666;
    line-height: 1.35;
  }
</style>

<div class="img-grid">
  <figure>
    <img src="/assets/img/postimages/Screenshot_2025-10-25_at_7.14.37_AM.png">
  </figure>
  <figure>
    <img src="/assets/img/postimages/Screenshot_2025-10-25_at_7.16.41_AM.png">
  </figure>
</div>

<div class="img-grid">
  <figure>
    <img src="/assets/img/postimages/Screenshot_2025-10-25_at_7.19.24_AM.png">
  </figure>
  <figure>
    <img src="/assets/img/postimages/Screenshot_2025-10-25_at_7.20.52_AM.png">
  </figure>
</div>

<div class="img-grid">
  <figure>
    <img src="/assets/img/postimages/Screenshot_2025-10-25_at_7.20.52_AM%201.png">
  </figure>
  <figure>
    <img src="/assets/img/postimages/Screenshot_2025-10-25_at_7.23.14_AM.png">

  </figure>
</div>

![Screenshot 2025-10-25 at 7.24.11 AM.png](/assets/img/postimages/Screenshot_2025-10-25_at_7.24.11_AM.png)

I’ve then assigned each role to a singular Terraform API Client:

![Screenshot 2025-10-25 at 7.24.46 AM.png](/assets/img/postimages/Screenshot_2025-10-25_at_7.24.46_AM.png)

With the roles in place, I forked **Deployment Theory’s demo repository** and validated that the demo data could run successfully. Running my first Terraform plan in Visual Studio Code and seeing the output felt incredible. Not because of what it did, but because of what it *represented*.

![Running `terraform plan` on the demo data via Visual Studio Code](/assets/img/postimages/vscode_tf_plan.png)
_Running `terraform plan` on the demo data via Visual Studio Code_

For years, “infrastructure as code” sounded intimidating. But when you see Terraform create Jamf Pro resources automatically, you realize it’s not programming, it’s just structured configuration.

It felt *too easy*… and that’s when it clicked. And I realize I was using demo data, and the complexity lies in how deeply I want to make use of the tool. 

After running `terraform apply` and accepting the changes, I was able to see that within my Jamf Pro Server, I have new content with a `tf` prefix indicating it was generated and is managed via Terraform.

![Screenshot from Jamf Pro showing Terraform created new building entries from demo data](/assets/img/postimages/tf_jamf_buildings.png)

![Screenshot from Jamf Pro showing Terraform created a new policy](/assets/img/postimages/tf_jamf_policies.png)

---

## **Lessons Learned: Terraform Meets Jamf**

Terraform lets you describe **what** your environment should look like, not **how** to build it. For Jamf admins, that’s transformative. Instead of manually creating and modifying items in the web console, you can define them once in `.tf` files and track every change through Git. Terraform allows you to focus on the design, and the outcomes, and not so much “make sure you click the buttons in the right order in Jamf.”

The version control alone is worth it. No more “guessing what changed” between profile versions. No more downloading old XMLs to compare differences. Terraform brings **accountability, repeatability, and transparency** to Jamf management — three words any compliance-driven Mac Admin loves to hear.

---

## **Broader Learning: People, Process, and AI**

While Terraform was the main focus, I also completed [Google’s *People Management Essentials*](https://www.coursera.org/specializations/google-people-management-essentials/paidmedia) course. I may not be a formal manager, but as a team lead and mentor, I’m always looking for ways to grow people alongside technology. The course provided quick yet meaningful insights into feedback, motivation, and communication, all critical when introducing new processes like Terraform to a team.

At the same time, I started exploring AI tools for engineering workflows. Courses like [*AI Fluency: Framework & Foundations*](https://anthropic.skilljar.com/ai-fluency-framework-foundations) and [*Claude Code: Software Engineering with Generative AI Agents*](https://www.coursera.org/learn/claude-code) helped me understand how tools like **Claude** or **ChatGPT** can assist with Terraform configuration validation, documentation generation, and automation planning.

I’ve even created a dedicated Claude project for my Terraform-with-Jamf setup to help me expand and test my learning and assist with creating new ideas of how I can build out my test server.

---

## **Next Steps: The Road Ahead**

Over the coming months, I’ll be:

- Diving deeper into the [Deployment Theory Terraform Provider documentation](https://github.com/deploymenttheory/terraform-provider-jamfpro/tree/main/docs).
    
    ![image.png](/assets/img/postimages/terraform-provider-jamfpro-policy.png)
    
- Learning how to migrate existing Jamf Pro configurations into Terraform-managed HCL definitions.
- Exploring how Git workflows and CI/CD pipelines can automate Jamf deployments safely.
- Experimenting with modules that could standardize policy sets, baselines, or even security compliance frameworks like CIS or STIG.

I’m not rushing the process. Terraform is one of those tools where understanding *why* and *how* matters more than how quickly you can deploy it. Each small success reinforces the mindset shift from “I manage devices” to “I manage infrastructure as code.”

---

## **Closing Reflections**

What excites me most about this journey isn’t just the automation, but rather the level of control. Terraform lets Mac Admins design outcomes, not just follow steps. It allows our work to be collaborative, reviewable, and scalable. And when paired with Jamf’s expanding Platform API, the potential only grows.

So, to any Mac Admin curious about Terraform: start small. Use a sandbox. Break things. Learn by doing. You’ll quickly discover that managing infrastructure as code feels a lot like writing a really well-structured configuration profile, but now just with a bit more power behind it.

---

### Additional Resources and Readings:

- MacAdmin Musings: [**Terraform 101: Resources and Data Sources](https://macadminmusings.com/blog/2025/10/28/terraform-101-resources-and-data-sources/) (Part 4)**
    - This post introduces the two most fundamental concepts in Terraform: `resources` and `data sources`. These form the basis of every configuration, and they’re how Terraform interacts with the real world. - Scott Blake
- MacAdmin Musings: [**Terraform 101: Command Line Interface](https://macadminmusings.com/blog/2025/11/05/terraform-101-command-line-interface/) (Part 5)**
    - The CLI is how you interact with Terraform: initializing a project, previewing changes, applying updates, and cleaning up resources. It’s also where you’ll see Terraform’s declarative nature in action by analyzing the difference between your configuration and reality, then taking the steps needed to bring them in sync.
- Sound Mac Guy: [**What’s new with terraform-provider-axm (AppleCare, that’s what)**](https://soundmacguy.wordpress.com/2025/11/06/whats-new-with-terraform-provider-axm-applecare-thats-what/)
    - Apple recently, and quietly bumped their [Apple School and Business Manager API](https://developer.apple.com/documentation/apple-school-and-business-manager-api) to 1.3+. Along came a [new endpoint](https://developer.apple.com/documentation/applebusinessmanagerapi/get-all-apple-care-coverage-for-an-orgdevice) for querying AppleCare coverage details for individual devices.