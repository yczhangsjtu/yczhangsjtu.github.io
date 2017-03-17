---
Linux: Basic Filesystem Management
---

# Linux: Basic Filesystem Management

This blog is the note taken learning the book *Linux Professional Institute Certification STUTY GUIDE* by Roderick W. Smith.

## Mount Filesystem

* **Mount** makes a filesystem accessible by linux system. By `mount`, a filesystem is added as a part of the entire linux directory tree.
* The command `mount` supports a lot of options, but usually you do not need them. Just specify the device and the **mount point** is enough. Read `man mount` if you need to learn about the options.
```bash
mount /dev/hd7 /mnt/shared
```
* Usually, only the root can mount the filesystem. But if `/etc/fstab` specifies that `/dev/hd7` is destined to be mounted to `/mnt/shared`, a user can mount the device by `mount /dev/hd7` or `mount /mnt/shared`.
* The mount point must exist, and is usually empty. If it is not empty, after mount the contents will be invisible. But they still exists, and will show again after the filesystem is unmounted.
* You can specify how to mount a filesystem in `/etc/fstab` to save your time mounting one device over and over again. The structure of `/etc/fstab` is a table, each line (if not commented) is an entry for one device, and each column (separated by spaces and tabs) is a specific parameter for mounting that device.
    * The first column is **device**, the device you want to mount, it could be linux-style like `/dev/sda1` or specified by label `LABEL=` or UUID `UUID=`.
    * The second column is **mount point**, the directory you want to mount the device to.
    * **Filesystem type** specifies the type of the filesystem, `ext4` or `ntfs` or something else. Usually you can set it to `auto` to allow system to automatically detect it.
    * **Mount options** modifies how kernel treats this filesystem. For example `ro` means this filesystem is mounted read-only, and `auto` means to automatically mount it on startup. Separate the options by comma. For more details refer to `man mount`.
    * **Backup operation** specifies if the `dump` utility should back it up. Usually set to 0.
    * **Filesystem check order** 0 means `fsck` should not check it. Higher numbers means check order. The root `/` should have order 1 and others should have 2.
* To unmount a system, use command `umount`. The options are similar to `mount`, and the operand can be either the device or the mount point.
* A user cannot usually unmount a filesystem, unless it is specified in `/etc/fstab` that `user`, `users` or `owner` option is on. For details refer to `man mount`.
* If you cannot unmount a filesystem because it is busy, use `lsof` and `fuser` to find the process using it and terminate the process.

## Maintain Filesystem

* To format a device (like `/dev/sda6`) into a specified filesystem (like `ext4`), use command `mkfs -t ext4 /dev/sda6`. You can use `-c` option to perform a bad-block check before formating.
* To check a filesystem (like `/dev/sda6`) for errors, use `fsck /dev/sda6`. Note that only run `fsck` on filesystems **not mounted**.
* To get the information of a filesystem of type `ext`, use command `dumpe2fs /dev/sda6`. Then you can adjust the parameters by `tune2fs`. Note that you cannot adjust the filesystem mounted, and if you want to tune the root filesystem, you may need to boot into another disk system.
* Use `debugfs` to debug a filesystem. You will get an interactive command-line interface with prompt `debugfs:`. You can display inode information by `stat filename`, you can undelete a file by `undel inode filename`, you can extract a file from the system to your main linux system by `write internal-file external-file`, you can obtain help by `list_requests` `lr` `help` or `?`. To quit just command `quit`. This tool is very powerful and could save data from a damaged filesystem. Do not use it on a mounted filesystem.

## Swap Space

* Linux use a swap partition as an extension of memory.
* To turn a device into swap partition, use `mkswap /dev/sda6`. Then use `swapon /dev/sda6` to use it and `swapoff /dev/sda6` to deactivate it.
* If you run out of swap space, you can turn a file into swap file using `mkswap` without adjusting partitions. You can use `swapon` and `swapoff` on a swap file.

## ISO System

* You can create optical disk file `.iso` file by `mkisofs` or `genisoimage`. The usage is `genisoimage -o output.iso directory` to compile all files in the directory into the `.iso` file.

## Manage Devices Using `udev`

* `udev` control device files in the `/dev` tree.
* When a Linux kernel boots, the kernel scans the hardware to see what is available, and `udev` subsystem create entries in `/dev` for most hardware devices.
* Most devices are **character** devices, which you can read and write in byte flow, like mice and consoles.
* Some devices are **block** devices, which you must read and write in chunks, like disk.
* The `/dev/udev/rules.d` contains `udev` rule files, named `##-blah-blah.rules`, where `##` is the sequence number which controls the order these rules are executed when system boots or a device is attached or detached from computer.
* To learn to control `udev` system, first learn to use the `udevadm` command, and its subcommands. The most commonly used subcommand is `info`. The basic usage is like
```bash
$ udevadm info -q path -n /dev/input/mouse0
```
* Here the `-q` specifies what type of query you want to make, and `-n` is which device you want to query. To see all the query types use `udevadm info -h`.
* Rules in a `udev` rules file is a list of comma-separated key-value pairs. Each key and value is connected by an operator. If the operator is for comparison, it must be satisfied for this rule to take effect. If the operator is assignment, it will be executed if this rule takes effect.
* The comparison operators include `==` and `!=`, and assignment operators include `=`, `+=`, `-=` and `:=`. The right side of the match operator can be wildcards and could contain `[abc]` like regular expression.
* Some assignment is considered as **control** expression affecting the match of the rule. For example `WAIT_FOR="state"` causes matching to be paused (following rules will not be executed normally) until a file `state` is created.
* The following rule means when a memory device is added, set the `state` attribute to be `online`, and the matching is paused in this line until a file `state` is created.
```
ACTION=="add", KERNEL=="memory[0-9]*", WAIT_FOR="state", ATTR{state}="online"
```
* To make sure your network card has the same name everytime it boots, the rule like following works
```
SUBSYSTEM=="net", ACTION=="add", ATTR{address}=="00:03:47:b1:e3:d8", KERNEL=="eth*", NAME="eth0"
```
* The following rule causes the `udev` to create a symlink `/dev/scan5400` pointing to the device and set the ownership, group and permission code of the device.
```
ATTR{idVendor}=="0686", ATTR{idProduct}=="400e", SYMLINK+="scan5400" MODE="0660", OWNER="lisa", GROUP="scanner"
```
* For more labels refer to `man udev`.
* You can add your own rules file in the `/etc/udev/rules.d` directory. Avoid modifying the origin rules file because they might be changed back by system update.
* Once you save your rules file, they take effect immediately. However you may need to unplug and plug in your device again for the rules to apply on it.
* To see what `udev` does when you plug in a usb device, start `udevadm monitor`.
