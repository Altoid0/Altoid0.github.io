---
layout: post
author: Altoid0
tags: [ccdc, blue team, competitions]
---

#### Table of contents
- [Building on success](#building-on-success)
  - [Speed](#speed)
- [Strategy](#strategy)
  - [Team Composition](#team-composition)
    - [Web Lead](#web-lead)
    - [Database Lead](#database-lead)
    - [Threat Hunter](#threat-hunter)
  - [Competition Approach](#competition-approach)
- [Tool Development](#tool-development)
  - [Script Limitations](#script-limitations)
  - [Dovetail](#dovetail)
  - [Development Mindgames](#development-mindgames)
- [Feedback](#feedback)
  - [WRCCDC](#wrccdc)
  - [NCCDC](#nccdc)
  - [Learning Plateau ](#learning-plateau-)
- [What's next?](#whats-next)

![team picture](../images/24CCDCNats.png)
Smiling through the pain of 2nd place featuring surprise guest, Alex Levinson. A battle hard-fought.

# [Building on success](#building-on-success)
Coming off the back of a dark horse CCDC season we were back for one last face-off. Truth be told, there was a lot left on the table from last year, and we were back to ransack it all. The biggest takeway we had from the 2023 season was that our strategy and composotion were largely correct. The difference? Execution. The solution? Speed.

## [Speed](#speed)
If you've read my [previous post](../23-ccdc-nationals-reigniting-the-legacy) about the 2023 season, you'll know that my primary emphasis at the time was developing a system to operate at scale. Given our team composition was largely the same, my goal for the 2024 season was to optimize my workflow in the name of speed. Being fortunate enough to make Nationals as a Freshman allowed me to develop an understanding of the competition's quirks at each stage. WRCCDC tends to have at least one legacy or odd-ball box where as Nationals is surprisingly clean cut. Throughout the season I made multiple revisions to my previously garbage `Run.ps1` dispatcher script in an attempt to make it as fast as our Linux equivalent, [Coordinate](https://sourque.com/projects/coordinate/). As before, the core idea was to have our dispatcher utilize WinRM to fire off small tailored scripts against all boxes to take host inventory and configure various settings. The primary optimization I had in mind was concurrency (Yes I made a "dispatcher" that was not concurrent :clown_face:). I'll share some funny moments and explain my Windows scripts in detail down below, but the most up-to-date and independently maintained version can be found [here](https://github.com/Altoid0/Dovetail).

# [Strategy](#strategy)
How to win for dummies: running it back for real this time.
## [Team Composition](#team-composition)
I've found that flexibility is key in this competition. Of course every team needs 4 core roles: Windows, Linux, Networking, and Business. However, the remaining roles can and should be tailored to the strengths of your team members and the dyanmic of the various rounds you take part in. We continued with a relativly similar composition to the previous year, with a few tweaks. 
### [Web Lead](#web-lead) 
Web remained a key part of our services at both WRCCDC and NCCDC, so we opted to have a dedicated Web Lead. The greatest benefit of doing is is having someone who understands the scope of your mock company and can aleviate stress for the OS specific people as they focus on pure host-based security. More often than not the orange team (mock customer) calls you have will account for a siginificant part of your overall score. Your ability to score points here will depend on the web lead's ability to map out the ficticious company and understand the scenario. For example, at nationals we had a company ecommernce site that allowed customers to purchase our services. However, on the phone call, all you're asked is "What is the difference between the __ and __ service?". It becomes your reponsibility to quickly figure out what they are reffering to and provide a correct answer. This is where having a dedicated Web Lead comes in handy.
### [Database Lead](#database-lead)
Following our last year's tragedy with the database box being heavily targeted and owned, we assigned a dedicated person to serve as database admin. This way the entirety of web-related security is accounted for and the stress of managing a variety of individual services in taken off of the core OS leads. Having a database lead also allows for a more focused approach to securing the database box and its dependencies. Not to mention, since the web and database admin makeup two people. They can easily collaborate on security and inventory notes. During the initial stages of the competition, our web and database admin were relatively secluded from the rest of the team. They key to making this setup work is having each of these leads take strong notes and communicate their findings to the rest of the team. In between days one and two of the competition, these leads were able to give a detailed overview of the company's services which helps in developing the team's understanding of the scenario as a whole.
### [Threat Hunter](#threat-hunter)
Threat hunter this year was a little different for us. Instead of having someone dedicated to setting up a SIEM and managing all aspects of it, the role was more dyanmic. Our threat hunter's responsibilities included SIEM stand-up (should it be needed), SIEM related inject tasking, and general linux assistance when needed. This role changes the most round by round given the nature of each stage of the competition. At qualifiers we aren't given machines capable of running any type of SIEM (sick cyber defense comp). The threat hunter's job is to create some type of jank centralized logging solution that can atleast ingest events for inject points. At regionals, we have resources to play with, so the goal is to have a full SIEM setup and ready for any inject related tasking. However, WRCCDC decided to be a little different this year (nice?). We were given a graylog instance within our network with agents deployed, to Windows hosts at least. This was nice because it saved us setup time, however it also meant that we were sort of *forced* to use it since it didn't make sense investing more time to redeploy our own. The largest downside was our lack of familiarity with graylog, our instance ended up breaking itself and it took some planning in between days one and two in order to get it back up and running. At nationals, we had total control over our environment and opted to use Elastic Stack. This solution has a lot of cool integrations and a good search dashboard. It's greatest appeal was the ability to create dedicated dashboards and subsequently, visualizations. The idea was to use these visualizations in inject responses as an easy means to answer analytical questions like "Top 10 ports by traffic" or "Top 5 users by failed logins". The threat hunter was also responsible for assisting with injects and linux hardening should the extra set of hands be required. This role is a bit of a jack of all trades, but it's crucial to have someone who can pivot between tasks and assist where needed.

Our revised composition was as follows:
 - Player 1 - Windows Lead
 - Player 2 - Windows Admin
 - Player 3 - Linux Lead
 - Player 4 - Web Lead, Linux backup
 - Player 5 - Database Lead, Linux backup
 - Player 6 - Threat Hunter, Business, Linux backup
 - Player 7 - Business Lead
 - Player 8 - Captain, Networking, Business, Windows backup

A change here that I didn't mention was the role of captian. Last year's role was very managerial and hands off. With a solid understanding of the competition half of the previous team returing, we were confident everyone could operate independently and communicate without the need for a dedicated managerial role. If you're a team that potentially lacks high-level experience or has novice members, a hands-off captain would likely be beneficial.

## [Competition Approach](#competition-approach)
If you've read my blog post from last year then I'm afriad there isn't much that has changed aside from role responsibilities (Everyone works on injects). However, There was one big change we made for nationals. We did not walk into the competition with the intention to use our Windows dispatcher script (`Run/Dovetail.ps1`). I decided to make the call to pivot the way we do Windows based on our last year troubles with domain authentication and sucess with a solo/duo manual appraoch. This year we had an dedicated extra Windows admin, so us two, plus someone else playing a flex Windows role, meant we could easily split the work and manually harden our Windows boxes. Hearing we ditched our script is probably alarming for a lot of people who tracked our repository updates and were familiar with my previous post. However, NCCDC in its current state is a bit of a game and to play it, you need to identify where you can speed up and where you can relax. The first 15 minutes of the competition is the most important so with unknowns such as script failures, network issues, and general chaos, the one thing you can always count on is manual configuration.

TODO: More here about mentality
# [Tool Development](#tool-development)
## [Script Limitations](#script-limitations)
There are a couple of limitations with the way Windows script dispatching works, specifically in a CCDC environment. The biggest is the dependency of WinRM. WinRM honestly works way better than I had initially ever expected it to. It is enabled by default on modern Windows Servers and permitted through the firewall. However, in the name of security. Microsoft has made its firewall exclusion only applicable to the local subnet when a machine is configured to run on its "Public" firewall profile. Most of the times, CCDC starts with the firewall off/down. But, given that we saw defender enabled out of the box at NCCDC last year, I was skpeticle about firewall starting off on all boxes. If we walk in with our 15 minute plan relying heavily on the success of script deployment then any hiccup can add a sigificant hurdle to that plan. **It is worth noting that Dovetail.ps1 would have worked at nationals this year since firewall was off and most of the Windows boxes were all on the same subnet. However, the idea is to have a plan ready for a every use case, not just the most probable**
> Ok so WinRM is largely good, but it's not perfect for our use case. What about just using PsExec?
PsExec is a great methodology for mass executuon, it has a Microsoft implementation and relies on port 445 which is always open in a CCDC environment. However, how do you get script output back? Maybe I'm missing something, but the level of output and error logging I can achieve with WinRM and my `Dovetail.ps1` script is not something that I can easily achieve with PsExec. Not to mention. Microsoft's PsExec does not run asynchronously when given a list of hosts, meaning 1 script could hold up the line when executing across a large range of hosts unless you spawn N number of PowerShell windows for each boc. Based on my own testing with certain scripts and the NCCDC environment this year, if we would have used PsExec.exe it would have taken roughly 30 minues to deploy all scripts. At that point I might as well just use Dovetail and quickly troubleshoot unresponsive hosts.

## [Dovetail](#dovetail)
For rounds where Dovetail was used, the following child scripts were executed in this order: 
 - Inventory.ps1 - Collects information from each host such as users, startups, software, shares, and IIS site bindings.
 - Fix.ps1 - Resolve any QOL issues like crazy fonts, hidden folders, non-English keyboards, and GPO roadblocks.
 - Smb.ps1 - Disables SMBv1, forces security signatures, and deletes default shares.
 - Php.ps1 - Disables dangerous functions and file uploads dynamically by finding all php.ini files.
 - Log.p1  - Enables all types of PowerShell logging, sets audit policies, and installs Sysmon.
 - Users.ps1 - Creates a new admin account and changes all passwords to something random.
 - Hard.ps1 - Implements pass-the-hash mitigations, configures Windows defender and its extended capabilities, and sets up some fun tricks for red team to scratch their heads at.

## [Development Mindgames](#development-mindgames)
Bubba Gumper
Firewall of the gods
Wininit
Misc.ps1
Leaving in Dovetail for NCCDC

# [Feedback](#feedback)
## [WRCCDC](#wrccdc)
## [NCCDC](#nccdc)
## [Learning Plateau ](#learning-plateau)
# [What's next?](#whats-next)
