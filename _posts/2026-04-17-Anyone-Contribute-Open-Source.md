---

title: "You Do Not Need to Write Code to Contribute to Open Source"
date: 2026-04-17 16:40:00 -0400
description: "If a tool matters to your organization, speaking up about bugs, friction, and missing features counts as a real contribution."
categories: [Mac Management]
tags: [macOS, MacAdmins, Apple IT, Apple administration, Community, Open-Source]
image:
  path: /assets/img/postimages/How-To-Contribute-Open-Source-Banner.png
  lqip: /assets/img/postimages/How-To-Contribute-Open-Source-Banner.png
  alt: A replica of the Apple Notes App with the description of the blog post title.
  
---

## An Assumption Nobody Questions

I am sure many people in the Mac Admin community treat open-source contributions as something that only happens when code is involved. If you are not opening pull requests, fixing bugs yourself, or maintaining a repo, it can be easy to feel like you are just a consumer on the outside. I used to think that way too.

But that framing misses most of what actually makes open source software improve, especially in Mac Admin work, where so many of us depend on community-built tools every single day.

Maintainers are good at what they do. They are not good at knowing what they cannot see. A developer building a tool in their own lab, against their own test fleet, with their own assumptions, may or may not automatically know how that tool behaves in a 5,000-seat enterprise, or what happens when it collides with a compliance baseline, or where a workflow falls apart when it meets real users at scale. That context comes from the people in the field. That means you.

---

## What I Actually Did

The clearest way to make this argument is to show what it looked like in practice.

In the **Jamf CLI**, I [raised a discussion](https://github.com/Jamf-Concepts/jamf-cli/discussions/96) about support for marking an unmanaged system as managed. That specific gap would greatly benefit my team, which regularly encounters devices enrolling in Jamf Pro as unmanaged for various reasons. While I was pitching a feature, I was also describing operational reality.

With **SAP Privileges**, I asked how a new [revocation feature](https://github.com/SAP/macOS-enterprise-privileges/discussions/257) would behave for excluded users in environments with stricter authentication and lock policies. Different environments make different assumptions, so sharing how I intend to use the tool and new feature would directly impact my organization.

With **Mac Health Check**, I [opened an issue](https://github.com/dan-snelson/Mac-Health-Check/issues/81) after noticing behavior that did not account for non-Platform SSO environments. Left unchecked, it could result in bad data being written back into Jamf. I was not clever. I just described what I was seeing and explained the impact I was directly facing.

In each case, the maintainer got something they may not have had during publishing the release: a real environment, a real problem, and a clear explanation of why it mattered. None of that required me to write a single line of code.

---

## It Does Not Have to Be Formal

Some of the most useful contributions I have made did not happen through GitHub at all.

Through ongoing conversations with Kevin White around **super** and **pseudo**, and by staying active in the related MacAdmins Slack discussions, I have seen functionality enhancements land that directly helped my organization. That happened because people were present in the conversation, testing ideas, asking questions, and raising friction before it became a bigger problem.

Dan Snelson has made this point directly, and in more than one place. His December 2025 post, ["Your 2026 Mac Admin Open Source Journey: From Beneficiary to Jedi-Ninja Maintainer,"](https://snelson.us/2025/12/jedi-ninja-are-you-hmmm/) lays out a self-assessment that places admins on a spectrum from Beneficiary to Jedi-Ninja Maintainer. The point is not to make people feel behind. It is to show them how close they already are to the next step. As he put it: *"Start where you are. If you've benefited from open source projects and don't know how to contribute, remember that feedback — any feedback — is a gift."*

I had the chance to go deeper on this in an interview with Dan published earlier this year: [From Beneficiary to Maintainer: A Dialog with Dan Snelson on Open Source and the Mac Admin Community](https://tonyyo11.github.io/posts/102406-DanKSnelson-OpenSource-Community/). One exchange that stuck with me was when I asked him what he would change about the Mac Admin ecosystem. His answer was not "get more people writing code." It was: remove the friction from beta testing. Get more people in the loop, testing real builds, and saying what they found.

That framing is useful. Too many admins still assume contributing means writing the fix yourself. It *can* mean that. But, it can also mean helping a maintainer understand what the tool actually needs to do to serve a wider set of real users.

Feedback is useful. Context is useful. Testing is useful. Thoughtful feature requests are useful. Those things shape a project's direction in ways that are easy to underestimate.

---

## Why Admins Hold Back

A lot of people stay quiet because they do not want to sound like they are complaining. Or they assume the answer will be no. Or they feel like they have not earned the right to ask for anything if they are not directly contributing code.

That mindset does not help anyone, including the maintainers.

Open source projects need honest feedback loops. They need users who are willing to say: here is what I am seeing, here is what is missing, here is why it matters. Without that, maintainers are left building against incomplete information. Your environment, your use case, your edge case, that is data they do not have until someone speaks up.

---

## How to Actually Do It

If you have never filed an issue or opened a discussion on a project you use, here is what works if you don’t have any other template to follow:

- **Describe your environment.** macOS version, MDM platform, fleet size, any other setup. The more specific the better.
- **Show expected vs. actual behavior.** What did you think would happen? What actually happened? Include logs or output if you have them and are allowed to share them (sanitized of course).
- **Explain the organizational impact.** This is the part most people skip. Is this blocking a rollout? Causing compliance gaps? Creating extra manual work? Say so. That context is what moves something from "nice to know" to "worth fixing."
- **Be specific about what you are asking for.** Not every request needs to be a fully scoped feature. Sometimes it is just: "Is this the intended behavior? Because in our environment it causes X."

> **Tip:** If you are unsure whether something is a bug or expected behavior, open a discussion first rather than an issue. It keeps the signal cleaner for the maintainer.
{: .prompt-tip }

---

## Closing Thought

The projects I depend on most got better because someone said something. Sometimes that was me. Sometimes it was someone in a Slack thread I barely knew. None of us was touching the code. We were describing what we were actually seeing.

That turned out to be enough.

If there is a tool your team depends on, and something about it could be better, say so. You may not be able to implement every request. Not every bug gets fixed right away. But a well-explained issue or discussion puts a real-world need on the maintainer's radar, and sometimes that is exactly what leads to the next improvement.

Open source needs maintainers. It also needs users who are willing to show up and be honest about what they are experiencing. If you are using the software, you already have something worth contributing.

If you haven't taken Dan's self-assessment yet, check it out at [snelson.us](http://snelson.us). My guess is that most people reading this are already Explorers or Emerging Contributors and just do not know it. You do not need a GitHub handle full of commits to matter to an open source project. You just need to show up and say something useful.