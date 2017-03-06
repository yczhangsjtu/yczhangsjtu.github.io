---
title: Tips for Linux
---

# Some Small Tips for Linux

## Ubuntu

*   When the time is missing in the top bar

    $ pkill -f indicator-datetime-service

## Linux

*   Auto mount some partition of the disk

		$ cat >> /etc/fstab
    /dev/sda2       /home/username/misc      ext4        noatime     0       2
    /dev/sda3       /home/username/data      ntfs        noatime     0       2
    /dev/sda5       /home/username/file      ntfs        noatime     0       2
    ^Z
    $

*   Install standalone flash player

		wget https://fpdownload.macromedia.com/pub/flashplayer/updaters/11/flashplayer_11_sa.i386.tar.gz
    sudo apt-get install libnss3:i386
    sudo apt-get install libgtk2.0-0:i386
    sudo apt-get install gtk2-engines-murrine:i386
    sudo apt-get install libcanberra-gtk-module:i386

## Text

*   Transform binary into hex or vice versa

		$ cat input | xxd -p > output

		$ cat input | xxd -p -r > output

## Vim

*   Paste something in vim without messing out.

		:set paste
    :set nopaste

Contact GitHub API Training Shop Blog About
