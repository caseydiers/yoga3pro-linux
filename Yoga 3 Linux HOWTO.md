Installing Ubuntu 14.10 on Yoga 3 Pro

With this intructions you get a Ubuntu 14.10 on a Yoga 3 Pro from Leono in full SecureBoot enabled UEFI mode. This means dual boot to Windows 8 works just fine. When you turn on the Yoga Grub will greet you and you can Choose to boot Ubuntu (the default) or Windows.

# Create recovery USB flash drive

If you ever want to restore the original partition layout and the recovery partition make sure to not forget this step. If you have a recovery USB flash drive already (for Yoga 3) you can skip this step.

Requirements: USB flash drive with at least 16GB capacity. All data will be overwritten.

1. Boot into Windows 8.1

2. Do not connect to Internet and create a local account.

3. Open Control Panel

4. Select Recovery

5. Select Advanced recovery tools

6. Select Create a recovery drive

7. Select "Copy the recovery partition from the PC to the recovery drive" checkbox

8. Select the USB drive from the list

9. Continue and wait for the process to finish. It takes about half an hour.

With this USB flash drive you can later reset to the default content of the SSD including the recovery partition. (Boot from USB, Troubleshoot, Reset your PC, Next, Yes to re-partition, Fully clean the drive, Reset).

# Shrink Windows partition (Dual boot)

The partition can be shrinked with the Windows partition manager. It is important that you do this as early as possible to avoid long defragmentation. Shrink the Windows partition so it has around 100GB capacity (eg. shrink by 340000MB for a 512GB drive).

1. Shrink Windows partition to around 100GB

2. Make sure there is empty space (330 GB) after shrink.

Shrinking should be quick / take no time.

# Disable Windows8 FastStartup (Dual boot)

To boot another OS with EFI FastStartup of Windows8 must be disabled.

1. Open Control Panel

2. Go to Power Options -> System Settings

3. Enable Administrator access (click on "Change settings that are currently unavailable")

4. Uncheck (Turn on fast startup)

5. Save changes

# BIOS Settings

To enter the BIOS shut down the Yoga 3. Then long press the small button (with a pen or something). The button is called the Novo button and is located next to the power button on the right. When long pressing this button, the Yoga 3 will turn on and show the boot menu, which includes an option to enter the BIOS.

Check the following BIOS settings:

1. Secure Boot [Enabled]

2. Boot Mode [UEFI]

3. Fast Boot [Enabled]

4. USB Boot [Enabled]

# Boot from USB flash drive (Ubuntu 14.10)

Prepare a flash dive with Ubuntu 14.10 or later image. Use the Novo button to get into boot menu and select the "EFI USB Device". This should show Grub - select “Try Ubuntu without installing”.

1. Boot Ubuntu 14.10 USB flash drive

2. Try Ubuntu without installing

3. Test Ubuntu to see if everything works

    1. Check touchpad (works)

    2. Check touchscreen (works)

    3. Check keyboard (works)

    4. Check screen dim (works)

    5. Check audio (works)

    6. Check wifi (not working)

# Wifi

Wifi is not working out of the box as it needs a driver. The chip is a BCM4352 from Broadcom. To get Wifi working just install "bcmwl-kernel-source" which is available on the installation image.

sudo apt-get install bcmwl-kernel-source

After that the module should be compiled and loaded. Check with dmesg (wlan0 should appear). We now have a wifi0 interface but it is blocked by hardware switch. This is because the ideapad-laptop module is broken in the default kernel and needs a patch ([http://ubuntuforums.org/showthread.php?t=2215044](http://ubuntuforums.org/showthread.php?t=2215044)) as the Yoga’s do not have Wifi kill switches. So remove it for now.

sudo modprobe -r ideapad_laptop

Voila, wifi suddenly works like a charm and you can connect to Wifi using network manager. To make this persistent, add the module to the blacklist.

	sudo echo "blacklist ideapad_laptop" > /etc/modprobe.d/blacklist-yoga3.conf

# Install Ubuntu 14.10

Make sure you have booted with EFI by checking if /sys/firmware/efi exists. Do not continue if it does not exist and reboot in EFI mode (Dual boot). Ubuntu can be installed now on the SSD just fine. Click on Install Ubuntu and make the following selections:

1. Something else (As we want full disk encryption)

2. In the free space, create partition 500MB ext4 /boot

3. In the remaining space, create a partition "Use as physical volume for encryption"

4. The newly created partition is created under /dev/mapper. Select this partition and click "Change…" button and choose ext4 /

5. As device for boot loader installation select the EFI partition (probably /dev/sda2)

6. We do not create a swap partition. So click on "Install Now"

7. Select Timezone and Keyboard

8. Create your user. Do not select to encrypt the home folder if you are the only user on this machine

Wait until the system is installed. This should take around 5 minutes. When done, restart the system. It did not reboot for me .. so hard reset.



Remember that the Wifi will not work, until the drivers are installed. So install them by accessing the installation USB drive again.

1. Mount USB installation image from Files

2. Install dkms from pool/main/d/dkms

3. Install bcmwl-kernel-source from pool/restricted/b/bcmwl

4. Blacklisting is not required anymore, as the problem was fixed in Kernel 3.16.0-29 - check with "rfkill list all"

Voila - we are complete.

# HiDPI support

Ubuntu supports scaling up the Unity interface in System Settings -> Displays. I use 1.5 as scale factor. In Firefox, go to about:config and set layout.css.devPixelsPerPx to 2.

# Dual screen / external display

The external display works out of the box, including 4K support through the micro HDMI port. Ubuntu 14.10 comes with Kernel 3.16 which has the latest Intel drivers. So at time of writing there is no better Intel driver. See known issues.

# Improve battery life

TLP is an adwanced power management tool for Linux that automatically handles settings and tweaks to enhance the battery live. It does not have a GUI and just runs in the background.

sudo apt-add-repository ppa:linrunner/tlp

sudo apt-get update

sudo apt-get install tlp tlp-rdw smartmontools ethtool powertop

sudo tlp start

# Known issues

* Sometimes hangs on shutdown / reboot

* GPU sometimes hangs after resume - X is dead

* HiDPI with external displays is a little flaky and can result in strange redraw issues

# Extra steps after installing Ubuntu

Some things you should do after installing Ubuntu to get the most out of your installation.

* Turn off the online search. Open System Settings and head to Security & Privacy. In the Search tab turn off the online search results.

* Install restricted extras "sudo apt-get install ubuntu-restricted-extras".

* Remove Apport "sudo apt-get remove apport" to avoid leaking private information / turns of bug reports.

* Install tweak tools "sudo apt-get install unity-tweak-tooll". Use the Unity tweak tool to enable multiple desktops (i use 3x3).
