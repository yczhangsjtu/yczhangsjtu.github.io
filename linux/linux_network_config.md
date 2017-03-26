---
title: Linux Network Configuration
---

# Linux Network Configuration

## Basic Network Configuration

* If you are using WLAN, before configuration, use `iwlist` tool to scan for available local networks. The command is `iwlist scan`. If you know the name of the network device of your computer, for example `wlan0`, you can preceed the `scan` by it optionally, i.e. `iwlist wlan0 scan`.
* This command is the command-line version of clicking the wireless icon in GUI, and seeing a list of wifi networks. The `ESSID` item is the same to the name of the network.
* You can replace `scan` by other subcommands, for details refer to `man iwlist`.
* The following command tries to connect to the network named `NoWires` on channel 1 with password `N1mP7mHNw`. Usually the password is a string of hexadecimal values with optional dashes between two-bytes blocks like `01a8-6567-4445`. To use an ascii string as password, preceed with `s:` like in the example.
```bash
$ iwconfig wlan0 essid NoWires channel 1 mode Managed key s:N1mP7mHNw
```
* To check the settings of a wireless network interface `wlan0`, use `iwconfig wlan0`.
* To set the IP address and netmask, use `ifconfig` command. For example
```bash
ifconfig eth0 up 192.168.29.39 netmask 255.255.255.0
```
* To set the router gateway, use command `route`, for example
```bash
route add default gw 192.168.29.1
```
* To check the current configuration of network, use `ifconfig` or `route` without any arguments.

## Additional Network Options

* Linux can be configured to be a router. To enable this future, use
```bash
echo "1" > /proc/sys/net/ipv4/ip_forward
```
* Permanently setting this option requires modifying a configuration file, some in the `/etc/sysctl.conf`, with
```
net.ipv4.ip_forward=1
```
* The `arp` command shows the current ARP cache. It can also be used to modify the cache.
* The `ip` command is a sort of Swiss Army knife of network commands. The syntax is
```
ip [options] object { command | help }
```
* The `object` is one of a specific set supported by `ip` including `link` and `addr`, etc. For different `object` there are different `command`. Refer to `man ip` for details. An example is
```
ip route list
```

## Monitor Network Traffic

* The `netstat` utility is a powerful tool to monitor the network state of your computer. The `-i` and `-r` options give information like `ifconfig` and `route` respectively. The `-p` option gives all programs making network connections. For details refer to `man netstat`.
* The `tcpdump` utility is a packet filter. Simple `tcpdump` command displays simple packet information. To write packets to files use `-w file` option. For details refer to `man tcpdump`.

