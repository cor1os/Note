#JS加密的三种方法
##base64

```  
<!DOCTYPE HTML>
<html>
<head>
<meta charset="utf-8">
<title>base64加密</title>
<script type="text/javascript" src="base64.js"></script>
<script type="text/javascript">  
        var b = new Base64();  
        var str = b.encode("admin:admin");  
        alert("base64 encode:" + str);  
　　　　　//解密
        str = b.decode(str);  
        alert("base64 decode:" + str);  
</script>  
</head>

<body>
</body>
</html>
```  

##md5

```  
<!DOCTYPE HTML>
<html>
<head>
<meta charset="utf-8">
<title>md5加密</title>
<script type="text/ecmascript" src="md5.js"></script>
<script type="text/javascript">  
  var hash = hex_md5("123dafd");
    alert(hash)
</script>  
</head>

<body>
</body>
</html>
```  

##sha1

```  
<!DOCTYPE HTML>
<html>
<head>
<meta charset="utf-8">
<title>sha1加密</title>
<script type="text/ecmascript" src="sha1.js"></script>
<script type="text/javascript">
  var sha = hex_sha1('mima123465')
    alert(sha)   
</script>  
</head>

<body>
</body>
</html>
```  
