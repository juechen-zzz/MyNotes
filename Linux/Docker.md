# Docker

## 一、常用命令

### 1.1 帮助命令

```shell
$ docket version		# 版本信息
$ docket info			# 系统信息，包括镜像和容器的数量
$ docket 命令 --help	# 万能命令
```

帮助文档的地址：https://docs.docker.com/engine/reference/commandline/docker/

### 1.2 镜像命令

**docker images**：查看所有本地主机上的镜像

```shell
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
nginx               latest              0901fa9da894        10 hours ago        132MB
hello-world         latest              bf756fb1ae65        6 months ago        13.3kB

# 解释
REPOSITORY	镜像的仓库源
TAG			镜像的标签
IMAGE ID	镜像的ID
CREATED		镜像的创建时间
SIZE		镜像的大小

# 可选项
  -a, --all             Show all images (default hides intermediate images)
      --digests         Show digests
  -f, --filter filter   Filter output based on conditions provided
      --format string   Pretty-print images using a Go template
      --no-trunc        Don't truncate output
  -q, --quiet           Only show numeric IDs（只显示 id）
```

**docker search**：搜索镜像

```shell
$ docker search mysql
NAME                              DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
mysql                             MySQL is a widely used, open-source relation…   9721                [OK]
mariadb                           MariaDB is a community-developed fork of MyS…   3544                [OK]
...

# 可选项
  -f, --filter filter   Filter output based on conditions provided
      --format string   Pretty-print search using a Go template
      --limit int       Max number of search results (default 25)
      --no-trunc        Don't truncate output

# 通过收藏来过滤
 --filter=STARS=3000	# 搜索出来的镜像就是 STARS 大于 3000 的
```

**docker pull**：下载镜像

```shell
# 下载镜像 docker pull 镜像名[:tag]
$ docker pull mysql	# 如果不写 tag，默认是 lastest
Using default tag: latest
latest: Pulling from library/mysql
8559a31e96f4: Already exists
d51ce1c2e575: Pull complete	# 分层下载，docker image 的核心，联合文件系统
c2344adc4858: Pull complete
fcf3ceff18fc: Pull complete
16da0c38dc5b: Pull complete
b905d1797e97: Pull complete
4b50d1c6b05c: Pull complete
c75914a65ca2: Pull complete
1ae8042bdd09: Pull complete
453ac13c00a3: Pull complete
9e680cd72f08: Pull complete
a6b5dc864b6c: Pull complete
Digest: sha256:8b7b328a7ff6de46ef96bcf83af048cb00a1c86282bfca0cb119c84568b4caf6	# 签名
Status: Downloaded newer image for mysql:latest
docker.io/library/mysql:latest	# 真实地址

# 两者是等价的
docker pull mysql
docker pull docker.io/library/mysql:latest

# 其他版本
$ docker pull mysql:5.7
5.7: Pulling from library/mysql
8559a31e96f4: Already exists	# 已经存在的就不需要再下载了
d51ce1c2e575: Already exists
c2344adc4858: Already exists
fcf3ceff18fc: Already exists
16da0c38dc5b: Already exists
b905d1797e97: Already exists
4b50d1c6b05c: Already exists
d85174a87144: Pull complete
a4ad33703fa8: Pull complete
f7a5433ce20d: Pull complete
3dcd2a278b4a: Pull complete
Digest: sha256:32f9d9a069f7a735e28fd44ea944d53c61f990ba71460c5c183e610854ca4854
Status: Downloaded newer image for mysql:5.7
docker.io/library/mysql:5.7
```

**docker rmi**：删除镜像

```shell
$ docker rmi -f 镜像id	# 删除指定的镜像
$ docker rmi -f 镜像id 镜像id 镜像id	# 删除多个镜像
$ docker rmi -f $(docker images -aq)	# 删除全部镜像
```

### 1.3 容器命令

**新建容器并启动**

```shell
$ docker run [可选参数] image

# 参数说明
--name="Name" 	容器名字，用来区分容器
-d				 后台方式运行
-it				 使用交互方式运行，进入容器查看内容
-p				 指定容器的端口 -p 8080:8080
	-p ip:主机端口:容器端口
	-p 主机端口:容器端口（常用）
	-p 容器端口
	容器端口
-P				 随机指定端口

# 测试，启动并进入容器
$ docker run -it centos /bin/bash
[root@a9e289aa02c5 /]# ls		# 查看容器内的 centos，基础版本，很多命令都是不完善的
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
[root@a9e289aa02c5 /]# exit		# 退出命令
exit
```

**列出所有运行的容器**

