---
layout: post
title: HP Laptop Wireless Connectivity Issue.
published: true
---

#### 20191022

## Troubleshooting HP Zbook Laptop Wifi Connection

So I had a rather perplexing issue with my laptop, an [HP Zbook 17 G2](www.google.com/search?q=HP%20Zbook%2017%20G2).  Occasionally I would enable the WiFi hotspot to update a phone, and while this worked for a while, it would go through this pattern where I would enable the WiFi, and about 3 seconds later it would turn off.

### A driver problem?

Initially I assumed this was a driver problem and I spent a good amount of time researching the various Drivers for the Intel Ac 7260 wireless card.  Unfortunately, a lot of people have been having trouble with this driver and this led me down a path where I was chasing a lot of rabbit holes.  I installed a bunch of different drivers, all to no avail.  

### Just google it

Searching the web was no help because there are apparently a great many issues that can affect your wireless connection.  And virtually all of these articles recommend the same, unhelpful steps.  Run the troubleshooter.  Re install the drivers.  Do a Winsock reset.  Install some obscure Windows 10 patches.  But none of that helped. 

There weren't even any helpful messages in the Windows Event Viewer, which presumably would show an error message if some driver or service was failing.

I was practically tearing my hair out over this, and I don't know why but I tried disconnecting the laptop from its dock and its Ethernet connection.  All of a sudden the wireless worked flawlessly.

### Not a driver issue

A clue!  

There are evidently a few security solutions that do this, they disable the wireless when connected to a companies network.  But these security solutions need to be installed onto the laptop, and I was running a clean install of windows that I had done myself.

Then I read about how there is a switch that you can enable in BIOS that will force this kind of behavior.  Some Sys Admins were using powershell and scripting to push this out on HP Laptops.  

I did find the BIOS switch, but it did nothing.  But I made an interesting discovery.  If you disable the WLAN Autoconfig service, you lose wireless connectivity.  But once you enable it, the WiFi would work for a short while.  But after the next reboot the problem would surface again.

So I thought that is the WLAN Autoconfig service that is turning the WiFi off.  But if I disable the service then WiFi does not work at all, so it seemed like I was stuck.

### The answer

![This was the culprit][HP-Wlan-Service]


HP LAN/WLAN/WWAN Switching UWP Service

Bingo!  As part of a fresh install of Windows on an HP Laptop, you can be tempted to install some of the enterprise software found on the HP website where they have all of the drivers.  But this is a great example of why I never do this.  When you install the HP Hotkey application, it also installs this service, the "HP LAN/WLAN/WWAN Switching UWP Service."  This service evidently stays running even even if you uninstall the hotkey program.  What it is supposed to do is allow you to switch WIFI on and off with a keyboard shortcut.  But what it does instead, is automatically disable wifi when you are connected to an ethernet connection.  Not a problem for most use cases.  BUT - if you need to use the mobile hotspot feature on your laptop, this won't work, and this is what led to try and solve this problem.

A lot of the work I do is solving problems and this was a minor one, but I think it is worthwhile going through the thought process involved in solving a problem.  

1. Identify the issue
2. Attempt to resolve
3. Reconsider your assumptions
4. Make alist of everything you try
5. Keep working it until you figure it out.

[HP-Wlan-Service]: https://peteshaw.github.io/images/HP-LAN-WLAN-Service.png "HP WLAN Service"
