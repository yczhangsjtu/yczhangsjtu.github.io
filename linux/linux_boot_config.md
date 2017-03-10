---
title: Coonfigure Linux Boot Procedure
---

# Configure Linux Boot Procedure

## Exploring the Boot Process

* The computer booting process
    1. The **CPU** initializes itself
    2. The **CPU** finds **firmware** (like BIOS) codes on a particular part of the memory to run
    3. The **firmware** performs basic checks
    4. The **firmware** looks for **boot loader** on storage device like hard disk
    5. The **boot loader** looks for operating system and loads the **kernel**
    6. The **kernel** looks for its first process file. In linux, this is usually `/sbin/init`. This process starts to run as `init` process.
    7. The `init` process reads several configuration files to know what other programs it should launch

* The linux start up process
    * The computer mounts disks using the `mount` utility under direction of `/etc/fstab` file
    * It may also perform checks using the `fsck` utility
    * The system normally launches user login tools (text mode `login` or X Display Manager login screen)
    * Server systems launches server programs, some of which perform login possibilities for remote users

* Remark: BIOS has been used as firmware since 1980s, and it is a little dated. A shift is underway to a new system called Extensive Firmware Interface (EFI).

## Configure linux start up process

* **Grand Unified Bootloader (GRUB)** is used in most installations of linux, and can load several OSs.
* There are two versions of GRUB:
    * GRUB Legacy has been frozen at version 0.97
    * GRUB 2 is now under active development
* GRUB Legacy's configuration file is usually `/boot/grub/menu.lst`, some distributions use `grub.conf` instead.
* GRUB Legacy reads the configuration time at boot time.
* A sample GRUB Legacy configuration file looks like this
```
# grub.conf
#
# Global Options:
#
default=0
timeout=15
splashimage=/grub/bootimage.xpm.gz
#
# Kernel Image Options:
#
title Fedora (2.6.32)
  root (hd0,0)
  kernel /vmlinuz-2.6.32 ro root=/dev/hda5 mem=2048M
  initrd /initrd-2.6.32
title Debian (2.6.36-experimental)
  root (hd0,0)
  kernel (hd0,0)/bzImage-2.6.36-experimental ro root=/dev/hda6
#
# Other operating systems
#
title Windows
  rootnoverify (hd0,1)
  chainloader +1
```
* The first line `default=0` tells GRUB to boot the first listed OS to boot by default.
* The `timeout=15` tells GRUB to wait for the user input for 15 seconds beforing booting into the default OS.
* The `splashimage=` line points to graphics file displayed as the background for the boot process. The path is relative to the GRUB Legacy root partition. Alternatively, the path may begin with a GRUB device specification, such as `(hd0,5)` to refer to a file on that partition.
* For each listed OS:
    * **Title** specifies the label to display for the system when boot loader runs. It accepts spaces.
    * **GRUB Legacy Root** specifies the location of GRUB legacy's root partition. They use GRUB style device identifiers, in the example both linux OSs uses first partition on the first disk as GRUB Legacy root partition.
    * **Kernel Specification** location of the linux kernel, and any other kernel options are passed to it (like the `root=`).
        * The `ro` option tells the kernel to mount its root file system read only.
        * The `root=` option specifies the linux root file system. They use linux style device identifiers like `/dev/hd5`.
    * **Initial RAM Disk** use `initrd` to specify **initial RAM disk**, which can be used to store loadable kernel modules and some basic tools used in boot process.
    * **Non Linux Root** The `rootnoverify` option is similar to `root` option, used to specify a boot partition for which GRUB Legacy cannot directly load the kernel, like Windows.
    * **Chain Loading** The `chainloader` tells GRUB Legacy to pass control to another boot loader, typically it is `+1` to load the first sector of the root partition specified by `rootnoverify`.
* GRUB Legacy refers to disk drives by numbers preceded by `hd`, enclosed by parenthesis, like `(hd0)` denotes the first hard disk detected by GRUB.
* GRUB Legacy's drive mapping can be found in `/boot/grub/device.map`.
* Additionally, GRUB Legacy numbers partitions on hard disks starting from 0. So `(hd0,0)` denotes the first partition on the first hard disk.
* GRUB Legacy's root partition is different from Linux's root partition. GRUB's root partition is the partition in which `grub.conf` file resides. Usually they are the same, if you do not use a separate `/boot/grub` partition.
* GRUB 2 configuration is much like GRUB Legacy, with some important different details (following are some examples):
    * The configuration file of GRUB 2 is `/boot/grub/grub.cfg`.
    * GRUB 2 supports conditional logic statements.
