---
layout: post
author: Tanay Shah
tags: [windows, persistence, log poisoning, wmi, powershell]
---

Lorem ipsum[^1] dolor sit amet, consectetur adipiscing elit. Pellentesque vel lacinia neque. Praesent nulla quam, ullamcorper in sollicitudin ac, molestie sed justo. Cras aliquam, sapien id consectetur accumsan, augue magna faucibus ex, ut ultricies turpis tortor vel ante. In at rutrum tellus.

## Origin Story
Over here at CPP's cyber club SWIFT, we have a tradition of hosting CCDC-esque competitions, dubbed Red vs Blue (RvB) to train students on the blue teaming side and to expose them to what working on a mock company environment is like. The primary emphasis of this is to teach people how to deal with red team. As such we prestage a lot of misconfigurations and vulnerabilities on the blue team's network to allow red team to have a fun and easy time. On the linux side of things something we historically do is replace the pam_deny module with pam_permit so that event in the event blue teams reconfigure policies, red team can maintain access. However, this is not as easily achomplished on Windows. This is where Powerkocka comes in.

## Implementation
Powerkocka is a simple powershell script that implants WMI events or scheduled tasks to trigger on the 4625 event ID. It then parses the domain name field of the event and compares it to a preconfigured domain "key" set by the red teamer. From here it resets the password of the target user or creates it if it does not exist and sets the password to whatever was configured when implanting PowerKocka.

### Domain Key?
The domain key was implemented so that only a knowledable red teamer could trigger PowerKocka to act on an event. Essentially the red teamer sends auth requests to the machine with a slightly malformed domain name tacked on to the username. 
For example, if I wanted to reset the password to domain user `kelp\otter` I would implant powerkocka with a domain key of `keIp` and then send a bogus auth request to the machine with the username `keIp\otter`. PowerKocka would parse the failed logon attempt and notice the `keIp` doamin name, which would lead it to reset the `otter` user password. However, if a blue teamer were to fat finger their password and and generate a 4625 event ID, the PowerKocka implant would disregard it as it is only designed to act on requests with the `keIp` domain.
The idea behind the domain key was one, to prevent accidental password changes on behalf of the blue team, and two, to better blend in with normal logon attempts. Now generally speaking this method of persistence is not very stealthy by nature. However given the nature of the competition, and the fact that blue teams often don't have time to stand up fully functional SIEMs, stressed blue teamers may miss the misspelling in the domain name if the key is set to something very similar to the domain. It's the same logic as phishing email domains.

### WMI Events
I opted to use WMI events in PowerKocka because they check a lot of my personal cool persistence checkboxes. To my knowledge there aren't a whole lot of ways to trigger some execution based on an event (WMI and Scheduled tasks are the only ways I know of). WMI events are fairly easy to create (develop is a whole different story) and pretty stable in my testing. Not to mention, since WMI is a core capability of windows, it's pretty much guaranteed to be present on any modern box your attacking. Oh and it run as `SYSTEM`, what's not to love about it?

### Scheduled Tasks
I can't lie I despise these a lot. Mainly because there doesn't seem to be a singular or even finite number of ways to implement them through powershell. Every time you look it up you see something different. One day I'll sit down and figure out what the best and most consistent was is but that day TBD. It's currently an option in PowerKocka but I don't recommend it since I just threw it in there without testing.
