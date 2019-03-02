---
layout: post
title:  "NetBehave - the story - mvp versions"
date:   2019-03-02 16:00:00 -0500
categories: netbehave
---
My presentation at the BSides Ottawa 2018 conference on November 8th, 2018 covered some of this, but I figured since the talk was not recorded that I'd present a more detailed recollection of how all of this project evolved. I thought this would have been a quick post, but I now realize that I want to put a bit more depth in this. This post will cover the initial versions of tool in 2016 and 2017. The next post will cover NetBehave.

Without further ado, let us begin.

v1.0 - The MVP

MVP, is a commonly used term in agile and startup worlds that stands for Minimal Viable Product. One common analogy, is looking at a car. The MVP for a car is not just the steering wheel, but just enough to get moving. as shown in the image below (taken from this [post][mvp-post]).

![image of MVP of a car](http://www.caroli.org/wp-content/uploads/2014/10/how-to-buil-an-mvp.png "car mvp")

This project started almost 3 years ago (in spring 2016) when I got hired as a consultant to help an organization achieve PCI DSS compliance in a short amount of time. In order to have any inklings of a chance of remediating this quickly required massive scope reduction from an acquired organization's flat network. In the end, we achieved a reduction to 20% of the original scope (CDE and connected, see my [model][pciresources-scope-model-and-approach] for detail) which gave us the chance we needed to pull that off. There were more efforts than these tools (it was a team effort), but the scoping effort was instrumental (many remediations were also required, most of which I also led).

When doing my initial research, I was looking to get access to raw data in a format that I could use in whatever way made more sense. I also had little budget. I did look at many tools, but I finally had to choose between two similar systems: Fluentd and Logstash. Fluentd won the day by being lighter weight (half the memory according to comments at the time).

So, I built the tools iteratively. Initially, I fixed the Fluentd Netflow plugin (there were a few bugs) and added IPFix support (also referred to as Netflow v10, still missing to this day)... all without knowing Ruby (the language Fluentd is written in). I used other standard Fluentd plugins (filter_grep, out_file) and made sure the output was a CSV file. Then I just used standard command line tools (grep, cut) and Excel to do an initial analysis.

As we created new network zone behind a firewall, I had to find a way to validate the firewall rules I had defined, and thus started creating my first plugin to match traffic to predefined rules (again in a CSV format). Plugins can greatly simplify the work that can be done through existing Fluentd plugins and configuration. I later added MySql support (through existing plugins) so I could facilitate access, but I'll get back to this later. We used the tools and reduced a flat network of about 800 (mostly virtualized server) to a segmented one and dropped the scope (CDE and connected systems for PCI DSS, see the aforementioned model for more information) to under 20% of this (~160 servers). We also used the tool to create and validate the required firewall rules, and removed the “ip any any” rule live during daily operations, with no notice from the business. I only missed one less regular application, a Veeam backup process that a sysadmin inquired about later that same week. This reduction in scope allowed the team to meet PCI DSS compliance in only 19 weeks. Note that this is compliance (focus on protecting payment card information only), but still an impressive feat (we also had a very talented and competent team).


v2.0 - A slightly more user friendly version

As we moved from PCI DSS compliance to addressing security of the combined organization holistically, we went at expanding the products we used focussing on the [CIS top 20][cis-top-20]. This is what our initial list looked like late summer 2016.

|CSC #|Control|Solution|
|1|Inventory of Authorized and Unauthorized Devices|TSC, Netflow|
|2| Inventory of Authorized and Unauthorized Software|TSC|
|3| Secure Configurations for Hardware and Software on Mobile Devices, Laptops, Workstations, and Servers|TSC|
|4| Continuous Vulnerability Assessment and Remediation|TSC|
|5| Controlled Use of Administrative Privileges|???|
|6| Maintenance, Monitoring, and Analysis of Audit Logs|TSC + FEK411|
|7| Email and Web Browser Protections|Proxy, MobileIron, Phishme|
|8| Malware Defenses|A/V|
|9| Limitation and Control of Network Ports, Protocols, and Services|Netflow->SharePoint|
|10| Data Recovery Capability|Processes|
|11| Secure Configurations for Network Devices such as Firewalls, Routers, and Switches|TSC|
|12| Boundary Defense|FireEye, Palo Alto|
|13| Data Protection|DLP, FireEye|
|14| Controlled Access Based on the Need to Know|HR+processes|
|15| Wireless Access Control|Airtight,TSC+Netflow|
|16| Account Monitoring and Control|TSC + FEK411 + PAM|
|17| Security Skills Assessment and Appropriate Training to Fill Gaps|LMS|
|18| Application Software Security|F5(ASM), Burp, SDLC|
|19| Incident Response and Management|Forensics with FireEye|
|20| Penetration Tests and Red Team Exercises|External Firm|

As you can tell TSC (Tenable Security Center) was out main tool, with my Netflow tool (what became NetBehave) a close seconds. The FEK411 would be the evolution of another tool I built to meet PCI DSS compliance. It consisted of NXlog community edition agent on Windows devices to send windows security events to a rsyslog server (which also received regular syslogs), which was read by another Fluentd instance and parsed. It had a simple Bootstrap-based web interface to review logs (that code is similar to what I did for the web portion of NetBehave). It would have added Elastic Search and Kibana (of the standard Elk stack), and [411][411-site], a tool developed by Etsy which I saw at [DefCon that summer][411-defcon-talk]. There were a few tools and firms involved, but I never got to see this through as I left for other opportunities later that year.

But the tool continued to improve nonetheless during that fall. As mentioned previously, I wanted something that did not require transferring files back to my PC. A great number of [Fluentd plugins][fluentd-plugins] have been produced by the community that one could leverage. I opted for MySQL as a backend. This made it easier, and allowed me to make the required reporting.

A cleaned up version of those tools is basically what I presented at the [BSides Calgary 2017 conference][bsides-calgary-2017-netflow-open-source-tools] and you can find the [slides and code][bsides-calgary-2017-slide-code] on the blog of my book's website.

In the next post, I'll describe the design and implementation of NetBehave.

[mvp-post]: http://www.caroli.org/how-to-build-a-mvp/
[cis-top-20]: https://en.wikipedia.org/wiki/The_CIS_Critical_Security_Controls_for_Effective_Cyber_Defense 
[pciresources-scope-model-and-approach]: https://http://www.pciresources.com/pci-dss-scoping-model-and-approach/
[411-site]: https://github.com/etsy/411
[411-defcon-talk]: https://www.youtube.com/watch?v=LQyqhrDl7f8
[fluentd-plugins]: https://www.fluentd.org/plugins/all
[bsides-calgary-2017-netflow-open-source-tools]: https://www.youtube.com/watch?v=T-5ltIT3-Jg
[bsides-calgary-2017-slide-code]: https://www.pciresources.com/blog/bsides-calgary-2017-netflow