* Following is an example of GRUB 2 configuration file:
```
#
# Kernel Image Options
#
menuentry "Fedora (2.6.32)" {
  set root=(hd0,1)
  linux /vmlinuz-2.6.32 ro root=/dev/hda5 mem=2048M
  initrd /initrd-2.6.32
}
menuentry "Debian (2.6.36-experimental)" {
  set root=(hd0,1)
  linux (hd0,1)/bzImage-2.6.36-experimental ro root=/dev/hda6
}
#
# Other operation systems
#
menuentry "Windows" {
  set root=(hd0,2)
  chainloader +1
}
```
* The modifications are self explainatory. Note that partitions are numbered from 1 instead of from 0.
* GRUB 2 makes further changes, and employs a set of scripts and other tools that help automatically maintain the the `/boot/grub/grub.cfg` file. It is intended that the administrators need never edit this file explicitly. Instead, you would edit files `/etc/grub.d` and `/etc/default/grub` file.
* Files in `/etc/grub.d` are scripts controlling particular GRUB OS probers. The scripts like `10_linux` scan the system for particular OSs and kernels and add GRUB entries to `/boot/grub/grub.cfg`. You can add custom kernel entries to `40_custom` file.
* The `/etc/default/grub` controls the defaults created by the configuration scripts. You can modify the `GRUB_DEFAULT=` or `GRUB_TIMEOUT=` lines, and then `update-grub` to exert effects on `/boot/grub/grub.cfg`.

## Installing the GRUB Boot Loader

* The command for installing GRUB Legacy or GRUB 2 is `# grub-install`. The command is like `# grub-install /dev/hda` or `grub-install '(hd0)'` to install it in MBR of that device.
* You can further specify the partition if you do not want to install it in MBR.

## Interacting with GRUB at Boot Time

* In the GRUB boot menu, press `E` to edit the boot option highlighted.
* Press `B` to start booting in GRUB Legacy, or `Ctrl+X` to start booting in GRUB 2.
* Press `C` to enter interactive mode, you will be given a `grub>` prompt.
* `grub> ls` will give you list of partitions.
* To view the contents of that partition, command like `ls (hd0,0)/`.

## Customizing System Setup

* Linux relies on **runlevel** to determine which features are available. **Runlevel** are numbered from 0 to 6.
* Upon booting, Linux enters a predetermined runlevel which you can set.
* To check the current runlevel, type command `runlevel`. The result may be like `N 5`. The `N` means the system has not switched runlevel since booting.
* To change runlevel in running system, type command like `init 1`. Recall that `init` is the first process run by linux, yet you can still use it in command. This command takes effect immediately. For example, `init 0` does exactly what `shutdown` does, exept that `shutdown` would also inform other users of the shutdown and supports many functionalities.
* An alternative command is `telinit`. It does the same thing as `init`, but supports some more commands like `Q` or `q` to reread the configuration files and implements them immediately. In some systems `telinit` may just be a link to `init`.
* It is recommended to use `shutdown`, `reboot` or `poweroff` instead of `init 0` and `init 6`. Typically, `shutdown` and `reboot` are symbolic links to the third, and the same binary reacts differently according to the name used to call it.

| **Runlevel** | **Purpose** |
| ------------ | ----------- |
| 0 | Shutdown the system |
| 1,s,S | Single user mode. Typically used for low-level system maintenance. |
| 2 | On Debian based system, full multi-user mode, with X graphical system running. For other systems this is usually empty. |
| 3 | On Fedora, Red Hat, full multi-user mode without X running. |
| 4 | Undefined by default. |
| 5 | Similar to 3 with X running. |
| 6 |  Reboot the system. |


* The `/etc/rc?.d` directories contain runlevel specific scripts, where `?` is the runlevel number.
* Note that some scripts start with `K` and some start with `S`. When entering a runlevel, `rc` passes the start parameter to all `S`-started scripts and passes the stop parameter to all `K`-started scripts.
* The scripts are also numbered after the `S` or `K` denoting the order in which services are started or stopped.
* The scripts in `/etc/rc?.d` are usually links to `/etc/init.d`.
* The `update-rc.d` program is most common on Debian based distributions to manage runlevel services. Its basic syntax is
```bash
update-rc.d [options] name action
```
* The most common action is `-n`, which causes the program to report what it will do without taking any real action. The `name` is the name of service, and `action` is the name of action to be performed, along with action-specific options.

| **Action code** | **Effect** |
| --------------- | ---------- |
| `remove` | Cleanup the startup scripts after a service has been completely removed from the system |
| `defaults` | Create links to start service in runlevels 2,3,4,5, and stops in runlevels 0,1 |
| `start NN runlevel` | Create link to start service in specified runlevel using sequence number `NN` |
| `stop NN runlevel` | Create link to stop service in specified runlevel using sequence number `NN` |
| `enable [runlevel]` | Modify links to enable service in specified runlevel (default 2,3,4,5) |
| `disable [runlevel]` | Modify links to disable service in specified runlevel (default 2,3,4,5) |


* As an example
```bash
$ update-rc.d samba defaults
$ update-rc.d gdm disable 234
```

* Some linux systems use an `init` process called **Upstart**. The configuration files locate in `/etc/init`.
* Open any of the `/etc/init/name.conf` file, you may see something like
```bash
start on runlevel [2345] and not-container
stop on runlevel [!2345]
```
* After modifying the configurations, you can immediately start or stop the service as in `start gdm` or `stop gdm`. And command `initctl reload` before trying to change runlevel.
