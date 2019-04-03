#ShadowBrokers Tool Pakges
---

`Explodingcan (unsupport)` IIS漏洞利用工具,只对Windows 2003有影响  
`Eternalromance` SMB 和 NBT漏洞利用工具，影响端口139和445  
`Emphasismine` 通过IMAP漏洞攻击，攻击的默认端口为143  
`Englishmansdentist (unsupport)`通过SMTP漏洞攻击，默认端口25  
`Erraticgopher` 通过RPC漏洞攻击，端口为445  
`Eskimoroll` 通过kerberos漏洞进行攻击，默认攻击端口88  
`Eclipsedwing` MS08-67漏洞利用工具  
`Educatedscholar` MS09-050漏洞利用工具  
`Emeraldthread` SMB和 Netbios 漏洞利用工具，使用445端口和 139端口  
`Zippybeer` SMTP漏洞利用工具，默认端口 445  
`Eternalsynergy` SMB漏洞利用工具，默认端口 445  
`Esteemaudit (unsupport)` RDP漏洞利用工具，默认攻击端口为3389  

--

135：  135端口主要用于使用RPC（Remote Procedure Call，远程过程调用）协议并提供DCOM（分布式组件对象模型）服务，通过RPC可以保证在一台计算机上运行的程序可以顺利地执行远程计算机上的代码；使用DCOM可以通过网络直接进行通信，能够跨包括HTTP协议在内的多种网络传输。  
137：  137端口的主要作用是在局域网中提供计算机的名字或IP地址查询服务，一般安装了NetBIOS协议后，该端口会自动处于开放状态。137端口属于UDP端口，使用者只需要向局域网或互联网上的某台计算机的137端口发送一个请求，就可以获取该计算机的名称、注册用户名，以及是否安装主域控制器、IIS是否正在运行等信息。  
139：  139 NetBIOS File and Print Sharing 通过这个端口进入的连接试图获得NetBIOS/SMB服务。这个协议被用于Windows"文件和打印机共享"和SAMBA。在Internet上共享自己的硬盘是可能是最常见的问题。  
445：  445端口也是一种TCP端口，该端口在windows 20XX Server系统中发挥的作用与139端口是完全相同的。具体地说，它也是提供局域网中文件或打印机共享服务。不过该端口是基于CIFS协议（通用因特网文件系统协议）工作的，而139端口是基于SMB协议（服务器协议族）对外提供共享服务。同样地，攻击者与445端口建立请求连接，也能获得指定局域网内的各种共享信息。  
3389：  3389端口是Windows 20xx Server远程桌面的服务端口，可以通过这个端口，用"远程桌面"等连接工具来连接到远程的服务器，如果连接上了，输入系统管理员的用户名和密码后，将变得可以像操作本机一样操作远程的电脑，因此远程服务器一般都将这个端口修改数值或者关闭。  

--
`Architouch`  `Darkpulsar`  `Domaintouch`  

```
Esteemaudit(use) ==>
set MigrateProcessDLL: ShadowBrokers\windows\storage\rudo_x86.dll ==>
set CallbackPayloadDLL: ShadowBrokers\windows\storage\capa_x86.dll ==>
set ListenPayDLL: ShadowBrokers\windows\storage\lipa_x86.dll(lipa_x64.dll) ==>
Pcdlllauncher(use)==>
set LPFilename: ShadowBrokers\windows\Resources\Pc\Legacy\PC_Exploit.dll
(ImplantFilename=payload)
```

`Eternalblue ==> Doublepulsar  (DLLPayload = payload)`  
`Emeraldthread`  
`Eternalchampion`  `Educatedscholartouch`  `Educatedscholar`  
`Esteemaudittouch`  
`Emeraldthreadtouch`  
`Eternalromance`  
`Emphasismine`  
`Eternalsynergy`  
`Englishmansdentist`  
`Ewokfrenzy`  
`Erraticgopher`  
`Explodingcan`  `Easybee`  `Easypi`  
`Erraticgophertouch`  
`Explodingcantouch`  `Eclipsedwing`  
`Eskimoroll`  `Eclipsedwingtouch`  
`Iistouch`  
`Jobdelete`  
`Jobadd`  
`Joblist`  
`Mofconfig`  
`Namedpipetouch`  
`Printjobdelete`  
`Printjoblist`  
`Processlist`  
`Regdelete`  
`Rpcproxy`  
`Rpctouch`  
`Regenum`  
`Regread`  
`Regwrite`  
`Smbdelete`  
`Smblist`  
`Smbread`  
`Smbtouch`  
`Smbwrite`  
`Webadmintouch`  
`Worldclienttouch`  
`Zippybeer`  
