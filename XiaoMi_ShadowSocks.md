#如何让小米路由器聪明的使用shadowsocks
######January 21, 2016######
---

##1.怎么安装shadowsocks
简单说：不用安装  
复杂点说：  
1.确保小米路由器已经正常工作，并且当前使用的机器已经连接  
2.到 **小米路由器网站** ，切换到ROM tab， 下载开发版的ROM，我使用的是2.11.39(2.11.11也是一样的)  
3.打开路由器管理界面, 右上角的箭头->系统升级，然后上传刚才下载的XXX.bin文件, 确认更新  
4.安装完开发版的ROM之后其实就已经有了shadowsock  

##2.怎么启用shadowsocks
1.先到这里 拿到路由器ssh登录的root密码  
2.ssh到你的小米路由器，输入上一步获得的密码即可  

`ssh root@192.168.31.1`  

######PS. 如果想改密码的话passwd root就可以更改密码了######
3.看看shadowsocks在不在, shadowsocks客户端有三个可执行的命令ss-local, ss-redir, ss-tunnel，我们这里会用到ss-redir和ss-tunnel  

```  
which ss-redir
#会看到这样的输出：
#/usr/bin/ss-redir
which ss-tunnel
#输出：
#/usr/bin/ss-tunnel
```  

4.修改配置文件，/etc/shadowsocks.json, 输入如下内容：  

```
{
  "server":"SERVER", //这里写服务器地址，最好用ip    
  "server_port": 5555, //shadowsocks服务器的端口
  "local_address":"127.0.0.1",
  "local_port":8964, //本地shadows绑定的端口, 
  "password":"PASSWORD",//shdowsocks 密码
  "timeout":600, //不用改
  "method":"aes-256-cfb"//加密算法, 根据服务商要求填写
}
```

修改完成之后输入:wq  
5.添加管理脚本 vi /etc/init.d/myshadowsocks, 这里可能已经存在了/etc/init.d/shadowsocks, 不用管他，我们用myshadowsocks吧，内容如下：  

```
#!/bin/sh /etc/rc.common

. /lib/functions.sh

START=95

SS_REDIR_PID_FILE=/var/run/ss-redir.pid
SS_TUNNEL_PID_FILE=/var/run/ss-tunnel.pid
CONFIG=/etc/shadowsocks.json
DNS=8.8.8.8:53
TUNNEL_PORT=5353

start() {
    # Client Mode
    #service_start /usr/bin/ss-local -c $CONFIG -b 0.0.0.0 -f $SERVICE_PID_FILE
    # Proxy Mode
    service_start /usr/bin/ss-redir -c $CONFIG -b 0.0.0.0 -f $SS_REDIR_PID_FILE
    # Tunnel
    service_start /usr/bin/ss-tunnel -c $CONFIG -b 0.0.0.0 -u -l $TUNNEL_PORT -L $DNS -f $SS_TUNNEL_PID_FILE
}

stop() {
    # Client Mode
    #service_stop /usr/bin/ss-local
    # Proxy Mode
    service_stop /usr/bin/ss-redir
    # Tunnel
    service_stop /usr/bin/ss-tunnel
}
```

以上的脚本会启动两个服务，一个ss-redir代理，一个ss-tunnel, 这里完全用做DNS解析  
6.运行shadowsocks, 并且添加到开机启动:  

```
/etc/init.d/myshadowsocks enable //添加到开机器启动
/etc/init.d/myshadowsocks start //运行
```

7.有了shadowsocks, 这一步我们让她聪明一点，使用gfwlist, 该翻翻给，不该翻的时候不瞎翻, 打开firewall配置文件vi /etc/firewall.user, 在文件的末尾加入这两句:  

```
ipset -N gfwlist iphash
iptables -t nat -A PREROUTING -p tcp -m set --match-set gfwlist dst -j REDIRECT --to-port 8964 #这里的最后的数字要和第三步的local_port对应
```

然后重启firewall, /etc/init.d/firewall restart  
8.添加规则配置文件到/etc/dnsmasq.d/目录中，运行这个项目 中的gfwlist2dnsmasq.py文件(或者下载codebar同学已经生成好的)，把生成的dnsmasq_list.conf的文件上传到/etc/dnsmasq.d/目录, 之后重启dnsmasq  

```
/etc/init.d/dnsmasq restart
```

9.好了，现在访问一下https://www.google.com, 嘿嘿，大功告成了  

##3.参考资料：
`https://cokebar.info/archives/962`  
[Referer: ](http://liangpir.com/post/2016-01-21)`http://liangpir.com/post/2016-01-21`  
