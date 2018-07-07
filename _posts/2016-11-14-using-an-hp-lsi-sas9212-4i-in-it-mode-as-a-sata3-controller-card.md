---
layout: post
title: "Using an HP LSI SAS 9212-4i in IT mode as a SATA3 Controller Card"
categories: ["computers", "diy", "upgrades", "ssd"]
---

{{ page.title }}
================

Continuing from my [last post](/upgrading-an-intel-X58-gaming-system-to-ssd), I was able to join modern times by adding an SSD to my circa-2011 gaming system. The performance improvements have been fantastic and I couldn't be happier with the decision to upgrade.

That said, there was a bit more room for improvement when it came to the SSD upgrade. My X58 motherboard had a subpar third party SATA3 chipset which was known to cause stability issues in exchange for a suboptimal performance improvement. Most discussion threads determined it was not worth using, and one would be better off disabling the feature altogether and sticking with the built in Intel SATA2 controller. Up to this point, I had decided to follow that advice.

The problem was, I knew I could squeeze a bit more juice out of this system, and I figured that while I had the system open and was working on replacing the drive, I should look into fixing the disk performance issue.

Most threads I found when researching improving performance in systems similar to mine [pointed to a similar theme](https://hardforum.com/threads/hba-recommendation-for-x58-system.1825738/) - Find a used enterprise SATA/ RAID card and drop it in.

Most recommended a particular card/ chipset - The LSI 9211-8i, which was an 8-port SAS RAID card. A bit overkill for my personal needs, but one that could generally be found on ebay for ~$50. The only other major downside was that I would need to buy a new SAS cable, or try and find a card that supplied a SAS to SATA cable, both of which would increase the investment cost. Another issue was ensuring you could find a reputable seller in the US, in case the card ended up DOA and you needed to do return services.

After initially putting in eBay offers for non-US 9211-8i cards (and those offers being declined), I came across a [forum post](https://forums.freenas.org/index.php?threads/hp-sas92124i.30412/) talking about a variation of the card in question - the HP LSI 9212-4i.

The 9212-4i is a slightly updated version of the 9211, but only offers 4 internal ports, and those ports are actually SATA ports instead of the more enterprise-y SAS. This meant I could use any SATA cables I had laying around without resorting to buying additional adapters. I was also able to get the 9212-4i for less than I was originally going to pay for any of the 9211 cards I was searching for, namely the IBM M1015 and the Dell Perc H200/ H310.

The only other thing recommended, but not required for these cards was adding additional cooling for the chipset. The cards are generally meant to be put in a rackmount/ server system with more dedicated cooling, and the chipset runs hot, so adding a small fan to the card gives additional peace of mind.

LSI 9212-4i, before adding fan
![lsi-9212-4i-1](/media/images/lsi-9212-4i-1-700.jpg "LSI 9212-4i card")

LSI 9212-4i, after adding fan:

![lsi-9212-4i-2-](/media/images/lsi-9212-4i-2-700.jpg "LSI 9212-4i card with attached cooling fan")

Once the fan was added and the card installed (NOTE: be sure to record the adapter address, which is on a label on the back of the card and starts with <strong>500605b0XXXXXXXX</strong>), it was time to flash the ROM into Target mode to allow booting from any devices attached.

To accomplish this, I used a combination of instructions from [nguvu](https://nguvu.org/freenas/Convert-LSI-HBA-card-to-IT-mode/) and the [FreeNAS forums](https://forums.freenas.org/index.php?threads/hp-sas92124i.30412/):

1) Use [Rufus](https://rufus.akeo.ie/) to create a bootable DOS USB stick.

2) Copy basic firmware installation bits to the USB (I used the base version of the tools found on the FreeNAS forums [here](https://forums.freenas.org/index.php?threads/hp-sas92124i.30412/) ([direct link](http://www.mediafire.com/download/9i8c85u4iuanf9h/HP_92124i_IT_FW_P16.zip)).

3) Download the most up-to-date version of the card firmware (v20 as of this writing) ~~[found here](http://www.avagotech.com/products/storage/host-bus-adapters/sas-9212-4i4e#downloads). I downloaded the package "9212-4i4e_Package_P20_IR_IT_FW_BIOS_for_MSDOS_Windows".~~

**UPDATE** - It appears LSI was purchased by Broadcom, and the old links were removed. I was able to find the P20 release on their new site. you can download it [here](https://docs.broadcom.com/docs/9212_4i4e_Package_P20_IR_IT_FW_BIOS_for_MSDOS_Windows.zip)

4) Copy the proper firmware onto the USB. I used the "214i4et.bin" firmware in the "HBA_9212_4i4e_IT" folder as well as the "mptsas2.rom" file in the "sasbios_rel" directory. Both were copied to the root of the USB stick.

5) Boot off the USB with the card installed and install the firmware:

Clear the card's old firmware

<blockquote><strong>cd sas2008</strong></blockquote>
<blockquote><strong>megarec -writesbr 0 sbrempty.bin</strong></blockquote>
<blockquote><strong>megarec -cleanflash 0</strong></blockquote>

Reboot (via ctrl alt delete, etc), then boot back to USB stick

Flash the card to the new firmware and bootrom make it bootable:

<blockquote><strong>sas2flsh -o -f 214i4et.bin -b mptsas2.rom</strong></blockquote>

Set SAS Adapter address with address recorded from label on back of card prior to installation

<blockquote><strong>sas2flsh -o -sasadd 500605b0XXXXXXXX</strong></blockquote>

Once this is complete, you should be able to see the card appear in the boot order and its bios on startup.

At this point I connected the SSD to the card and moved the card to the first position in the motherboard boot order. Startup generally takes a few seconds longer than before as the card boots, but overall is negligible, especially coming from a non-SSD prior.

As for the results, I can say I am very happy. I went from pretty good drive performance:

![SSD Post-ACHI-latest-intel-driver](/media/images/ssd-post-achi-latest-intel-driver.png "X58 motherboard with Intel SATA2 and SSD performance")

To effectively maxing out the SSD for Read/ Writes:

![SSD Post-ACHI-latest-intel-driver](/media/images/ssd-post-lsi-card.png "X58 motherboard with LSI 9212-4i and SSD performance")

X58 motherboard with LSI 9212-4i and SSD performance
All in all, another cost-effective, and worthwhile upgrade, in my book.
