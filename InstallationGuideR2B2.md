---
layout: doc
title: InstallationGuideR2B2
permalink: /doc/InstallationGuideR2B2/
redirect_from: /wiki/InstallationGuideR2B2/
---

Installation Guide for Qubes Release 2 Beta 2
=============================================

1.  [Hardware Requirements](#HardwareRequirements)
2.  [Download installer ISO](#DownloadinstallerISO)
3.  [Burning the ISO onto a DVD or USB stick](#BurningtheISOontoaDVDorUSBstick)
4.  [Upgrading from Qubes R1 or R2 Beta 1](#UpgradingfromQubesR1orR2Beta1)
5.  [Installing Updates](#InstallingUpdates)
6.  [Known Issues](#KnownIssues)
7.  [Getting Help](#GettingHelp)

Hardware Requirements
---------------------

Please see the [Hardware Compatibility List](/wiki/HCL) page for more information on required and recommended hardware.

Note: We don't recommend installing Qubes in a virtual machine! It will likely not work. Don't send emails asking about it. However, you can install it on an external USB hard drive and run from it, at least for testing (normally such disks are *orders* of magnitude slower than even the slowest internal hard drives).

Download installer ISO
----------------------

See [this page](/wiki/QubesDownloads) for ISO downloads. Remember, we have absolutely no control over those servers, and so you should be assuming that they might be compromised, or just be serving a compromised ISOs because their operators decided so, for whatever reason. Always verify the digital signature on the downloaded ISO. See this [page](/wiki/VerifyingSignatures) for more info about how to download and verify our GPG keys, and then verify the downloaded ISO:

{% highlight trac-wiki %}
gpg -v <iso>.asc
{% endhighlight %}

Burning the ISO onto a DVD or USB stick
---------------------------------------

Once you verify this is an authentic ISO, you should burn it on a DVD.

If you prefer to use USB as a source for installation, then you just need to copy the ISO onto the USB device, e.g. using dd:

{% highlight trac-wiki %}
dd if=Qubes-R2-Beta2-x86_64-DVD.iso of=/dev/sdX
{% endhighlight %}

**Be sure to use a correct device as the target in the dd command above (instead of sdX)**

Before proceeding with the installation, you are encouraged to first read all the information on this page, especially the *Known Issues* paragraph.

Then, when finally ready, boot your system from the installer DVD and follow the instructions on screen. The installer is very simple and asks very few questions -- it's actually easier to install Qubes right now than most other Linux distributions!

The installer loads Xen right at the beginning, so chances are high that if you can see the installer's graphical screen, Qubes will work on your system :)

Upgrading from Qubes R1 or R2 Beta 1
------------------------------------

Because of the distribution change in R2B2 (from fc13 to fc18) it's preferred that users reinstall Qubes R2B2 from scratch, and use [qubes backup and restore tools](/wiki/BackupRestore) for migrating of all of the user VMs.

Advanced users (and advanced users only) can also try a manual upgrade procedure that has been described [here](/wiki/UpgradeToR2B2). It's advisable to backup your VMs before proceeding anyway!

Installing Updates
------------------

Installing updates is very easy and can be done using the "Update" button in the Qubes Manager. Alternatively it can also be done from command prompt -- see the following for more details:

-   For installing updates for Dom0 -- see instructions [here](/wiki/SoftwareUpdateDom0).
-   For installing updates for you domains (VMs) -- see instructions [here](/wiki/SoftwareUpdateVM).

Known Issues
------------

-   On some graphics cards the Xfce4 Window Manager (one of the two supported Dom0 Windows Managers in Qubes R2 B2, the other being KDE) might behave "strangely", e.g. decorations might not be drawn sometimes. Also the accompanying lightdm login manager might incorrectly display the wallpaper. If you're facing those problems, it's advisable to use the KDE Window Manager and kdm instead of Xfce4 and lightdm (this is default if one chooses the KDE only installation option in the installer).

-   Some icons in the Qubes Manager application might not be drawn correctly when using the Xfce4 environment in Dom0. If this bothers you, please use the KDE environment instead.

-   When restoring service VMs from a backup (such as custom netvms, firewallvms, etc) their icons might not be preserved in the "Start Menu".

Getting Help
------------

-   **User manuals are [here](/wiki/UserDoc).** (Strongly recommended!)

-   Developers documentation (normally not needed by users) is [here](/wiki/SystemDoc)

-   If you don't find answer in the sources given above, write to the *qubes-devel* mailing list (you don't need to be subscribed to the list, just send email to the address given below):
    -   [http://groups.google.com/group/qubes-devel](http://groups.google.com/group/qubes-devel)
    -   `qubes-devel@googlegroups.com`

-   Please do not write email to individual developers (Marek, Joanna, etc) asking questions about installation or other problems. Please send all such questions to the mailing list.

