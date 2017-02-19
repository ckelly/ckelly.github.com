---
layout: post
title: "Upgrading an Intel X58 Gaming System to SSD"
categories: ["computers", "diy", "upgrades", "ssd"]
---

{{ page.title }}
================

For a long time I've been keeping an eye on SSDs as an upgrade path for my gaming PC. Upgrading to an SSD is likely the final upgrade possible for my aging X58 system. Until now, I have been carrying on with an older Western Digital Black HDD from the day I built the machine in 2011. It had served me well, but after upgrading my girlfriend's laptop to a new Samsung 850 EVO SSD and seeing the before and after performance, I had a massive itch to upgrade the hard drive in the ol' gaming rig.


I had picked her EVO up as part of a deal on Jet.com, getting it for about $40 below normal prices. Once I got it home and installed it in her laptop, I immediately regretted not buying a second one for the same price. I waited for several months for the price to drop but when no other deals popped up, I started looking for deals for other ~500GB SSDs at or below the $100 range.

I was fortunate enough to snag a slightly outdated (but still new!) Micron m500 SSD on clearance from Newegg and promptly ordered it.

Initial installation
Once it arrived, I was able to connect and install the hard drive into a 5.25" bay in my case, and ensure that the drive was working properly. I then followed a guide on doing an SSD system migration using Macrium Reflect Free. I'll say the migration went well, though it did take about three hours to clone everything (~300GB) over from a 500GB hard drive to a 480GB SSD; much longer than the guide suggested.

The only issue I had with the migration was that the SSD was slightly smaller than the original drive, so I had to shrink the main system partition on clone to fit the last recovery partition - I originally shrunk the main partition to its minimum per the article suggestion, with the goal of resizing after cloning, but I found that not possible to do with Reflect or any other free partition software (none would allow me to resize the middle of three partitions to take up the rest of the space after the fact). I ended up having to start over, placing the first partition, then place the second/ main one with its new size (the whole rest of the drive minus the size of the last partition), then drag and drop the third partition into place. After doing that, the clone went off without a hitch.

I was able to restart and boot from the new drive. It was definitely much snappier, and then I took a look at the benchmarks using CrystalDiskMark:

![SSD Pre-ACHI](/media/images/ssd-pre-achi.png "SSD Pre-ACHI")

Compared to the original hard drive (non-SSD), a fantastic (almost 3x) improvement:

![Original Hard Drive](/media/images/hdd-benchmark.png "Original Hard Drive")

But, it doesn't seem *that* fast. What gives?

Check Yo' Self
Once I got the first benchmarks in, They were good, but not quite what I was expecting. I eventually found out there were a couple reasons for this.

SATA
My (and most) X58 motherboards don't come with any sort of integrated high speed SATA3 controller to go along with the slower SATA2 controller - Intel didn't make one for most boards at the time, so manufacturers had the choice of either not including one, or including a 3rd party one. the board I have came with a Marvell controller that was notorious for being slow (slower in some cases than the "inferior" Intel ports) and buggy. Not something you want to mess with when dealing with all your data. Given the choice between the slower, more reliable Intel ports, and the faster, yet buggier Marvell ports, I'll stick with the Intel ports, even at the expense of some speed, at least for now (though I've got some ideas about that going forward...)
ACHI
The second potential issue I stumbled upon when researching "Slow SSD X58" was that when moving from hard drives to SSDs, many systems such as mine are set by default to the older IDE mode rather than the newer ACHI mode. For hard drives the difference was negligible, but moving to ACHI is a must for getting the most performance out of an SSD.
Of course once I read this, I went right ahead and set the mode in my motherboard from IDE to ACHI and rebooted.

Failure to boot. oops.

Turns out, when moving from IDE to ACHI for the first time on a Windows machine, you can't just flip the switch and continue on. Luckily in Windows 10, you just need to force a safe mode boot to detect/ load the proper driver, then reboot normally.

Once I properly got ACHI mode enabled, I ran the test again:

![SSD Post-ACHI](/media/images/ssd-post-achi.png "SSD Post-ACHI")

Now we're getting somewhere - 4k read/writes are on the slow side, but overall I think I'm nearing the limits of the Intel SATA2 chipset, so I'll be content with this configuration (for now...)

Driver Issues
Ok, so I was pretty happy at this point, so I decided to take the new system for a spin in one of my favorite games, Diablo 3. The game loaded quickly, and I started playing, but ran into another issue - every so often I would get a 5+ second pause in the middle of gameplay. This pausing extended to the desktop as well. This was of course very disconcerting, having just added a new SSD to the mix and all.
Once again, researching the issue led me to discover others had issues with similar configurations. Potential fixes ranged from installing different drivers to hacking the Windows registry. I decided to go with the easier end of the spectrum and look to installing a different set of drivers.

As a general rule, aside from video cards, I usually let Windows handle driver management. Windows 10 has been very good at recognizing everything in my system, and it's been remarkably stable. Seeing others recommend installing a different driver, I decided to see if Intel had a better/ newer option.

Attempting to install Intel's latest driver for its Rapid Storage Technology product (a brand name for its controller chips), I encountered several errors about my system being unsupported. A few searches later, and it appears that the latest versions drop support for the older (in this case, 5-series) chipsets, so you have to go back. How far though? Looking for what is considered the best driver, I came across this guide, including custom download packages (spoiler, it's version "v11.7.4.1001 WHQL" for the X58 chipset), available through a custom install package on the site, or a slightly older official revision through Intel's site. I originally installed a version direct from my motherboard manufacturer, but that version was several major revisions old, so I ended up using the recommended driver from the guide. In both the manufacturer and guide-based driver cases, the hangs went away completely with little change to benchmarks over the default windows one (faster in smaller reads, slightly slower in 4k).

Conclusion
The X58 + Radeon HD7970 system I built back in 2011 has done a very good job of not showing its age to this point. From a processor performance standpoint, it's held up remarkably well compared to even current generation systems. The Radeon still runs most games at a respectable clip. These days I run most games at 1440p, and have only recently started having to crank down the settings in newer games like Battlefield 1, and for that I am hoping to get a newer-than-my-current-card hand me down in the form of a GeForce Titan that should help remedy the GPU perf issues.

One area where the system does lag is on the peripheral side. There is no WiFi or bluetooth, and things like USB3 and SATA3 are both handled by third party chips, as opposed to being integrated like they are in newer systems. In the case of my system, the Marvell SATA controller is widely considered unreliable and is deactivated.

Migrating to an SSD involved a bit more legwork than I was original anticipating, but the end result has been worth it: near instant load times for Windows and all the games I like to play.

Overall, the hardware deficiencies of this older platform haven't affected much in terms of day-to-day general usage, and I'm happy to say I have gotten my money's worth with this build over the last 5ish years. With the addition of an SSD to the mix, I should be able to get at least another couple of years out of the this system.
