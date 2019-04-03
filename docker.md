# Docker

## 容器

`docker run`

`docker run -it ubuntu /bin/bash`

> -t:在新容器内指定一个伪终端或终端  
> -i:允许你对容器内的标准输入 (STDIN) 进行交互  

`docker ps`

> 查看在运行的容器

`docker logs [ID]`

> 查看容器内的标准输出

`docker stop [ID]`

> 停止容器

`docker run -d -v finder:/MountPoint -P training/webapp python app.py`

> -d:让容器在后台运行  
> -P:将容器内部使用的网络端口映射到我们使用的主机上  
> -v:挂载文件或文件夹

`docker run -d -p 5000:5000/tcp training/webapp python app.py`

> -p:标识绑定指定端口

`docker port [ID]`

> 查看指定 （ID或者名字）容器的某个确定端口映射到宿主机的端口号

`docker logs -f [ID]`

> 实时查看容器内部的标准输出

`docker top [ID]`

> 查看容器内部运行的进程

`docker inspect [ID]`

> 返回一个 JSON 文件记录着 Docker 容器的配置和状态信息

`docker start [ID]`

> 已经停止的容器，我们可以使用命令 docker start 来启动

`docker ps -l`

> 查询最后一次创建的容器

`docker restart`

> 正在运行的容器，我们可以使用 docker restart 命令来重启

`docker rm [ID]`

> 删除不需要的容器 删除容器时，容器必须是停止状态，否则会报错误

`docker rmi [ID]`

> 删除镜像

`docker exec -it zen_swirles/[ID] /bin/bash /[sh]`

> 进入现有容器

## 镜像

`docker images [something]`

> 列出本地主机上的镜像  
> REPOSITORY：表示镜像的仓库源  
> TAG：镜像的标签  
> IMAGE ID：镜像ID  
> CREATED：镜像创建时间  
> SIZE：镜像大小

`docker pull`

> 拉镜像

`docker search [something]`

> 搜索镜像  
> NAME:镜像仓库源的名称  
> DESCRIPTION:镜像的描述  
> OFFICIAL:是否docker官方发布

`docker commit -m="has update" -a="runoob" e218edb10161 runoob/ubuntu:v2`

> -m:提交的描述信息  
> -a:指定镜像作者  
> e218edb10161：容器ID  
> runoob/ubuntu:v2:指定要创建的目标镜像名

`docker build`

> 构建镜像

```
runoob@runoob:~$ cat Dockerfile 
FROM    centos:6.7
MAINTAINER      Fisher "fisher@sudops.com"

RUN     /bin/echo 'root:123456' |chpasswd
RUN     useradd runoob
RUN     /bin/echo 'runoob:123456' |chpasswd
RUN     /bin/echo -e "LANG=\"en_US.UTF-8\"" >/etc/default/local
EXPOSE  22
EXPOSE  80
CMD     /usr/sbin/sshd -D
```

`docker build -t runoob/centos:6.7 .`

> -t ：指定要创建的目标镜像名  
> . ：Dockerfile 文件所在目录，可以指定Dockerfile 的绝对路径

`docker tag 860c279d2fec runoob/centos:dev`

> 为镜像添加一个新的标签

`docker stop $(docker ps -a -q)`

`docker rm $(docker ps -a -q)`

`docker export 7691a814370e > ubuntu1404.tar`

> 导出镜像

`docker load < /home/fengzheng/Docker/ubtuntu12.04.tar`

> 导入镜像  
> 删除镜像前，要先把依赖于这个镜像的容器删除 `docker rm [ID]`

`docker build -t nginx:v3 .`

> 构建镜像 上下文路径为`.`

# IMAGES

## DVWA

### LINK

[https://hub.docker.com/r/vulnerables/web-dvwa/](https://hub.docker.com/r/vulnerables/web-dvwa/)

### INSTALL

`docker pull vulnerables/web-dvwa`

### START

`docker run --rm -it -p 8888:80 vulnerables/web-dvwa`

### LOGIN WITH DEFAULT CREDENTIALS

USERNAME: admin  
PASSWORD: password  

## docker-compose

#### Compile environment

`docker-compose build`

#### Run environment

`docker-compose up -d`

#### Stop environment

`docker-compose down -v`