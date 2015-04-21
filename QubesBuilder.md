---
layout: doc
title: QubesBuilder
permalink: /doc/QubesBuilder/
redirect_from: /wiki/QubesBuilder/
---

Building Qubes from scratch
===========================

We have recently created a fully automated build system for Qubes, that downloads, builds and packages all the Qubes components, and finally should spit out a ready-to-use installation ISO.

In order to use it one should use an rpm-based distro, like Fedora :) and should ensure the following packages are installed:

-   git
-   createrepo
-   rpm-build
-   make
-   wget
-   rpmdevtools
-   pandoc

Unusually one can install those packages by just issuing:

{% highlight trac-wiki %}
sudo yum install git createrepo rpm-build make wget rpmdevtools pandoc
{% endhighlight %}

The build system creates build environments in chroots and so no other packages are needed on the host. All files created by the build system are contained within the qubes-builder directory. The full build requires some 25GB of free space, so keep that in mind when deciding where to place this directory.

The build system is configured via builder.conf file -- one should copy the attached builder.conf.default, and modify it as needed, e.g.:

{% highlight trac-wiki %}
cp builder.conf.default builder.conf 
# edit the builder.conf file and set the following variables: 
# (make sure to leave no spaces around '=' sign!) 
NO_SIGN=1

# As time of writing this, the default for Qubes 2 Dom0 is fc18
# and VMs is fc18 so if you want to build Qubes 2
DIST_DOM0=fc18
DISTS_VM=fc18
{% endhighlight %}

One additional useful requirement is that 'sudo root' work without any prompt, which is default on most distros (e.g. 'sudo bash' brings you the root shell without asking for any password). This is important as the builder needs to switch to root and then back to user several times during the build process.

Additionally, if building with signing enabled (so NO\_SIGN is not set), one must adjust \~/.rpmmacro file so that it point to the GPG key used for package signing, e.g.:

{% highlight trac-wiki %}
%_signature gpg
%_gpg_path /home/user/.gnupg
%_gpg_name AC1BF9B3  # <-- Key ID used for signing
{% endhighlight %}

It is also recommended to use an empty passphrase for the private key used for signing. Contrary to a popular belief, this doesn't affect your key or sources security -- if somebody compromised your system, then the game is over, whether you use additional passphrase for the key or not.

So, to build Qubes one would do:

{% highlight trac-wiki %}
# Import the Qubes master key 
gpg --recv-keys 0x36879494 

# Verify its fingerprint, set as 'trusted'. 
# This is described here: 
# https://wiki.qubes-os.org/trac/wiki/VerifyingSignatures 

wget http://keys.qubes-os.org/keys/qubes-developers-keys.asc 
gpg --import qubes-developers-keys.asc 

git clone git://git.qubes-os.org/joanna/qubes-builder.git qubes-builder 
cd qubes-builder 

cp builder.conf.default builder.conf 
# edit the builder.conf file and set the following variables: 
# (make sure to leave no spaces around '=' sign!) 
# NO_SIGN="1"

# Download all components:

make get-sources

# And now to build all Qubes rpms (this will take a few hours): 

make qubes 

# ... and then to build the ISO 

make iso 
{% endhighlight %}

And this should produce a shiny new ISO.

You can also build selected component separately. Eg. to compile only gui virtualization agent/daemon:

{% highlight trac-wiki %}
make gui-daemon
{% endhighlight %}

Full list you can get from make help. For advanced use and preparing sources for use with [QubesBuilder](/doc/QubesBuilder) take a look at [QubesBuilderDetails](/doc/QubesBuilderDetails) page.

Making customized build
-----------------------

### Manual source modification

If you want to somehow modify sources, you can also do it, here are some basic steps:

