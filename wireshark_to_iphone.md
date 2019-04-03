mac系统版本：mac 10.10 Yosemite
xcode版本：6.3.1

在追踪bug或者分析借鉴其他公司的app通讯思路时，抓包这件事是非常有必要的。下面说说Wireshark怎么截获iphone的数据包。

## 安装wireshark

wireshark是依赖x11的，所以首先确认安装了x11，mac自带，可以打开升级一下。
前往－实用工具－x11，打开后点击菜单栏上的x11，检查更新 即可。中间提取包内容过程比较长，耐心等待。

下载Wireshark最新版，尽量去官网下载：
https://www.wireshark.org/download.html （需要翻..）

安装，安装过程很简单，一路下一步。

我这里下载的Wireshark 1.12.4 Intel 64，安装后无法运行，网上说x11位置不对。控制台执行：
sudo ln -s /opt/X11 /usr/X11
问题依旧。

没办法下载了一个XQuartz-2.7.7：
http://xquartz.macosforge.org/landing/
安装，运行Wireshark。点完wireshark图标等了10多分钟，终于算是打开了，之后再打开就不需要等了。

## 抓iphone数据

想抓iphone的数据，首先需要让iphone数据通过mac才行。看到网上很多设代理什么的方法，比较复杂，有的还要越狱。其实没必要。只要链上数据线，然后在mac的终端执行：
rvictl -s iphone设备id
这时，所有iphone网络流量都会经过iphone所链接的mac，并且iphone数据还是走自己的网络，比如iphone链接在3g网络，数据还是会通过3G收发，而不是通过mac的网络。断开连接：

`rvictl -x iphone设备id`

设备连接后，mac会出现一个对应的虚拟网络接口，名字是rvi0（如果有多个iphone则累加，rvi1，rvi2…）
只要启动Wireshark，监听rvi接口就能抓到iPhone数据了，当然，也可以使用Wireshark以外的工具抓取或分析。
关于获取iphone设备ID，可以使用xcode-windows-devices，选择相应设备，右面设备信息的identifier里就是了