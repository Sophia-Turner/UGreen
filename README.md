# UGreen
UGreen DXP4800 Plus BIOS Watchdog Debian Bookworm Bootable SD Card

This documents my attempts to run Debian Bookworm Linux on a DXP4800 Plus NAS. I keep most of the files for reference. The lucky side affect of this work is finding a way to unlock the UGreen AMI BIOS making most of this work unnecessary now. The UGreen NAS can be treated like an ordinary x86 PC.

Step 1: See if it possible to boot from USB or SD card. Yes SD boot is preferred over NVMe boot by BIOS defaults.

Step 2: Copy the NVMe to the SD card making a bootable backup system for testing. This works and ran for weeks on the SD card.

Step 3: See if it is possible to create a flat filesystem so changes to the filesystem will survive reboot. Yes Read-only squashfs layers -> /rootfs/base, /rootfs/kernel, /rootfs/apt, /rootfs/fw, /rootfs/oem. Then OverlayFS upperdir -> /overlay/upper. Final merged root -> / Fix overlay filesystem whiteout. Initramfs init script has to be modified to skip the overlay process and just mount partition 7. Yes, the SD card now has a flat filesystem. Ran for weeks, no problems. Copied SD card root filesystem back to the boot NVMe. Now default boot runs a flat filesystem.

Step 4: Create a bootable SD card with partition 2 as a regular Debian Bookworm install leaving partition 7 the UGreen flat filesystem boot. Worked... for 2 minutes; then the watchdog timer kick in and rebooted the system. Debian does not have the custom UGreen kernel drivers to create the watdog timer major and minor numbers, therefore communication is not possible.

Step 5: Boot UGreen kernel but change initramfs init script to perform "Magic Close" on the watchdog timer and then kexec the Debian Bookworm kernel. Yes, it works. The watchdog timer is stopped. A running Debian Bookworm on UGreen's DXP4800 Plus hardware. All security patches and software work.

There is no step 6 except to document my findings.... except an error on my part to shrink the initramfs image file gave me access to the AMI BIOS.
