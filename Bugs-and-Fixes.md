
**List of available fixes:**

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

**10.** Only for **casper-boot** users - [Alternative initrd.lz](https://github.com/MintPup/MintPup-Trusty/releases/download/v0.1/initrd.lz) and [md5sum](https://github.com/MintPup/MintPup-Trusty/blob/master/md5sum) - With the official initrd.lz for casper-boot (included in the iso) you can use save file only on vfat partition for security reasons. There is a warning in casper-helpers script - **being able to mount a journalled filesystem and replay the journal could cause data loss when a live CD is booted on a system where filesystems are in use by hibernated operating systems**. This initrd.lz includes mods to load casper-rw save file (also encrypted) from ntfs, ext2, ext3, ext4 and vfat partitions. But use it at your own risk!
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
