[More information from live-boot testing read here (should be valid for casper-boot also.)](https://github.com/MintPup/DebianDog-Wheezy/blob/master/live-boot-2/Extra-options.md)

In short if you have frugal install on sda1(ext2, 3, 4) inside "casper" folder create empty subfolder /casper/changes.dir and boot with nopersistent boot code (otherwise the save file will be loaded last but we need changes.dir to be loaded last):

```
title MintPup no persistent
root (hd0,0)
kernel /casper/vmlinuz boot=casper ignore_uuid cdrom-detect/try-usb=true showmounts union=aufs
initrd /casper/initrd.lz
```
Now run apt-get update, install some programs, make some desktop changes...

To save the changes before reboot run in terminal this version of snapmergepuppy script (just type snapmergepuppy) - (some small changes from the included version in directory locations):

```
#!/bin/bash

#2007 Lesser GPL licence v2 (http://www.fsf.org/licensing/licenses/lgpl.html)
#Barry Kauler www.puppylinux.com
#Edited for 'porteus-boot' on Debiandog for the "save on exit" boot option, by fredx181
#2016-02-26 Change; Line 89 "--remove-destination" instead of "-f", workaround possible crashing when copying files from upgraded libc6
#2016-07-14 saintless - just quick changes in directory location to make it work for casper-boot in /live/image/casper/changes.dir

export LANG=C #110206 Dougal: I **think** this should not cause problems with filenames

PATH="/bin:/sbin:/usr/bin:/usr/sbin:/usr/X11R7/bin"

SNAP="/live/cow"
cd $SNAP || exit 1

BASE="/live/image/casper/changes.dir"

echo "Merging $SNAP onto $BASE..."

SFSPoints=$( ls -d -1 /casper/* |sort -u ) #110206 Dougal: get a list of the sfs mountpoints

#Handle Whiteouts...
find . -mount \( -regex '.*/\.wh\.[^/]*' -type f \) | sed -e 's/\.\///;s/\.wh\.//' |
while read N
do
 BN="`basename "$N"`"
 DN="`dirname "$N"`"
 [ "$BN" = ".wh.aufs" ] && continue #w003 aufs has file .wh..wh.aufs in /initrd/pup_rw.
 #[ "$DN" = "." ] && continue
 #110212 unionfs and early aufs: '.wh.__dir_opaque' marks ignore all contents in lower layers...
 if [ "$BN" = "__dir_opaque" ];then #w003
  #'.wh.__dir_opaque' marks ignore all contents in lower layers...
  rm -rf "${BASE}/${DN}" 2>/dev/null #wipe anything in save layer. 110212 delete entire dir.
  mkdir -p "${BASE}/${DN}" #jemimah: files sometimes mysteriously reappear if you don't delete and recreate the directory, aufs bug? 111229 rerwin: need -p, may have to create parent dir.
  #also need to save the whiteout file to block all lower layers (may be readonly)...
  touch "${BASE}/${DN}/.wh.__dir_opaque" 2>/dev/null
  rm -f "$SNAP/$DN/.wh.__dir_opaque" #should force aufs layer "reval".
  continue
 fi
 #110212 recent aufs: .wh.__dir_opaque name changed to .wh..wh..opq ...
 if [ "$BN" = ".wh..opq" ] ; then
  rm -rf "${BASE}/${DN}" 2>/dev/null  #wipe anything in save layer.
  mkdir -p "${BASE}/${DN}" #jemimah: files sometimes mysteriously reappear if you don't delete and recreate the directory, aufs bug? 111229 rerwin: need -p, may have to create parent dir.
  #also need to save the whiteout file to block all lower layers (may be readonly)...
  touch "${BASE}/${DN}/.wh..wh..opq" 2>/dev/null 
  rm -f "$SNAP/$DN/.wh..wh..opq"  #should force aufs layer "reval".
  continue
 fi
 #comes in here with the '.wh.' prefix stripped off, leaving actual filename...
 rm -rf "$BASE/$N"
 #if file exists on a lower layer, have to save the whiteout file...
 #110206 Dougal: speedup and refine the search...
 for P in $SFSPoints
 do
   if [ -e "$P/$N" ] ; then
     [ ! -d "${BASE}/${DN}" ] && mkdir -p "${BASE}/${DN}"
     touch "${BASE}/${DN}/.wh.${BN}"
     break
   fi
 done #110206 End Dougal.
done

#Directories... v409 remove '^var'. w003 remove aufs .wh. dirs.
#w003 /dev/.udev also needs to be screened out... 100820 added var/tmp #110222 shinobar: remove all /dev
find . -mount -type d | busybox tail +2 | sed -e 's/\.\///' | grep -v -E '^mnt|^initrd|^proc|^sys|^tmp|^root/tmp|^\.wh\.|/\.wh\.|^dev/|^run|^var/run/udev|^run/udev|^var/tmp|^etc/blkid-cache' |
#110224 BK revert, leave save of /dev in for now, just take out some subdirs... 110503 added dev/snd
#find . -mount -type d | busybox tail +2 | sed -e 's/\.\///' | grep -v -E '^mnt|^initrd|^proc|^sys|^tmp|^root/tmp|^\.wh\.|/\.wh\.|^dev/\.|^dev/fd|^dev/pts|^dev/shm|^dev/snd|^var/tmp' |
while read N
do

 mkdir -p "$BASE/$N"
 #i think nathan advised this, to handle non-root user:
 chmod "$BASE/$N" --reference="$N"
 OWNER="`stat --format=%U "$N"`"
 chown $OWNER "$BASE/$N"
 GRP="`stat --format=%G "$N"`"
 chgrp $GRP "$BASE/$N"
 touch "$BASE/$N" --reference="$N"
done

#Copy Files... v409 remove '^var'. w003 screen out some /dev files. 100222 shinobar: more exclusions. 100422 added ^root/ftpd. 100429 modify 'trash' exclusion. 100820 added var/tmp #110222 shinobar: remove all /dev
find . -mount -not \( -regex '.*/\.wh\.[^/]*' -type f \) -not -type d |  sed -e 's/\.\///' | grep -v -E '^mnt|^initrd|^proc|^sys|^tmp|^pup_|^zdrv_|^root/tmp|_zdrv_|^dev/|^\.wh\.|^run|^var/run/udev|^run/udev|^root/ftpd|^var/tmp' | grep -v -E -i '\.thumbnails|\.trash|trash/|^etc/blkid-cache|\.part$'  |
#110224 BK: revert, leave save of /dev in for now... 120103 rerwin: add .XLOADED
#find . -mount -not \( -regex '.*/\.wh\.[^/]*' -type f \) -not -type d |  sed -e 's/\.\///' | grep -v -E '^mnt|^initrd|^proc|^sys|^tmp|^run|^pup_|^zdrv_|^root/tmp|_zdrv_|^dev/\.|^dev/fd|^dev/pts|^dev/shm|^\.wh\.|^var/run|^root/ftpd|^var/tmp|\.XLOADED$' | grep -v -E -i '\.thumbnails|\.trash|trash/|\.part$'  |
while read N
do

[ -L "$BASE/$N" ] && rm -f "$BASE/$N"

# Finally, copy files unconditionally.
cp -a --remove-destination "$N" "$BASE/$N"


 BN="`basename "$N"`" #111229 rerwin: bugfix for jemimah code (110212).
 DN="`dirname "$N"`" #111229  "
 [ -e "$BASE/$DN/.wh.${BN}" ] && rm "$BASE/$DN/.wh.${BN}" #110212 jemimah bugfix - I/O errors if you don't do this

done

# Remove files, corresponding with .wh files, from /live/image/casper/changes.dir
# Taken from 'cleanup' script included in the official Porteus initrd.xz 
MNAME="/live/image/casper/changes.dir"; NAME="basename "$MNAME""
find $MNAME -name ".wh.*" 2>/dev/null | while IFS= read -r NAME; do wh=`echo "$NAME" | sed -e 's^$MNAME^^g' -e 's/.wh.//g'`; test -e "$wh" && rm -rf "$NAME"; done

sync
exit 0

###END###

```

I'm not sure if we need to copy in changes.dir also /live/cow/.wh..wh.aufs, /live/cow/.wh..wh.plnk and /live/cow/.wh..wh.orph but it works without them. These files and folders are auto-generated on boot in /live/cow anyway. I guess we don't need them in changes.dir.
Otherwise it is easy to copy them once only with:
`cp -af /live/cow/.wh* /live/image/casper/changes.dir`
