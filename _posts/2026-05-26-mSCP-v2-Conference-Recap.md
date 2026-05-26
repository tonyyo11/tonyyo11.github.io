---

title: "mSCP 2.0 Is Almost Here: Highlights from Mac Admins Day 2026"
date: 2026-05-26 6:00:00 -0400
description: "A Mac Admin's recap of NIST's mSCP Mac Admins Day 2026: the 2.0 launch timeline, what changed under the hood, new community tools, and what you need to review before June 23."
categories: [Mac Management]
tags: [macos, compliance, mscp, nist, macadmins, mace, jamf, intune, security, conference, disa, mace, workspaceone, zentral, bsi, cis, benchmarks, baselines]
image:
  path: /assets/img/postimages/mSCP_Logos_Catalog02.jpeg
  lqip: /assets/img/postimages/mSCP_Logos_Catalog02.jpeg
  
---

Last year, I was in Rockville, Maryland, at the inaugural mSCP Developer Conference, meeting people in person for the first time and walking away inspired about where the project was headed. This year, the event went virtual, and Mac Admins Day was the second of two days focused specifically on practitioners who use mSCP in their environments.

If you missed it, this post is for you.

Day Two was aimed squarely at Mac admins: less deep-dive architecture talk, more “here’s what’s changing and what you need to know.” The mSCP team walked through the 2.0 release timeline, showcased community tools built on top of the project, and delivered live demos that made the improvements feel tangible. Here’s my recap.

## The Launch Timeline

The biggest question going in was simple: when does mSCP 2.0 actually ship?

The answer is June 23, 2026. That is when the `dev_2.0` branch is merged into `main` and becomes the official release.

To put that in context, the alpha started back on March 25, 2025. The Fall 2025 OS release brought in the beta. Today, the best way to describe the state of things is to look at it from the perspective that we are sitting in the release candidate phase. After June 23, macOS 27, iOS 27, and visionOS 27 will all be built from the 2.0 structure going forward.

For the 1.0 branch, OS-specific branches are still receiving fixes, and there will be one final merge into those branches before end-of-life. The EOL lands around the Fall 2026 OS release cycle. If your organization is still on mSCP 1.0, you have runway, but the clock is running.

> If you maintain custom baselines or scripts tied to the 1.0 branch structure, start planning your migration now. The directory structure, YAML format, and CLI have all changed.
{: .prompt-warning }
> 

## What Actually Changed in 2.0

The core summary from the session was this: v1.0 made generating artifacts possible. v2.0 makes the data queryable.

That framing helped the release click for me. The 1.0 architecture was built to produce outputs such as PDFs, configuration profiles, and compliance scripts. It did that well. But the underlying data was harder to build on, especially if you wanted to integrate mSCP with other tools or reporting workflows.

2.0 rewrites that foundation.

**Branch Unification**

There is now one main branch instead of separate macOS Sonoma, Sequoia, and other OS-specific branches. If you have ever had to re-run baseline generation against multiple branches to stay current, you know why this matters.

**Unified YAML Rule Files**

Rules no longer live in OS-specific silos. A single YAML file can now express platform applicability, eliminating much duplication for anyone maintaining custom content.

**New Directory Structure**

There is a source folder reorganization in 2.0. If you have automation or scripts that reference specific paths in the mSCP repo, those will need to be reviewed and updated.

**JSON Vendor Manifest**

mSCP 2.0 includes a single export file that tools can consume programmatically. This is one of the bigger changes for vendors and community tools because it gives them a common structure to work from, rather than requiring everyone to build their own parser.

**Localization**

English, German, Dutch, French, and Italian are supported at launch, using Apple’s translation APIs. Dark mode support is also included for PDF and HTML outputs.

**Markdown Export**

Documentation can now be exported to Markdown in addition to PDF, HTML, and AsciiDoc. This is useful if you store compliance documentation in a wiki, GitHub repo, or internal documentation platform.

**Official Schema Files**

Schema definitions are now published, which gives developers building tooling on top of mSCP a clearer contract to code against.

**Libraries and Classes**

The older copy-and-paste approach to shared code is replaced with proper libraries. This matters more for developers than day-to-day admins, but it should make the overall project easier to maintain.

## The New CLI

The biggest change for most admins will be the CLI. Multiple separate scripts, s`uch` as `generate_guidance.py and generate_baseline.py,` are replaced by a single `mSCP.py` controller.

The workflow is cleaner than 1.0, where it was easy to run scripts in the wrong order and end up with unexpected output. In 2.0, the flow is closer to:

1. Create or load a baseline.
2. Generate guidance from that baseline.
3. Map rules to controls.
4. Optionally generate SCAP content.

Baseline defines what you are building. Guidance creates the human-readable documentation. Mapping aligns the rules to control frameworks. SCAP generates machine-consumable compliance content.

