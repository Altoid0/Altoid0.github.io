---
layout: post
author: Altoid0
tags: [ccdc, competitions, blue team, DFIR]
---

#### Table of contents
- [Building on success](#building-on-success)
- [Strategy](#strategy)
  - [Mentality](#mentality)
  - [Team Composition](#team-composition)
    - [Web Lead](#web-lead)
    - [Database Lead](#database-lead)
    - [Threat Hunter](#threat-hunter)
- [Tool Development](#tool-development)
  - [Speed](#speed)
  - [Script Limitations](#script-limitations)
  - [Dovetail](#dovetail)
  - [Development Mindgames](#development-mindgames)
    - [Wininit](#wininit)
    - [Bubba Gumper](#bubba-gumper)
    - [Misc.ps1](#miscps1)
    - [Firewall of the gods](#firewall-of-the-gods)
- [The Competition](#the-competition)
  - [WRCCDC](#wrccdc)
    - [WRCCDC Competition Approach](#wrccdc-competition-approach)
  - [NCCDC](#nccdc)
    - [NCCDC Competition Approach](#nccdc-competition-approach)
- [Feedback](#feedback)
  - [WRCCDC](#wrccdc-1)
  - [NCCDC](#nccdc-1)
- [What's next?](#whats-next)
  - [Learning Plateau](#learning-plateau)

![team picture](../images/24CCDCNats.png)
"We're smiling but 2nd place sucks" Feat. Alex Levinson

> The following post is a mix of my thoughts, obersations, and advice to competitors, volunteers, and organizers after competing in CCDC at the regional and national level for 2 years.

# [Building on success](#building-on-success)
Coming off the back of a dark horse CCDC season we were back for one last face-off. Truth be told, we were happy to have placed 2nd in our previous and first outing at nationals. However, with an abundence of mistakes, there was a lot left on the table. Our biggest takeway from the 2023 season was that our technical strategy and team composition were largely correct. Given that these are the two biggest topics to address with aa new team, all signs pointed to a succssful 2024 season.

# [Strategy](#strategy)
![how to win](../images/HowToWin.png)
## [Mentality](#mentality)
One of the biggest prerequisites I see in competitions like this is a hyper-competietive mindset. Successful teams and competitors take years to build and unhealthy addictions. In my opinion, teams only reach the neccessary level of dedication when there is a baseline level of convition paired with a ~~hatred~~rivalry against some adversary. Find adversary, and you'll find or develop the right people.
## [Team Composition](#team-composition)
Flexibility is mandatory in this competition. Every team needs 4 core roles: Windows, Linux, Networking, and Business/Inject Handler. Each of the 8 team members needs to pick a specialization, but also take up a secondary. It's impossible to perfectly predict the technical and business/inject challenges. Thus, people need to be ready to adapt at a moments notice. As far as our team goes, we continued with a relativly similar composition to the previous year. We retained a heavy emphasis on Linux, added some help on Windows, created dedicated roles for Web and Database, and ensured that everyone was capable of handling injects on their own.
### [Web Lead](#web-lead) 
In anticipation of the technical challenges we'd face we opted to have a dedicated Web Lead. If you think about it, most modern apps are websites and/or use HTTP protocols so this was a fairly reasonable projection to make. Web remained a significant portion of our services at both WRCCDC and NCCDC, meaning this role payed dividends. One of the greatest yet understated benefits is having someone who understands the scope of our mock company and can aleviate stress for the OS-specific people as they focus on host-based security. More often than not the orange team (mock customers) IT calls account for a siginificant part of our overall score. Our ability to score points here depended on our web lead's ability to map out the ficticious company and understand the scenario. For example, at nationals we had a company ecommernce site that allowed customers to purchase our services. However, on the phone call, all you're asked is "What is the difference between the __ and __ service?". It becomes our reponsibility to quickly figure out what they are reffering to and provide a correct answer. This is where having a dedicated Web Lead comes in handy.
### [Database Lead](#database-lead)
Following our last year's tragedy with the database box being heavily targeted and owned, we assigned a dedicated person to serve as database admin. This way the entirety of web-related security is accounted for and the stress of managing a variety of individual services in taken off of the core OS leads. Having a database lead also allows for a more focused approach to securing the database box and its dependencies. This team member is in charge of mapping out web dependencies, creating service isolated databases, and rotating passwords. Since the web and database admin makeup two people, they can easily collaborate on security and inventory notes. During the initial stages of the competition, our web and database admin were relatively secluded from the rest of the team, however, that was intentional and beneficial. They key to making this setup work is having each of these leads take strong notes and communicate their findings to the rest of the team when appropriate. In between days one and two of the competition, these leads were able to give a detailed overview of the company's services which helped the rest of the team understand dependencies they may have overlooked and advanced our understanding in the competition lore.
### [Threat Hunter](#threat-hunter)
Threat hunter this year was a little different for us. Instead of having someone dedicated to setting up a SIEM and managing all aspects of it, the role was more dyanmic. Our threat hunter's responsibilities included SIEM stand-up (should it be needed), SIEM related inject tasking, and general linux assistance. This role changes the most round by round given the nature of each stage of the competition. At qualifiers we aren't given machines capable of running any type of SIEM (nice cyber defense comp). The threat hunter's job is to create some type of jank centralized logging solution that can atleast ingest events for inject points. At regionals, we have resources to play with, so the goal is to have a full SIEM setup and ready for any inject related tasking. However, WRCCDC decided to be a little different this year (one could even be brave enough to say kind?). We were given a graylog instance with agents deployed, to Windows hosts at least. This was nice because it saved us setup time, however it also meant that we were sort of *forced* to use it since it didn't make sense investing more time to redeploy our own. The largest downside was our lack of familiarity with graylog. Our instance ended up breaking itself and it took some planning in between days one and two in order to get it back up and running. At nationals, we had total control over our environment and opted to use Elastic Stack. This solution has a lot of cool integrations and a good search dashboard. It's greatest appeal was the ability to create dedicated dashboards and subsequently, visualizations. The idea was to use these visualizations in inject responses as an easy means to answer analytical questions like "Top 10 ports by traffic" or "Top 5 users by failed logins". This role is a bit of a jack of all trades being that they must understand how to operate different SIEMs, Windows security, and linux security. It's crucial to have someone telented and dedicated who can pivot between tasks and assist where needed.

Our revised composition was as follows:
 - Tanay (Me) - Windows Lead
 - [Dylan M](https://www.linkedin.com/in/dwmichalak/) - Windows Admin
 - [Dylan T](https://x.com/d_tranman) - Linux Lead
 - [Derrick](https://www.linkedin.com/in/derrickttran/) - Web Lead, Linux Admin
 - [Bill](https://www.linkedin.com/in/bill-luong/) - Database Lead, Linux Admin
 - [Marshall](https://www.linkedin.com/in/marshall-j-ung/) - Threat Hunter, Business, Linux backup
 - [Jess](https://www.linkedin.com/in/jessicacleung/) - Business Lead
 - [Evan](evandeters.com) - Captain, Networking, Business, Windows backup

A few changes that I haven't mentioned yet, everyone was mandated to do injects whether or not their role explicitly mentioned it and Captain was no longer hands-off. Our last year interpretation of the captain role was that it should be very managerial, prioritizing communication over technical impect. With a solid understanding of the competition and half of the previous team returing, we were confident everyone could operate independently and communicate without the needing to sacrifice one set of hands. If you're a team that potentially lacks high-level experience or has novice members, a hands-off captain would likely be beneficial.

# [Tool Development](#tool-development)
## [Speed](#speed)
If you've read my [previous post](../23-ccdc-nationals-reigniting-the-legacy) about the 2023 season, you'll know that my primary emphasis at the time was developing a system to operate at scale. Given our team composition was largely the same, my goal for the 2024 season was to optimize my workflow in the name of speed. Being fortunate enough to make Nationals as a Freshman allowed me to develop an understanding of the competition's quirks at each stage. WRCCDC tends to have at least one legacy or odd-ball box where as Nationals is surprisingly clean cut. Throughout the season I made multiple revisions to my previously garbage `Run.ps1` dispatcher script in an attempt to make it as fast as our Linux equivalent, [Coordinate](https://sourque.com/projects/coordinate/). As before, the core idea was to have our dispatcher utilize WinRM to fire off small tailored scripts against all boxes to take host inventory and configure various settings. The primary optimization I had in mind was concurrency (Yes I made a "dispatcher" that was not concurrent :clown_face:). I'll share some funny moments and explain my Windows scripts in detail down below, but the most up-to-date and independently maintained version can be found [here](https://github.com/Altoid0/Dovetail).
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
### [Wininit](#wininit)
### [Bubba Gumper](#bubba-gumper)
### [Misc.ps1](#misc.ps1)
### [Firewall of the gods](#firewall-of-the-gods)

# [The Competition](#the-competition)
## [WRCCDC](#wrccdc)
### [WRCCDC Competition Approach](#wrccdc-competition-approach)
## [NCCDC](#nccdc)
### [NCCDC Competition Approach](#nccdc-competition-approach)
If you've read my blog post from last year then I'm afriad there isn't much that has changed aside from the previously mentioned role responsibilities. However, There was one big change we made for nationals. We did not walk into the competition with the intention to use our Windows dispatcher script (`Run/Dovetail.ps1`). I decided to make the call to pivot the way we do Windows based on our last year troubles with domain authentication and sucess with a solo/duo manual appraoch. This year we had an dedicated extra Windows admin, so us two, plus someone else playing a flex Windows role, meant we could easily split the work and manually harden our Windows boxes. Hearing we ditched our script is probably alarming for a lot of people who tracked our repository updates and were familiar with my previous post. However, NCCDC in its current state is a bit of a game and to play it, you need to identify where you can speed up and where you can relax. The first 15 minutes of the competition is the most important so with unknowns such as script failures, network issues, and general chaos, the one thing you can always count on is manual configuration.

# [Feedback](#feedback)
## [WRCCDC](#wrccdc)
## [NCCDC](#nccdc)
# [What's next?](#whats-next)
## [Learning Plateau](#learning-plateau)
TODO: How to learn in CCDC and why there exists a learning plateau
