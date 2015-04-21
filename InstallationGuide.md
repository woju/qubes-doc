---
layout: doc
title: InstallationGuide
permalink: /doc/InstallationGuide/
redirect_from: /wiki/InstallationGuide/
---

Installation Guide (for Qubes Release 1)
========================================

1.  [Hardware Requirements](#HardwareRequirements)
2.  [Download installer ISO](#DownloadinstallerISO)
3.  [Burning the ISO onto a DVD or USB stick](#BurningtheISOontoaDVDorUSBstick)
4.  [Upgrading from Qubes 1.0-rc1](#UpgradingfromQubes1.0-rc1)
5.  [Migrating from Qubes Beta 3](#MigratingfromQubesBeta3)
6.  [Installing Updates](#InstallingUpdates)
7.  [Known Issues](#KnownIssues)
8.  [Getting Help](#GettingHelp)

Hardware Requirements
---------------------

Please see the [Hardware Compatibility List](/doc/HCL) page for more information on required and recommended hardware.

Note: We don't recommend installing Qubes in a virtual machine! It will likely not work. Don't send emails asking about it.

Download installer ISO
----------------------

See [this page](/doc/QubesDownloads) for ISO downloads. Remember, we have absolutely no control over those servers, and so you should be assuming that they might be compromised, or just be serving a compromised ISOs because their operators decided so for whatever reason. Always verify the digital signature on the downloaded ISO. See this [page](/doc/VerifyingSignatures) for more info about how to download and verify our GPG keys, and then verify the downloaded ISO:

{% highlight trac-wiki %}
gpg -v <iso>.asc
{% endhighlight %}

Burning the ISO onto a DVD or USB stick
---------------------------------------

Once you verify this is an authentic ISO, you should burn it on a DVD.

If you prefer to use USB as a source for installation, then you just need to copy the ISO onto the USB device, e.g. using dd:

{% highlight trac-wiki %}
dd if=Qubes-R1-x86_64-DVD.iso of=/dev/sdX
{% endhighlight %}

**Be sure to use a correct device as the target in the dd command above (instead of sdX)'''**

Before proceeding with the installation, you are encouraged to first read all the information on this page, especially the *Known Issues* paragraph.

Then, when finally ready, boot your system from the installer DVD and follow the instructions on screen. The installer is very simple and asks very few questions -- it's actually easier to install Qubes right now than most other Linux distributions!

The installer loads Xen right at the beginning, so chances are high that if you can see the installer's graphical screen, Qubes will work on your system :)

Upgrading from Qubes 1.0-rc1
----------------------------

If you're already running Qubes 1.0-rc1, you don't need to reinstall, it's just enough to update the packages in your Dom0 and the template VM(s). The easiest way for doing this is to click on the Update Button in the Qubes Manger -- one click when you selected Dom0, and one click for each of your template VM (by default there is just one template).

Migrating from Qubes Beta 3
---------------------------

If you have Qubes Beta 3 currently installed on your system, you must reinstall from scratch, as we offer no direct upgrade option in the installer (sorry). However, we do offer tools for smooth migration of your AppVMs. In order to do that, please backup your AppVMs using the `qvm-backup` tool [as usual](/doc/BackupRestore). Then, after you install Qubes 1.0 rc1, you can restore them using `qvm-backup-restore` tool. However, because we have changed the default template in RC1, you should tell qvm-back-restore about that by passing `--replace-template` option:

{% highlight trac-wiki %}
qvm-backup-restore <backup_dir> --replace-template=fedora-15-x64:fedora-17-x64 
{% endhighlight %}

Installing Updates
------------------

Installing updates is very easy and can be done using the "Update" button in the Qubes Manager. Alternatively it can also be done from command prompt -- see the following for more details:

-   For installing updates for Dom0 -- see instructions [here](/doc/SoftwareUpdateDom0).
-   For installing updates for you domains (VMs) -- see instructions [here](/doc/SoftwareUpdateVM).

Known Issues
------------

-   Installer might not support some USB keyboards (\#230). This seems to include all the Mac Book keyboards (most PC laptops have PS2 keyboards and are not affected).

-   If you don't enable Composition (System Setting -\> Desktop -\> Enable desktop effects), which you really should do, then the KDE task bar might get somehow ugly (e.g. half of it might be black). This is some KDE bug that we don't plan to fix.

-   Some keyboard layout set by KDE System Settings can cause [keyboard not working at all](https://groups.google.com/group/qubes-devel/browse_thread/thread/77d076b65dda7226). If you hit this issue, you can switch to console (by console login option) and manually edit `/etc/X11/xorg.conf.d/00-system-setup-keyboard.conf` (and `/etc/sysconfig/keyboard`) and place correct keyboard layout settings (details in linked thread). You can check if specific keyboard layout settings are proper using `setxkbmap` tool.

-   On systems with more than 8GB of RAM there is problem with Disposable VM. To fix it, limit maximum memory allocation for DispVM to 3GB

    {% highlight trac-wiki %}
    qvm-prefs -s fedora-17-x64-dvm maxmem 3072
    qvm-create-default-dvm --default-template --default-script
    {% endhighlight %}

-   On some systems the KDE Window Manager might freeze upon resuming from S3 sleep when compositing is enabled (and the only method to log in to the system if this happens is to switch to a text console, enter your user's password, kill the kwin process, go back to the Xorg console, log in, and start a new instance of kwin using Konsole application :) If you experience such problems, make sure to disable compositing before putting the system into sleep by pressing Alt-Ctrl-F12 (and then enabling it back once you log in after resume) -- this way you should never see this problem again.

Getting Help
------------

-   **User manuals are [here](/doc/UserDoc).** (Strongly recommended!)

-   Developers documentation (normally not needed by users) is [here](/doc/SystemDoc)

-   If you don't find answer in the sources given above, write to the *qubes-devel* mailing list:
    -   [http://groups.google.com/group/qubes-devel](http://groups.google.com/group/qubes-devel)
    -   `qubes-devel@googlegroups.com`