```shell
# docker ps 命令
		# 当前正在运行的容器
-a		# 列出当前正在运行的容器 + 历史运行过的容器
-n=?	# 显示最近创建的容器
-q		# 只显示容器的编号

$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                          PORTS               NAMES
a9e289aa02c5        centos              "/bin/bash"         3 minutes ago       Exited (0) About a minute ago                       awesome_liskov
3fc0e38d7509        centos              "/bin/zsh"          3 minutes ago       Created                                             dreamy_heyrovsky
036d21212dfa        bf756fb1ae65        "/hello"            3 hours ago         Exited (0) 3 hours ago                              sweet_mirzakhani
```

**删除容器**

```shell
$ docker rm 容器id		# 删除指定的容器，不能删除正在运行的容器，如果要前置删除 docker rm -f 容器id
$ docker rm -f $(docker ps -aq)	# 删除所有的容器
$ docker ps -a -q | xargs docker rm # 删除所有容器
```

**启动和停止容器**

```shell
$ docker start 容器id		# 启动容器
$ docker restart 容器id	# 重启容器
$ docker stop 容器id		# 停止当前正在运行的容器
$ docker kill 容器id		# 强制停止档期那容器
```

### 1.4 其他常用命令

**后台启动**

```shell
# 命令 docker run -d 镜像名
$ docker run -d centos

# 问题 docker ps，发现 centos 停止了

# 常见的坑，docker 容器使用后台运行，就必须要有一个前台进程，docker 发现没有应用，就会自动停止
# nginx，容器启动后，发现自己没有提供服务，就会立刻停止
```

**查看日志**

```shell
# 命令
$ docker logs

# 自己编写一段 shell 脚本
$ docker run -d centos /bin/sh -c "while true;do echo lonely7;sleep 1;done"

# 测试
$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
596d948ec297        centos              ....

# 显示日志
$ docker logs -tf --tail 10 596d948ec297

# 参数
-t			# 带有时间戳
-f			# 持续输出实时更新日志
--tail number		# 显示最后 number 条日志
```

**查看容器中的进程信息**

```shell
# 命令
$ docker top 容器id

# 测试
$ docker top 596d948ec297
PID                 USER                TIME                COMMAND
2601                root                0:00                /bin/sh -c while true;do echo lonely7;sleep 1;done
2959                root                0:00                {sleep} /usr/bin/coreutils --coreutils-prog-shebang=sleep /usr/bin/sleep 1
```

**进入当前正在运行的容器**

```shell
# 我们通常容器都是使用后台方式运行的，需要进入容器，修改一些配置

# 方式一（新开一个终端）
$ docker exec -it 容器id bashShell

# 测试
$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
596d948ec297        centos              "/bin/sh -c 'while t…"   11 minutes ago      Up 11 minutes                           sweet_hofstadter
$ docker exec -it 596d948ec297 /bin/bash
[root@596d948ec297 /]# ps -ef
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 22:00 ?        00:00:00 /bin/sh -c while true;do echo lonely7;sleep 1;done
root       717     0  0 22:12 pts/0    00:00:00 /bin/bash
root       737     1  0 22:12 ?        00:00:00 /usr/bin/coreutils --coreutils-prog-shebang=sleep /usr/bin/sleep 1
root       738   717  0 22:12 pts/0    00:00:00 ps -ef

# 方式二（进入之前运行脚本的终端）
$ docker attach 容器id

# 测试
$ docker attach 596d948ec297
lonely7
lonely7
...

# docker exec		# 进入容器后开启一个新的终端，可以在里面操作（常用）
# docker attach		# 进入容器正在执行的终端，不会启动新的进程
```

**拷贝文件**

```shell
# 命令
$ docker cp 容器id:容器路径 目的的主机路径

# 测试
# 查看正在运行的 docker 容器
$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
bb483fa194d9        centos              "/bin/bash"         12 minutes ago      Up 12 minutes                           gallant_leavitt
# 进入 docker 容器，并创建一个 java 文件
$ docker attach bb483fa194d9
[root@bb483fa194d9 /]# cd home/
[root@bb483fa194d9 home]# touch docker.java
[root@bb483fa194d9 home]# ls
docker.java
[root@bb483fa194d9 home]# exit
exit
# 查看所有 docker 容器
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                      PORTS               NAMES
bb483fa194d9        centos              "/bin/bash"         14 minutes ago      Exited (0) 31 seconds ago                       gallant_leavitt
# 把 bb483fa194d9 容器中 /home/docker.java 复制到当前文件夹
$ docker cp bb483fa194d9:/home/docker.java ./
$ ls
docker.java

# 拷贝是一个手动过程，之后可以使用 -v 卷的技术，实现自动同步
```