A few flags worth knowing:

- `L` lists available keywords and tags.
- `T` launches interactive tailoring mode for Organizational Defined Values.
- `os_name` and `os_version` specify the target platform and version.
- `reference` generates rule IDs using benchmark references instead of internal names.

> Tip: Run `mSCP.py -L` before building a baseline to confirm what keywords are available for your target platform and version.
{: .prompt-tip }
> 

Before publishing this, I would recommend double-checking the exact flag syntax against the 2.0 CLI help output. Some examples shown during sessions and documentation may use slightly different single-dash or double-dash formatting, and that is the type of thing admins will copy directly into a terminal.

## Installation: Containers Are Now the Recommended Path

One of the pain points with mSCP 1.0 was Python dependency management. If you have ever fought with conflicting package versions on your admin machine, you will probably appreciate that containers are now the first-class way to install.

[Apple Container](https://github.com/apple/container) support is now available, and the mSCP team highlighted it during the session. The setup uses commands like `container system start` and `container run -it --volume` to mount your working directory into the sandbox.

Three installation methods are supported:

1. Container, using Docker, Apple Container, or Podman
2. Python virtual environment using `pip install`
3. Direct Python installation

For most admins, the container option eliminates a whole category of dependency problems. It also makes it easier to standardize the build process across different admins or documentation workflows.

Option two is still a reasonable fallback if you prefer to stay native.

> Containers and VMs are only needed on the machines creating guidance and baselines. End-user devices do not need any of this. Benchmarks are deployed through your MDM the same way they always were.
{: .prompt-info }
> 

Apple’s container tutorial is available here: [https://github.com/apple/container/blob/main/docs/tutorial.md](https://github.com/apple/container/blob/main/docs/tutorial.md)

## Output Formats

One of the live demos showed just how many output formats mSCP 2.0 can produce from a single baseline.

The full list includes:

- PDF, with dark mode support
- HTML, with dark mode support
- Markdown
- Excel spreadsheet
- Mobile configuration profiles, signed and unsigned
- DDM configurations
- Compliance scripts with check, fix, and remediate modes
- JSON manifest files

The DDM output is worth calling out separately. As Apple continues moving more management behavior toward Declarative Device Management, having mSCP produce DDM-ready configurations directly removes a manual translation step.

That does not mean every organization will immediately deploy DDM-based compliance settings. But it does mean mSCP is being built toward where Apple device management is going, not only where it has been.

## The Programmatic API

For admins who want to do more than run scripts, 2.0 ships with a Python API built on Pydantic base models.

This unlocks the ability to:

- Filter rules by platform, benchmark, NIST control, or tags
- Compare rule differences between OS versions
- Feed compliance data into external systems like SIEMs or CMDBs
- Generate custom reports that are not tied to a specific baseline
- Automate rule authoring and validation workflows

The API reference lives here:

[https://pages.nist.gov/macos_security/mscp-2/overview/](https://pages.nist.gov/macos_security/mscp-2/overview/)

Most admins will probably live in the CLI and standard outputs. But the API is what makes the next generation of integrations possible. If your organization wants to treat compliance data as something queryable instead of just something exported into a static document, this is where 2.0 gets interesting.

## Community Tools Being Built on mSCP 2.0

![scary-movie-shawn-wayans.gif](/assets/img/postimages//scary-movie-shawn-wayans.gif){.right}
This was one of the more interesting parts of the day. Several teams have already built tools on top of mSCP, and the demos gave a good picture of where the ecosystem is going. It was pretty fun to see someone watching the CLI demo and stating they would need a GUI, and the response was: *“Just hang on the call for a bit more. wink wink”.*

The important point is not just that new tools exist. It is that mSCP 2.0 gives those tools a more stable foundation to build from.

### M.A.C.E

[M.A.C.E](https://github.com/MACE-App/MACE/) is a native macOS app built on top of mSCP 2.0. The demo showed a “New Compliance Project” wizard that walks you through selecting a platform, choosing a version, and selecting a compliance framework.

The platform options included macOS, iOS/iPadOS, visionOS, and Application, the latter of which was marked as new.

From there, you can enable or disable individual rules, add comments and flags, and export directly to MDM platforms like Jamf Pro, Workspace ONE, and Intune. There is also a custom rule creation wizard and a faster Swift-based engine running alongside the Python option.

If you have been generating baselines from the command line and wish there were a GUI, M.A.C.E is worth watching.

### Contour

[Contour](https://github.com/macadmins/contour) is a command-line tool described as a Swiss Army knife for Apple device management configurations. It customizes configuration profiles for your organization, supports multiple MDM platform modes, and can slice compliance scripts by category for more organized deployment.

Jamf and Fleet GitOps workflows were both called out during the demo.

### Munki mSCP Generator

A team out of the Netherlands built a tool that converts mSCP baselines into [Munki items](https://github.com/jc0b/munki-mscp-generator), an infrastructure-as-code and MDM-agnostic format.

One thing I liked about this approach is that it generates a separate script for each rule. That can make troubleshooting easier than working with one large monolithic compliance script. The pipeline also runs through GitLab automation, which fits well for organizations already managing compliance content through source control.

### AI Integration Demo

One of the more interesting demos showed Claude connected to mSCP 2.0 via the API to perform live fleet compliance analysis against the GitHub repository. The workflow included automated profile creation with safety recommendations.

The structured API was highlighted as a way to reduce hallucinations by constraining the model to validated rule data rather than free-form guesses. That distinction matters. Schema validation does not magically make AI perfect, but it does give the model a safer, more predictable data structure to work with.

## Using mSCP with Microsoft Intune

A question that comes up regularly in the Mac Admins community is whether mSCP works with Microsoft Intune.

The short answer from the session was: yes, but it is not a first-class integration.

Configuration profiles can be imported into Intune, and scripts can be deployed through Intune’s script management. There are some practical constraints, with character limits on large scripts being the main one. Compliance flags can also be set through configuration profiles.

If you are using Intune, expect to test the script size, profile import behavior, and reporting expectations before treating mSCP output as part of a production workflow.

A community reference guide was also shared:

[https://intuneirl.com/secure-contain-protect-your-data-deploy-mscp-with-intune/](https://intuneirl.com/secure-contain-protect-your-data-deploy-mscp-with-intune/)

If your organization manages Macs through Intune, that guide is worth bookmarking.

## Who Should Start Planning Now

If you only consume the default outputs, you can probably start by reviewing the new CLI and container workflow.

If you maintain custom baselines, custom scripts, wiki exports, CI jobs, or automation that references the old repo layout, start testing against 2.0 before June 23.

If you are responsible for compliance reporting, pay close attention to the JSON manifest, Markdown export, Excel output, and API. Those are the pieces most likely to change how you build repeatable reporting.

The biggest risk is not that 2.0 is hard to use. The bigger risk is assuming that existing 1.0 workflows will continue to work without review.

## What Is Still Coming After 2.0

The “Future” slide from the session listed three things on the post-2.0 roadmap:

- Removing the Ruby dependency
- Expanding the API
- Additional baselines and benchmarks

Removing Ruby has been a long-standing goal. Between Python and Ruby being required for different parts of the project, dependency management has been a friction point. Finishing that cleanup should make the tool more approachable for admins who are not deeply familiar with both runtimes.

## Getting Involved

The mSCP community is active and welcoming. If you want to stay connected, these are good places to start:

- MacAdmins Slack: `#macos_security_compliance`
- CIS community calls: Fridays at 9 AM Eastern, with free registration
- GitHub: [https://github.com/usnistgov/macos_security](https://github.com/usnistgov/macos_security)

If you are heading to Penn State Mac Admins Conference, there are two mSCP sessions on the schedule: a full-day workshop on Monday and a “[mSCP 2.0: The Quest for More Compliance](https://psumac2026.sched.com/event/2CY5u/mscp-20-the-quest-for-more-compliance)” session on Tuesday. Both will include hands-on mSCP 2.0 building.

Even if you never submit code, asking questions, testing release candidates, and reporting what works or breaks in real environments helps the project mature.

## Wrap-Up

Last year, the conference was about previewing what 2.0 could become. This year, it is about shipping it.

With June 23, 2026 set as the move from `dev_2.0` to `main`, this is no longer just a preview cycle. The tooling ecosystem is growing, the output formats have expanded, and the project is moving toward a more flexible and queryable foundation.

If you have been waiting to adopt mSCP, or if you have been running on 1.0 and putting off migration planning, now is the time to start getting familiar with the 2.0 structure.

Start with the getting-started docs and go from there:

[https://pages.nist.gov/macos_security/welcome/getting-started/](https://pages.nist.gov/macos_security/welcome/getting-started/)

## Resources

Primary documentation: [https://pages.nist.gov/macos_security/welcome/getting-started/](https://pages.nist.gov/macos_security/welcome/getting-started/)

GitHub repository: [https://github.com/usnistgov/macos_security](https://github.com/usnistgov/macos_security)

mSCP 2.0 API overview: [https://pages.nist.gov/macos_security/mscp-2/overview/](https://pages.nist.gov/macos_security/mscp-2/overview/)

Apple Container tutorial: [https://github.com/apple/container/blob/main/docs/tutorial.md](https://github.com/apple/container/blob/main/docs/tutorial.md)

mSCP with Intune community guide: [https://intuneirl.com/secure-contain-protect-your-data-deploy-mscp-with-intune/](https://intuneirl.com/secure-contain-protect-your-data-deploy-mscp-with-intune/)