# Fortify Error

## not enough memory

通常报错下面伴有`[Error]:Unable to load build session with ID "xxx". See log file for more details`

猜测是内存不足引起的

`fortify`运行时默认以32位方式运行，且未指定内存用量，可能引起上面的报错。

解决方法：

扫描时指定扫描参数

```
"-64"
"-Xmx8G" [根据你的内存来设置，也别忘了设置主机的虚拟内存，m为兆G为千兆]
"-encoding"
"UTF-8"     [如果有中文最好指定编码方式]
```

> 兆用`-Xmx700m`

## ildasm

```
Unable to locate the Microsoft .NET Disassembler tool (ildasm)
```

首先应安装`Visula Studio`, 如果报错应在`fortify\Core\config\fortify-sca-properties`这个配置文件中加入`ildasm`的全路径，如下

`com.fortify.sca.IldasmPath=c:\\Program Files\ (x86)\\Microsoft\ SDKs\\Windows\\v8.1A\\bin\\NETFX\ 4.5.1\ Tools\\ildasm.exe`