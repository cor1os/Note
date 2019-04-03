#### 1

确保你的android设备真正链接到电脑上了，我在这里遇到过坑，弄了好久，才发现能充电的线，确无法传递数据过去。所以不要以为随便拿一根线，能充电，就可以传递数据了，我就是这么傻傻的拿了根不能用的数据线联机调试了半天。

> 方法：下载一个 androidfiletransfer.dmg,安装之后，看看能不能读取手机数据，如果能够读取，好的，恭喜你，第一步完成了。

#### 2

设置好你机器的环境变量

#### 3

第一步： 查看usb设备信息（我用的是魅族mx3）

在终端输入：system_profiler SPUSBDataType

可以查看连接的usb设备的信息

比如我的usb信息如下（部分内容）：

 M351:  
              Product ID: 0x4e26  
              Vendor ID: 0x18d1  (Google Inc.)  
              Version:  2.33  
              Serial Number: 351BBJHCBWT6  
              Speed: Up to 480 Mb/sec  
              Manufacturer: MEIZU  
              Location ID: 0x1a120000 / 4  
              Current Available (mA): 500  
              Current Required (mA): 2

其中的 vendor ID: 0x18d1 很重要，记下来

第二步： 创建、修改adb_usb.ini文件

输入： vi ~/.android/adb_usb.ini 命令，在打开的 adb_usb.ini文件中添加0x18d1， （然后保存退出）

然后请一定重启finder ：鼠标单击窗口左上角的苹果标志-->强制退出-->Finder-->重新启动

