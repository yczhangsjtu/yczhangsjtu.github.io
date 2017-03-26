---
title: Compile the Linux Kernel
---

# Compile the Linux Kernel

## Obtain the Kernel

* The home site for Linux Kernels is the [Linux Kernel Archives](http://www.kernel.org).:w
* It can also be obtained via package management tools like `apt`. The package name for the kernel source should be `linux-source-version`.
* Linux kernels have three or four dot-separated numbers as version number.
* Type `uname -r` to obtain the current kernel version. And `uname -a` returns more information.
* If you `sudo apt-get install linux-source-4.8.0`, you will find a directory `linux-source-4.8.0` under `/usr/src`. Other directories may also exist like `linux-headers-4.8.0`, they contain just the header files used to compile other programs.
* After compilation, the kernel yields quite a few files, mainly divided into two categories:
    * **The Main Kernel File** loaded by the boot loader in the booting process, mainly resides in `/boot`, very critical to the system
    * **Kernel Modules** mainly locate in `/lib/modules` directory, can be loaded and unloaded at run time
* The **main kernel file** may take on different names
    * `vmlinux`
    * `vmlinuz`
    * `zImage`
    * `bzImage`
    * `kernel`
* Sometimes a kernel version is appended to the kernel name, like `vimlinuz-4.8.0-22-generic`. You can keep multiple versions of kernels on your computer.

## Preparing a Kernel

Before compiling your own kernel, you need some steps of preparation.
Usually, the `/usr/src/linux-source/README` file will contain enough information for you.
Read them carefully.

### Patch the Kernel Source

* Patching a kernel is not always necessary. It enables you to make changes to the kernel source, adding some features.
* Download the patch and `cd` into the source directory. Type the following command (modified accordingly)
```bash
$ sudo gzip -cd /path/to/patch.gz | patch -p1
```
Or you can use script as in `/usr/src/linux-source/scripts/patch-kernel linux`.
For details read the `README` file.

### Configure the Kernel Source

* This is a most important step in compiling the kernel sources.
* Configuring the kernel can be a daunting task because of the vast number of kernel options available.
* Chances are most of the options won't need to be changed, if you are starting from your distribution's version of the kernel.
* If you just start from a kernel downloaded directly from the kernel website, you better know a lot about your hardware, or you may obtain an unbootable kernel.

* You should pay particular attention to drivers for
    * Hard disk controller
    * Ethernet adapter
    * USB controller
    * Video adapter
* Check your PCI deviced by `lspci`, USB devices by `lsusb`.
* Check currently loaded modules my `lsmod`.

* When you are sure that you know enough details about your hardware, start the configuration by `make target`, where the `target` is be one of the configuration targets, based on your own needs. For details refer to the `README` file.
* To clean up old configuration files, `make mrproper`.
* If you have the source for a working kernel, copy the `.config` file here, and `make oldconfig`, then you will only be asked about the new options, others will be based on the existing `.config` file.
* If you do not have the source code, and do not want to waste too much time on configuration, you can start by `make allmodconfig`, and get a kernel with as many modules as possible. It costs time to compile modules for hardwares that you do not have, but this kernel works on many computers.
* Then, you can further optimize your configuration by using the `menuconfig`, `xconfig` or `gconfig` targets.
