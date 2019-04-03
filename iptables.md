# IPTABLES

---

## 0x00 模块

### iprange

`--src-range` 指定连续的源地址范围  
`--dst-range` 指定连续的目的地址范围

`iptables -t filter -I INPUT -m iprange --src-range 192.168.1.127-192.168.1.146 -j DROP`  
`iptables -t filter -I OUTPUT -m iprange --dst-range 192.168.1.127-192.168.1.146 -j DROP`  
`iptables -t filter -I INPUT -m iprange ! --src-range 192.168.1.127-192.168.1.146 -j DROP`  

### string

`--lgo` 指定对应的匹配算法，可用算法`bm`,`kmp`，此选项为必须选项  
`--string` 指定需要匹配的字符串  

`iptables -t filter -I INPUT -p tcp --sport 80 -m string --algo bm --string "OOXX" -j REJECT`  
`iptables -t filter -I INPUT -p tcp --sport 80 -m string --algo kmp --string "OOXX" -j REJECT`  

### time

`--timestart` 用于指定时间范围的开始时间，不可取反  
`--timestop` 用于指定时间范围的结束时间，不可取反  
`--weekdays` 用于指定"星期几"，可取反  
`--monthdays` 用于指定"几号"，可取反  
`--datestart` 用于指定日期范围的开始时间，不可取反  
`--datestop` 用于指定日期范围的结束时间，不可取反  

`iptables -t filter -I OUTPUT -p tcp --dport 80 -m time --timestart 09:00:00 --timestop 19:00:00 -j REJECT`  
`iptables -t filter -I OUTPUT -p tcp --dport 443 -m time --timestart 09:00:00 --timestop 19:00:00 -j REJECT`  
`iptables -t filter -I OUTPIT -p tcp --dport 80 -m weekdays 6,7 -j REJECT`  
`iptables -t filter -I OUTPUT -p tcp --dport 80 -m monthdays 22,23 -j REJECT`  
`iptables -t filter -I OUTPUT -p tcp --dport 80 -m ! --monthdays 22,23 -j REJECT`  
`iptables -t filter -I OUTPUT -p tcp --dport 80 -m time --timestart 09:00:00 --timestop 18:00:00 --weekdays 6,7 -j REJECT`  
`iptables -t filter -I OUTPUT -p tcp --dport 80 -m time weekdays 5 --monthdays 22,23,24,25,26,27,28 -j REJECT`  
`iptables -t filter -I OUTPUT -p tcp --dport 80 -m time --datestart 2017-12-24 --datestop 2017-12-27 -j REJECT`  

### connlimit

`--connlimit-above` 单独使用此选项时，表示限制每个IP的连接数量。**(大于)**  
`--connlimit-mask` 此选项不能单独使用，在使用`--connlimit-above`选项时，配合此选项，则可以针对"某类IP段内一定数量的IP"进行连接数量的限制。

`iptables -I INPUT -p tcp --dport 22 -m connlimit --connlimit-above 2 -j REJECT`  
`iptables -I INPUT -p tcp --dport 22 -m connlimit --connlimit-above 20 --connlimit-mask 24 -j REJECT`  
`iptables -I INPUT -p tcp --dport 22 -m connlimit --connlimit-above 10 --connlimit-mask 27 -j REJECT`  

### limit

`--limit` 类比"令牌桶"算法，此选项用于指定令牌桶中生成新令牌的频率，可用时间单位有`second`,`minute`,`hour`,`day`  
`--limit-burst` 类比"令牌桶"算法，此选项用于指定令牌桶中令牌的最大数量。  

`iptables -t filter -I INPUT -p icmp -m limit --limit-burst 3 --limit 10/minute -j ACCEPT`  
`iptables -t filter -A INPUT -p icmp -j REJECT`  

### tcp

`--sport` 匹配tcp协议报文的源端口，可以使用冒号指定一个连续的端口范围  
`--dport` 匹配tcp协议报文的目的端口，可以使用冒号指定一个连续的端口范围  

`iptables -t filter -I OUTPUT -d 192.168.1.146 -p tcp -m tcp --sport 22 -j REJECT`  
`iptables -t filter -I OUTPUT -d 192.168.1.146 -p tcp -m tcp --sport 22:25 -j REJECT`  
`iptables -t filter -I OUTPUT -d 192.168.1.146 -p tcp -m tcp ! --sport 22 -j ACCPET`  

