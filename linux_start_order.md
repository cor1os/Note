# Linux Start Order

`通电` ==> `BIOS` ==> `主引导记录` ==> `操作系统`

`操作系统` ==> `/boot(内核)` ==> `/sbin/init` ==> **`/etc/inittab`** ==> `启动内核模块` ==> `/etc/rc.d/rc.sysinit` ==> `/etc/rc[0-6].d/` ==> `/etc/init.d/` ==> **`/etc/rc.d/rc.local`(`/etc/rc.local`)** ==> `/sbin/mingetty /bin/login` ==> `用户登录` ==> `Login shell` ==> `Non-Login shell`

## /etc/inittab

`id:runlevels:action:process`

action：表示对应登记项的process在一定条件下所要执行的动作。

具体动作有：

```
initdefault：设定默认的运行级别，即我们开机之后默认进入的运行级别，不能是0,6，你懂的

respawn：当process终止后马上启动一个新的
wait：当进入指定的runlevels后process才会启动一次，并且到离开这个runlevels终止
sysinit：系统初始化，只有系统开机或重新启动的时候，这个process才会被执行一次
powerwait：当init接收到电源失败信号的时候执行相应的process，并且如果init有进程在运行，会等待这个进程完成之后，再执行相应的process
powerfail：当init接收到电源失败信号的时候执行相应的process，并且如果init有进程在运行，不会等待这个进程完成，它会直接执行相应的process
powerokwait：电源已经故障，但是在等待执行对应操作的时候突然来电了就执行对应的process
powerfailnow：当电源故障并且init被通知UPS电源已经快耗尽执行相对应的process
ctrlaltdel：当用户按下ctrl+alt+del这个组合键的时候执行对应的process
boot：只有在引导过程中，才执行该进程，但不等待该进程的结束；当该进程死亡时，也不重新启动该进程
bootwait：只有在引导过程中，才执行该进程，并等待进程的结束；当该进程死亡时，也不重新启动该进程
off：如果process正在运行，那么就发出一个警告信号，等待20秒后，再通过杀死信号强行终止该process。如果process并不存在那么就忽略该登记项
once：启动相应的进程，但不等待该进程结束便继续处理/etc/inittab文件中的下一个登记项；当该进程死亡时，init也不重新启动该进程
```

## /etc/rc.d/rc.sysinit

初始化 它做的工作非常多，包括设定`PATH`、设定网络配置(`/etc/sysconfig/network`)、启动swap分区、设定`/proc`等等

## 启动内核模块

依据`/etc/modules.conf`文件或`/etc/modules.d`目录下的文件来装载内核模块

## /etc/rc[0-6].d/

执行`/etc/rc.d/rc`和/`etc/rc.d/rcX.d`目录下的脚本。

`inittab`的运行级别为几 就运行下面对应的文件夹下的软连接

```
　　/etc/rc0.d
　　/etc/rc1.d
　　/etc/rc2.d
　　/etc/rc3.d
　　/etc/rc4.d
　　/etc/rc5.d
　　/etc/rc6.d
```

```
　　$ ls -l /etc/rc2.d
　　
　　README
　　S01motd -> ../init.d/motd
　　S13rpcbind -> ../init.d/rpcbind
　　S14nfs-common -> ../init.d/nfs-common
　　S16binfmt-support -> ../init.d/binfmt-support
　　S16rsyslog -> ../init.d/rsyslog
　　S16sudo -> ../init.d/sudo
　　S17apache2 -> ../init.d/apache2
　　S18acpid -> ../init.d/acpid
　　...
```

此目录保存的是指向`/etc/init.d/`下的服务 从01开始一次执行  
除`README`外 其他的命名方式为`字母(启动参数 启动还是关闭)+两位数字(启动顺序)+服务名称`

## /etc/rc.d/rc.local

执行用户自定义启动脚本。你可以把你想设置和启动的东西放到这里。

## 用户登录

1 命令行登录：

`init`进程调用`getty`程序（意为get teletype），让用户输入用户名和密码。输入完成后，再调用`login`程序，核对密码（Debian还会再多运行一个身份核对程序`/etc/pam.d/login`）。如果密码正确，就从文件`/etc/passwd`读取该用户指定的shell，然后启动这个shell。

2 ssh登录：

这时系统调用sshd程序（Debian还会再运行`/etc/pam.d/ssh`），取代`getty`和`login`，然后启动shell。

3 图形界面登录：

`init`进程调用显示管理器，Gnome图形界面对应的显示管理器为gdm（GNOME Display Manager），然后用户输入用户名和密码。如果密码正确，就读取`/etc/gdm3/Xsession`，启动用户的会话。

## Login shell

1 命令行登录

首先读入`/etc/profile`这是对所有用户都有效的配置；然后依次寻找下面三个文件，这是针对当前用户的配置。

```
　　~/.bash_profile
　　~/.bash_login
　　~/.profile
```

需要注意的是，这三个文件只要有一个存在，就不再读入后面的文件了。比如，要是 `~/.bash_profile`存在，就不会再读入后面两个文件了。

2 ssh登录(同1)

3 图形界面登录

只加载`/etc/profile`和`~/.profile`。也就是说，`~/.bash_profile`不管有没有，都不会运行。