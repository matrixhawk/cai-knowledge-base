# Docker Tips

# Docker 命令大全

[Docker 命令大全](http://www.runoob.com/docker/docker-command-manual.html)

## 容器生命周期管理

```
run
start/stop/restart
kill
rm
pause/unpause
create
exec
```

## 容器操作

```
ps
inspect
top
attach
events
logs
wait
export
port
```

## 容器rootfs命令

```
commit
cp
diff
```

## 镜像仓库

```
login
pull
push
search
```

## 本地镜像管理

```
images
rmi
tag
build
history
save
import
info|version
info
version
```

# Docker Documentation

[Docker Documentation](https://docs.docker.com/)

# Docker Hub

[Docker Hub](https://hub.docker.com/explore/)

# macOS install Docker

[Mac 上 Docker 的安装和使用初探](http://blog.devzeng.com/blog/using-docker-on-macos.html)
[macOS 安装 Docker](https://yeasy.gitbooks.io/docker_practice/content/install/mac.html)

# MySQL in Docker

## 运行 Docker 中的 MySQL 容器脚本

```
docker run -d -p 127.0.0.1:3306:3306 \
--name mysql \
-v /Users/zicongcai/04-Dev/docker/mysql/data:/var/lib/mysql \
-e MYSQL_ROOT_PASSWORD="123zxc" mysql:latest
```

**使用宿主机 IP 才能用它来远程访问：**

```
docker run -d -p 192.168.1.186:3306:3306 \
--name mysql \
-v /Users/zicongcai/04-Dev/docker/mysql/data:/var/lib/mysql \
-e MYSQL_ROOT_PASSWORD="123zxc" mysql:latest
```

查看 Docker 中的 MySQL 容器映射到的宿主 IP：
`docker port mysql 3306`

腾讯云 Docker 中的 MySQL 容器：

```
docker run -d -p 127.0.0.1:3306:3306 --name mysql -v /usr/docker/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD="123zxc" mysql:8.0
```

## 安装 MySQL

[Mac 上 Docker 的安装和使用初探](http://blog.devzeng.com/blog/using-docker-on-macos.html)
[mysql 镜像使用](https://yeasy.gitbooks.io/docker_practice/content/appendix/repo/mysql.html)

### 下载MySQL镜像

```
docker pull mysql:8.0
```

### 使用镜像创建并启动容器

Docker以镜像为基础创建启动容器的方式为：

```
docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
```

启动成功后会在终端输出容器的ID，启动MySQL容器的脚本为：

```
docker run -d -p 127.0.0.1:3306:3306 \
			--name mysql \
			-v /Users/zengjing/docker/mysql/data:/var/lib/mysql \
			-e MYSQL_ROOT_PASSWORD=”123456” mysql:latest
```
可以通过 `docker ps -a` 查看容器运行的情况。

参数说明：

* `-d` 表示容器将以后台模式运行，所有I/O数据只能通过网络资源或者共享卷组来进行交互。
* `-p 127.0.0.1:3306:3306` 将主机（127.0.0.1）的端口3306映射到容器的端口3306中。这样访问主机中的3306端口就等于访问容器中的3306端口。
* `--name mysql`给容器取名为 mysql，这样方便识别。
* `-v /Users/zengjing/docker/mysql/data:/var/lib/mysql` 将本机的文件目录挂载到容器对应的目录（`/var/lib/mysql`）中。这样可以通过数据卷实现容器中数据的持久化。
* `-e MYSQL_ROOT_PASSWORD="123456"`, -e表示设置环境变量，此处设置了mysql的root 用户的访问密码为 123456。
* `mysql:latest` 表示使用 mysql 的最新镜像启动一个容器。

### 使用MySQL

完成上面的步骤之后就可以使用MySQL的客户端工具使用了,连接信息如下：

```
Host: 127.0.0.1
Port: 3306
UserName: root
Password: 123456
```

## use MySQL Workbench in macOS

[Download MySQL Workbench](https://dev.mysql.com/downloads/workbench/)
[Mac 下安装 mysql 服务及基于 workbench 的使用方法](https://my.oschina.net/u/2391658/blog/716741)

## 连接到 Docker 容器中的 MySQL console

[DOCKER – HOW TO CONNECT TO A MYSQL RUNNING CONTAINER](https://littletechblogger.wordpress.com/2016/02/26/docker-how-to-connect-to-a-mysql-running-container-using-mysql-command-line-client/)

Bash into the running container and run MySQL client.

1. Bash into the running MySQL container:

```
$ docker exec -t -i <container_name> /bin/bash
```

2. Run MySQL client:

```
$ mysql -u “<useranme>” -p
```

## 开启 CentOS 中 MySQL 的远程连接权限

[CentOS下开启mysql远程连接，远程管理数据库](http://www.fantxi.com/blog/archives/enable-remote-access-mysql-centos/)

```
$ mysql -u root -p mysql
// 第1个mysql是执行命令，第2个mysql是系统数据名称
```

* 在 mysql 控制台执行:

```
mysql> grant all privileges on *.* to 'root'@'%' identified by '123456' with grant option;
// root是用户名，%代表任意主机，'123456'指定的登录密码（这个和本地的root密码可以设置不同的，互不影响）
mysql> flush privileges;
// 重载系统权限
mysql> exit;
```

* 添加防火墙规则，允许 3306 端口

```
$ iptables -I INPUT -p tcp -m state --state NEW -m tcp --dport 3306 -j ACCEPT
// 查看规则是否生效
$ iptables -L -n
// 或者
$ service iptables status
```

```
// 此时生产环境是不安全的，远程管理之后应该关闭端口，删除之前添加的规则
$ iptables -D INPUT -p tcp -m state --state NEW -m tcp --dport 3306 -j ACCEPT
```

另外，上面为 iptables 添加/删除规则都是临时的，如果需要重启后也生效，需要保存修改：

```
$ service iptables save
// 或者
$ /etc/init.d/iptables save
```

另一种方法是：

```
$ vi /etc/sysconfig/iptables
// 加上下面这行规则也是可以的
-A INPUT -p tcp -m state --state NEW -m tcp --dport 3306 -j ACCEPT
```

> 可部分参考：[远程连接腾讯云Centos系统的MySQL数据库](http://linux.it.net.cn/CentOS/course/2016/0530/22995.html)

# Dockerfile

[使用 Dockerfile 构建 Docker 镜像](http://blog.devzeng.com/blog/build-docker-image-with-dockerfile.html)
[docker 构建 java、tomcat、mysql 生产环境镜像](http://kaimingwan.com/post/rong-qi-yu-rong-qi-yun/dockergou-jian-java-tomcat-mysqlsheng-chan-huan-jing-jing-xiang)

# JDK & Tomcat in Docker

[在 docker 中制作自己的 JDK + tomcat 镜像](http://blog.csdn.net/smile326/article/details/51447757)（已保存到印象笔记）

# Trouble Shooting

```
docker run -d -p 127.0.0.1:23306:3306 --name mysql3 -e MYSQL_ROOT_PASSWORD="123zxc" mysql:8.0

docker exec -it mysql2 /bin/bash

mysql -uroot -p

GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION;

CREATE USER 'game'@'localhost' IDENTIFIED BY '123zxc';

GRANT ALL PRIVILEGES ON *.* TO 'game'@'localhost' WITH GRANT OPTION;

CREATE USER 'game'@'%' IDENTIFIED BY '123zxc';

GRANT ALL PRIVILEGES ON *.* TO 'game'@'%' WITH GRANT OPTION;

select user,host from mysql.user;
```

---

change log: 

	- 创建（2017-09-11）
	- 更新（2017-11-08）

---

