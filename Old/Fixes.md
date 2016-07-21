Fixes included in **MintPup-jwm-icewm-hybrid-07.05.2016.iso**:

**1.** Starting remsterdog scripts from menu entry from user account results in broken pipe messages sometimes. From root account there is no problem.
Repository updated with new deb package with gsu-root and progressbar fixes.
Type (copy/paste) in terminal:
```
sudo apt-get update
sudo apt-get install remaster-scripts
```
More information available [here](http://www.murga-linux.com/puppy/viewtopic.php?p=859891#859891) and [here](http://www.murga-linux.com/puppy/viewtopic.php?p=859915#859915)

**2.** The frugal installer included in the iso doesn't create uuid line for casper-boot. And in full-installer added extra lines to remove ~/Startup/check-for-aufs-and-cow file to prevent the "aufs not found" message each boot using full install.
To fix this run (copy-paste) in terminal:
```
sudo apt-get update
sudo apt-get install mintpupinstallscripts
```
**Update**: All previous bugs are fixed now in version 1.0.8, install:
```
sudo apt-get update # if required
sudo apt-get install mintpup-repo-updater
sudo apt-get update # apply new change in repositories listing
sudo apt-get install mintpupinstallscripts
```
Or download and install the package
[mintpupinstallscripts_1.0.8_i386.deb](http://www.smokey01.com/saintless/MintPup/Packages/Included/mintpupinstallscripts_1.0.8_i386.deb)

**3.** For anyone using full install the right click option in xfe -> scripts -> load-sfs-chroot-full_install will not work from user account (works fine as root). To be able to load squashfs modules from user account in full install edit /opt/bin/load-sfs-chroot-full_install adding gsu:
```
#!/bin/bash
gsu loadsfs-fuse "$@"
```

**4.** With porteus-boot creating save file on shutdown doesn't display progress bar.
Use the script [attached here](http://murga-linux.com/puppy/viewtopic.php?p=859536#859536)

**5.** After exit X you need to press Enter before typing command (as startx for example). This is because some process from acpid package is still active. For more information read [here.](http://murga-linux.com/puppy/viewtopic.php?p=859994#859994) I don't think this needs fixing because this is the default acpid behaviour but removing acpid solves the issue.

**6.** Installing and running **rythmbox** (and maybe similar packages) could fail with message like:
```
GLib-GIO-ERROR : Settings schema 'org.gtk.Settings.FileChooser
```
The problem is in some schemas from Wheezy included in gnome-mplayer-1.0.7 package. More information read [here.](http://murga-linux.com/puppy/viewtopic.php?p=863936#863936) To fix this download [gnome-mplayer-fix.zip](http://murga-linux.com/puppy/viewtopic.php?mode=attach&id=90533) and extract the archive in /opt/bin (or /usr/bin). Typing this is enough to fix the problem in your running system:
```
sudo gnome-mplayer-fix
```
Or you can upgrade fixed gnome-mplayer-1.0.7 deb with:
```
    sudo apt-get update
    sudo apt-get install gnome-mplayer-1.0.7
```

**7.** The installer scripts bugs fixing created new problem and the last two versions removed from the repository. 
Recommended to make sure you have this version on your system by reinstalling this package: [mintpupinstallscripts_1.0.4_i386.deb](http://kazzascorner.com.au/saintless/MintPup/Packages/Included/mintpupinstallscripts_1.0.4_i386.deb)

Or by running in terminal:
```
sudo apt-get update
sudo apt-get remove mintpupinstallscripts
sudo apt-get install mintpupinstallscripts
```
In case you have some problem with this package check out for updated deb package from Fred [here.](http://murga-linux.com/puppy/viewtopic.php?p=877514#877514)

**8.** The included /sbin/cryptsetup doesn't create working encrypted save file. Install this fixed [cryptsetup-bin_1.6.6-9-mintpup_i386.deb](http://kazzascorner.com.au/saintless/MintPup/Packages/Included/cryptsetup-bin_1.6.6-9-mintpup_i386.deb) or run in terminal:
```
sudo apt-get update
sudo apt-get install cryptsetup-bin
```

**9.** Some fixes from Fred for fixdepinstall (install deb and dependencies right click option):
[More information read here.](http://murga-linux.com/puppy/viewtopic.php?p=871384#871384)
Install fixed version from the link above or from terminal:
```
sudo apt-get update
sudo apt-get install fixdepinstall
```

**10.** Only for **casper-boot** users - [Alternative initrd.lz](https://github.com/MintPup/MintPup-Trusty/releases/download/v0.1/initrd.lz) and [md5sum](https://github.com/MintPup/MintPup-Trusty/blob/master/md5sum) - With the official initrd.lz for casper-boot (included in the iso) you can use save file only on vfat partition for security reasons. There is a warning in casper-helpers script - **being able to mount a journalled filesystem and replay the journal could cause data loss when a live CD is booted on a system where filesystems are in use by hibernated operating systems**. This initrd.lz includes mods to load casper-rw save file (also encrypted) from ntfs, ext2, ext3, ext4 and vfat partitions. 
I don't know if this warning is valid for porteus-boot and any other linux with persistent options to use save file on ext and ntfs partitions. Maybe they have some kind of prevention but I can't confirm or deny this.

**11.** Fix for squashfs module loading scripts from Fred. [More information read here.](http://murga-linux.com/puppy/viewtopic.php?p=878996#878996)
```
sudo apt-get update
sudo apt-get install sfsload portablesfs-loadsfs-fuse
```

**12.** Small fix for frisbee. [More information read here.](http://murga-linux.com/puppy/viewtopic.php?p=883158&sid=3588429564754e676ce49df134d930a8#883158)
```
sudo apt-get update
sudo apt-get install frisbee
```

**13.** Small fix for apt2sfs. [More information read here.](http://murga-linux.com/puppy/viewtopic.php?p=885536&sid=e09b92e591e85bcc4632168abdb32e5b#885536)
```
sudo apt-get update
sudo apt-get install apt2sfs
```

**14.** Fix for porteus-boot initrd1.xz. More information about this problem read [here.](http://murga-linux.com/puppy/viewtopic.php?p=885874&sid=a1a579b99b8a00be9a2b36bc9a227635#885874)
In case you are using **encrypted save file and usb keyboard** use this fixed [initrd1.xz-hid-mintpup](https://github.com/MintPup/MintPup-Trusty/releases/download/v0.1/initrd1.xz-hid-mintpup) instead initrd1.xz included in the iso. It will be replaced in next iso update.
In case you don't use **encrypted save file + usb keyboard** combination there is no need to change initrd1.xz yet.

**15.** With porteus-boot and only when using save on exit code upgrading libc6 could create some issues. More information and workarownd read [here](http://murga-linux.com/puppy/viewtopic.php?p=889934&sid=00f59036fe7b1df6f8bc7168fe1df597#889934) and the fix [here.](http://murga-linux.com/puppy/viewtopic.php?p=890342&sid=00f59036fe7b1df6f8bc7168fe1df597#890342)
Install this package to fix the problem:
```
sudo apt-get update
sudo apt-get install porteusbootscripts

```
Porteus-boot scripts will be upgraded to new versions with some improvements for save on exit. The changes are not well tested in MintPup yet. In case you experience some problem you can downgrade the package to the previous version (it has only the fix in snapmergepuppy script for the problem described above and will restore the other scripts to the versions included in the iso):
```
sudo apt-get install porteusbootscripts=0.0.1

```

**16.** New packages uploaded **sfs-get-mintpup** and **mintpup-repo-updater**.

We need to change some scripts and free some space by removing modules from kazzascorner.com.au and smokey01.com in order to make the maintaining easier. Sfs-get script will download the extra modules from github.com in the future for all DD versions and MintPup.

**It is recommended to install both packages by running these commands to keep sfs-get and mintpup-repo up to date:**
```
sudo apt-get update
sudo apt-get install mintpup-repo-updater sfs-get-mintpup
sudo apt-get update
```