### 1.5 部署Nginx

```shell
# 1、搜索镜像 search （建议去 docker hub 搜索，可以看到版本信息）
# 2、下载镜像 pull
# 3、运行测试

$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
nginx               latest              0901fa9da894        26 hours ago        132MB
centos              latest              831691599b88        3 weeks ago         215MB
# -d 后台运行
# --name 给容器命名
# -p 宿主机端口:容器内部端口
$ docker run -d --name nginx01 -p 3344:80 nginx
f9bb25f3b02382dbfc82954f058d1e255c45d6ad0a8be17c5c561ccdf7d51279
$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                  NAMES
f9bb25f3b023        nginx               "/docker-entrypoint.…"   6 seconds ago       Up 4 seconds        0.0.0.0:3344->80/tcp   nginx01
$ curl localhost:3344
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>

# 进入容器
$ docker exec -it nginx01 /bin/bash
root@f9bb25f3b023:/# whereis nginx
nginx: /usr/sbin/nginx /usr/lib/nginx /etc/nginx /usr/share/nginx
root@f9bb25f3b023:/# cd /etc/nginx
root@f9bb25f3b023:/etc/nginx# ls
conf.d	fastcgi_params	koi-utf  koi-win  mime.types  modules  nginx.conf  scgi_params	uwsgi_params  win-utf
```

### 1.6 部署Tomcat

```shell
# 官方的使用
$ docker run -it --rm tomcat:9.0
# 官方方式用完以后会直接删除容器（镜像不会删），一般用来测试

# 下载
docker pull tomcat

# 启动运行
docker run -d -p 3355:8080 --name tomcat01 tomcat

# 测试可以访问，但是会报 404 错误
# 进入容器
$ docker exec -it tomcat01 /bin/bash
root@6f2cbf8b73fe:/usr/local/tomcat# ls
BUILDING.txt  CONTRIBUTING.md  LICENSE	NOTICE	README.md  RELEASE-NOTES  RUNNING.txt  bin  conf  lib  logs  native-jni-lib  temp  webapps  webapps.dist  work

# 发现 webapps 中没有文件，但是可以在 webapps.dist 中发现文件
root@6f2cbf8b73fe:/usr/local/tomcat# cd webapps
root@6f2cbf8b73fe:/usr/local/tomcat/webapps# ls
root@6f2cbf8b73fe:/usr/local/tomcat/webapps# cd ..
root@6f2cbf8b73fe:/usr/local/tomcat# cd webapps.dist/
root@6f2cbf8b73fe:/usr/local/tomcat/webapps.dist# ls
ROOT  docs  examples  host-manager  manager

# 可以把 webapp.dist 中的文件拷贝到 webapp 中，这样就不报 404 了
root@6f2cbf8b73fe:/usr/local/tomcat/webapps.dist# cd ..
root@6f2cbf8b73fe:/usr/local/tomcat# cp -r webapps.dist/* webapps/
```



## 二、DeepLearning-Docker

[安装教程](https://blog.csdn.net/BigData_Mining/article/details/99681168)：注意要用显卡就必须装nvidia-docker

### 2.1 创建容器

* **查看**

	* ```shell
		docker images			#查看镜像
		docker ps 				#查看当前运行的容器信息
		```

* **下载**

	* [镜像网站](https://hub.docker.com/search?q=&type=image)

	* ```shell
		docker pull [name]:version
		```

* **创建**
	* -w 工作目录
	* --name 容器名
	* -v 目录映射
	* nvidia/cuda:10.0：image名（若不是latest则需要制定版本）

```shell
nvidia-docker run -itd --net host --shm-size 31G -w /home/ --name [容器名] -v /home/:/mnt/ nvidia/cuda:10.0 /bin/bash
```

* **执行**

```shell
docker attach [容器名]								# 退出挂断
docker exec -it [容器名] /bin/bash/	            # 退出不挂断
```

* **停止**

```shell
docker stop [容器名]
```

* **删除**

```shell
docker rm [容器名]
```

### 2.2 制作镜像

* **创建**

```shell
docker commit [容器名] [镜像名]
```

* **删除**

```shell
docker rmi [镜像名]
```

### 2.3 上传教程

```shell
git clone https://github.com/docker/doodle.git
cd doodle/cheers2019 && docker build -t komorebi0724/cheers2019 .
docker run -it --rm komorebi0724/cheers2019
docker login && docker push komorebi0724/cheers2019
```

### 2.4 安装

* 安装vim

```shell
apt-get update
apt-get install vim
```

* 安装anaconda（用安装包装）