`iptables -t filter -I INPUT -s 192.168.1.146 -p tcp -m tcp --dport 22:25 -j REJECT`  
`iptables -t filter -I INPUT -s 192.168.1.146 -p tcp -m tcp --dport :22 -j REJECT`  
`iptables -t filter -I INPUT -s 192.168.1.146 -p tcp -m tcp --dport 80: -j REJECT`  

`--tcp-flag [flags,] [flags,]` 用于匹配报文的tcp头的标志位  

`iptables -t filter -I INPUT -p tcp -m tcp --dport 22 --tcp-flags SYN,ACK,FIN,RST,URG,PSH SYN -j REJECT`  
> 前面(空格)的flags中，除了后面的flags标志位为1，其他全为0  

`iptables -t filter -I OUTPUT -p tcp -m tcp --sport 22 --tcp-flags SYN,ACK,FIN,RST,URG,PSH SYN,ACK -j REJECT`  
`iptables -t filter -I INPUT -p tcp -m tcp --dport 22 --tcp-flags ALL SYN -j REJECT`  
`iptables -t filter -I OUTPUT -p tcp -m tcp --sport 22 --tcp-flags ALL SYN,ACK -j REJECT`  

`--syn` 用于匹配tcp新建连接的请求报文，相当于使用`--tcp-flags SYN,RST,ACK,FIN SYN`  

`iptables -t filter -I INPUT -p tcp -m tcp --dport 22 --syn -j REJECT`  

## DDOS && CC

### DOOS

```
#防止SYN攻击 轻量级预防   
iptables -N syn-flood   (如果您的防火墙默认配置有“ :syn-flood - [0:0] ”则不许要该项，因为重复了)
iptables -A INPUT -p tcp --syn -j syn-flood   
iptables -I syn-flood -p tcp -m limit --limit 3/s --limit-burst 6 -j RETURN   
iptables -A syn-flood -j REJECT   
#防止DOS太多连接进来,可以允许外网网卡每个IP最多15个初始连接,超过的丢弃   
iptables -A INPUT -i eth0 -p tcp --syn-m connlimit --connlimit-above 15 -j DROP   
iptables -A INPUT -p tcp-m state --state ESTABLISHED,RELATED -j ACCEPT  
  
#用Iptables抵御DDOS (参数与上相同)   
iptables -A INPUT  -p tcp --syn -m limit --limit 12/s --limit-burst 24 -j ACCEPT  
iptables -A FORWARD -p tcp --syn -m limit --limit 1/s -j ACCEPT  
```

> 作者：摘取天上星  
来源：CSDN  
原文：https://blog.csdn.net/zqtsx/article/details/9405515  
版权声明：本文为博主原创文章，转载请附上博文链接！ 

### CC

```
#控制单个IP的最大并发连接数
iptables -I INPUT -p tcp --dport 80 -m connlimit  --connlimit-above 50 -j REJECT  
##单个IP在60秒内只允许最多新建30个连接  
iptables -A INPUT -p tcp --dport 80 -m recent --name BAD_HTTP_ACCESS --update --seconds 60 --hitcount 30 -j REJECT  
iptables -A INPUT -p tcp --dport 80 -m recent --name BAD_HTTP_ACCESS --set -j ACCEPT  
```

```
#查看效果  
watch 'netstat -an | grep:21 | grep  <模拟攻击客户机的IP>| wc -l'  
#实时查看模拟攻击客户机建立起来的连接数  
#查看模拟攻击客户机被 DROP 的数据包数  
watch 'iptables -L -n -v | grep <模拟攻击客户机的IP>'
```

```
#为了增强iptables防止CC攻击的能力，最好调整一下ipt_recent的参数如下  
#cat/etc/modprobe.conf options ipt_recent ip_list_tot=1000 ip_pkt_list_tot=60  
```  

防cc脚本.sh  

```
############### KILL DDOS ##############
iptables_log="/data/logs/iptables_conf.log"
### Iptables 配置导出的路径，可任意修改 ###
########################################
status=`netstat -na|awk '$5 ~ /[0-9]+:[0-9]+/ {print $5}'|awk -F ":" -- '{print $1}' |sort -n|uniq -c |sort -n|tail -n 1|grep -v 127.0.0.1`
NUM=`echo $status|awk '{print $1}'`
IP=`echo $status|awk '{print $2}'`
result=`echo "$NUM > 200" | bc`
### 如果同时连接数大于 200 则干掉！###
if [[ $result = 1 ]]
then
echo IP:$IP is over $NUM, BAN IT!
/sbin/iptables -A INPUT -s $IP -j DROP
fi
########################################
iptables-save > ${iptables_log}
### 输出当前的 iptable 配置作为日志 ###
########################################
```