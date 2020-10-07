# Docker

[安装教程](https://blog.csdn.net/BigData_Mining/article/details/99681168)：注意要用显卡就必须装nvidia-docker

## 1 创建容器

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
docker exec -it [容器名] /bin/bash/	# 退出不挂断
```

* **停止**

```shell
docker stop [容器名]
```

* **删除**

```shell
docker rm [容器名]
```



## 2 制作镜像

* **创建**

```shell
docker commit [容器名] [镜像名]
```

* **删除**

```shell
docker rmi [镜像名]
```



## 3 上传教程

```shell
git clone https://github.com/docker/doodle.git
cd doodle/cheers2019 && docker build -t komorebi0724/cheers2019 .
docker run -it --rm komorebi0724/cheers2019
docker login && docker push komorebi0724/cheers2019
```



## 4 安装

* 安装vim

```shell
apt-get update
apt-get install vim
```

* 安装anaconda（用安装包装）