1.  Download qubes-builder as described above (if you want to use marmarek's branches, you should also download qubes-builder from his repo - replace 'joanna' with 'marmarek' in above git clone command)
2.  Edit builder.conf (still the same as above), some useful additions:
    -   As time of writing this, the default is fc15, but latest supported is fc17, so switch to newer one

        {% highlight trac-wiki %}
        DISTS_VM="fc17"
        {% endhighlight %}

    -   You can also set GIT\_SUBDIR="marmarek" to use my repo instead of "mainstream" - it contains newer (but less tested) versions

1.  Download unmodified sources

    {% highlight trac-wiki %}
    make get-sources
    {% endhighlight %}

1.  **Make your modifications here**

1.  Build the Qubes
     `make qubes` actually is just meta target which build all required components in correct order

    {% highlight trac-wiki %}
    grep ^qubes: Makefile
    qubes: get-sources xen core kernel gui addons docs template kde-dom0 installer qubes-manager dom0-updates sign-all
    {% endhighlight %}

> `get-sources` is already done, so continue with the next one. You can skip `sign-all` if you've disabled signing
>
> {% highlight trac-wiki %}
> make xen core kernel gui addons docs template kde-dom0 installer qubes-manager dom0-updates
> {% endhighlight %}

1.  build iso installation image

    {% highlight trac-wiki %}
    make iso
    {% endhighlight %}

### Non-default git branches

**Below example values (especially branch names) are outdated, but config options and steps are still valid**

You can use above tool to build Qubes with some components modified. Besides manual source modification, it is possible to use non-default git repositories, or just another branches. For example to try (**unofficial, not supported**) configuration with newer kernel and xorg server, you can add to builder.conf:

{% highlight trac-wiki %}
GIT_SUBDIR="marmarek"
BRANCH_kernel=devel-3.4
BRANCH_dom0_updates=devel/xserver-1.12

# NO_SIGN=1 can also be useful
{% endhighlight %}

before doing "make qubes". If you built anything before this modification, you should remove qubes-src directory first to fetch the sources again (this will also remove packages compiled before). Above devel/xserver-1.12 branch require some modification of Makefile in qubes-builder. Find dom0-updates target and replace it with:

{% highlight trac-wiki %}
dom0-updates:
        MAKE_TARGET="stage0" ./build.sh $(DIST_DOM0) dom0-updates
        MAKE_TARGET="stage1" ./build.sh $(DIST_DOM0) dom0-updates
        MAKE_TARGET="stage2" ./build.sh $(DIST_DOM0) dom0-updates
        MAKE_TARGET="stage3" ./build.sh $(DIST_DOM0) dom0-updates
        MAKE_TARGET="stage4" ./build.sh $(DIST_DOM0) dom0-updates
{% endhighlight %}

Then you can build qubes as usual.

There is one issue with above experimental version: new Xorg server have different cmdline options, so after installation one config file must be edited to reflect this change. At first system startup hit ESC while progress bar is displayed - to switch to detailed boot view. After you set up user account etc, you must fix KDM config file.

1.  Switch to tty2 (Alt-F2)
2.  Login as just created user
3.  Switch to root (sudo -s)
4.  Edit /etc/kde/kdm/kdmrc
    -   locate "[ServerArgsLocal?](/doc/ServerArgsLocal)=-nr -nolisten tcp" line
    -   remove "-nr" from it

5.  Restart login manager (now it will start and allow you to login graphically): initctl restart prefdm

Code verification keys management
=================================

[QubesBuilder](/doc/QubesBuilder) by default verifies signed tags on every downloaded code. Public keys used for that are stored in `keyrings/git`. By default Qubes developers' keys are imported automatically, but if you need some additional keys (for example your own), you can add them using:

{% highlight trac-wiki %}
GNUPGHOME=$PWD/keyrings/git gpg --import /path/to/key.asc
GNUPGHOME=$PWD/keyrings/git gpg --edit-key ID_OF_JUST_IMPORTED_KEY
# here use "trust" command to set key fully or ultimately trusted - only those keys are accepted by QubesBuilder
{% endhighlight %}

All Qubes developers' keys are signed by the Qubes Master Signing Key (which is set as ultimately trusted key), so are trusted automatically.

If you are the owner of Master key and want to revoke such signature, use the `revsig` gpg key edit command and update the key in qubes-developers-keys.asc - now the key will be no longer trusted (unless manually set as such).
