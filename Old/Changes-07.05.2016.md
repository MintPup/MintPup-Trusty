Changes in **MintPup-jwm-icewm-hybrid-07.05.2016.iso** compared to the previous version (MintPup-jwm-icewm-hybrid-26.07.2015.iso):

  
**1.**  Removed package linux-firmware.
Only short list of free firmware included in /lib/firmware (same list as in Debian firmware-linux-free package). Wired connection should work but your wifi will not work with the new iso.
The package linux-firmware includes more and more firmware every update. For example the updated linux-firmware is 42Mb (xz compression) compared to 32Mb in the previous iso. 99% of this firmware you will never need. My system for example works fine without linux-firmware package. Probabaly you will need only few files from this package.

You have few options:
   - Install it with apt-get or synaptic and remaster the system (adds 42Mb compressed):
```
sudo apt-get update
sudo apt-get install linux-firmware
```

   - Make bootable usb or frugal install, download this module [02-linux-firmware_1.127.14_all-xz.squashfs](https://github.com/MintPup/MintPup-Trusty/releases/download/v0.1/02-linux-firmware_1.127.14_all-xz.squashfs) and keep it inside "casper" folder where 01-filesystem.squashfs is (adds 32Mb compressed). It will be loaded on boot and includes all firmware from previous iso.
   - For advanced users: find the firmware only for device that doesn't work, copy the firmware (from the deb package or from the squashfs module) in /lib/firmware and reboot. [Example from William](http://murga-linux.com/puppy/viewtopic.php?p=833212#833212) how to do that for wifi devices.

**2.**  Removed package locales.
In case you like to change the localisation install locales package:
```
sudo apt-get update
sudo apt-get install locales
```

**3.**  Removed package casper.
If you don't know what is this package you will never need it.
If you need this package then you also know how to install it and use it.
