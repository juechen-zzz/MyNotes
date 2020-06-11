# Linux基础

```shell
ls -lh	# 列表形式，h可以将占用空间更好的表示
rm -rf  # 删库跑路
cd      # 回到家目录
cd -		# 在最近的两个目录切换
cp ~./Desktop/a.txt ./	# 文件名不改变，后面只需要写目录
mv -i a.txt b.txt		# 重命名
cat -n/ more # 都用来查看文件，cat看小文件，全部显示，more看大文件，分屏显示
grep -n XXX 123.txt
grep -n ^f 123.txt  /  grep -n ke$ 123.txt
echo XXX > 123.txt  /  echo XXX >> 123.txt # >会覆盖，>>是追加 
scp -P port user@XXX:Desktop/123.txt 123.txt # 远程复制，-r可以复制文件夹
scp -r demp user@XXX:Desktop
ps au	# a查看全部进程，u显示进程详细状态
```

* 重定向：**>** / **>>**  搭配各种指令将本应该显示在终端的字符输入到文件中去
* 管道： **|** 需要两个命令，将第一个命令的输出作为第二个命令的输入  ls -lha / | more

## 1 文件权限（ls -al）

![1563007096936](../images/1563007096936.png)

* 第一栏代表文件的**类型和权限**
  * 第一个字符代表这个文件是**目录、文件或者链接文件**等
    * [d]则为目录
    * [-]则为文件
    * [l]则表示为链接文件（link file）
    * [b]则表示为设备文件里面的可供存储的周边设备（可按块随机读写的设备）
    * [c]则代表设备文件里面的串行端口设备，例如键盘、鼠标
  * 接下来的字符中，以三个为一组，且均为[rwx]的三个参数的组合，代表：**可读、可写、可执行**，这三个权限的位置不会改变，若无这一权限，会用[-]表示
    * 第一组是文件拥有者可具备的权限
    * 第二组为加入此用户组账号的权限
    * 第三组为非本人且并没有加入本用户组的其他账号的权限
* 第二栏表示有多少文件名链接到此节点（node）
* 第三栏表示这个文件的**拥有者账号**
* 第四栏表示这个文件的**所属用户组**
* 第五栏表示这个文件的**容量大小**，默认单位为Bytes
* 第六栏表示这个文件的**创建日期或是最近的修改日期**



## 2 修改文件属性与权限（P157）

* **chgrp**：修改文件所属用户组（change group）
* **chown**：修改文件拥有者
* **chmod**：修改文件的权限，SUID、SGID、SBIT等的特性
  * 文件基本权限有9个：**owner、group、others**，每种对应的**read、write、execute**
  * 权限数字对照表：r, 4; w, 2; x, 1，即[-rwxrwx—]对应为770
  * 符号类型修改文件权限
  * Chmod 777 filename



## 3 目录与路径

* cd ：切换路径   cd - 在最近的两个目录之间来回切换

* pwd ：显示当前目录

* mkdir：**建立**一个新目录

* rmdir：**删除**一个**空目录**

* Touch：建立一个新的文件，格式自行指定

* 目录含义

  * .	代表此层目录
  * ..   代表上一层目录
  * $-$  代表前一个工作目录
  * ~   代表目前使用者身份所在的家目录
  * ~account   代表account这个使用者的家目录

* 在命令行输入的时候，利用Tab可以进行自动补全

* **文件与目录的查看**：$ls$  **P181**

  * ```shell
    ls [-aAdfFhilnrRSt] 文件名或目录名称
    ls [--color={never, auto, always}] 文件名或目录名称
    ls [--full-time] 文件名或目录名称
    ```

    

  * -a 	全部的文件，包含隐藏文件

  * -d     仅列出目录本身，而不是列出目录内的文件数据

  * -l      详细信息显示，包含文件的属性与权限等数据

  * ![image-20200611084341668](../images/image-20200611084341668.png)

* **复制（cp）、删除（rm）与移动（mv）** ： **P183**

  * ```shell
    cp [-adfilprsu] 源文件 目标文件
    cp [options] source1 source2 source3 ... directory
    ```

  * -a  相当于 -dr –preserve=all 的意思

  * -i   若目标文件已经存在时，在覆盖时会先询问操作的进行

  * -p  连同文件的属性（权限、用户、时间）一起复制过去，而非使用默认属性（备份属性）

  * -r   递归复制，用于目录的复制操作

  * ```shell
    rm [-fir] 文件或目录
    ```

  * -f   就是force的意思，忽略不存在的文件，不会存在警告信息

  * -I   交互模式，在删除前会询问使用者是否操作

  * -r   递归删除，最常用于目录的删除

  * ```shell
    mv [fiu] source destination
    mv [options] source1 source2 source3 ... directory
    ```

  * -f   force强制的意思，如果目标文件已经存在，不会询问而直接覆盖

  * -i   若目标文件已经存在时，就会询问是否覆盖

  * -u  若目标文件已经存在，且source比较新，才会更新





## 4 服务器

* 挂后台训练代码：

  ```shell
  nohup 你的命令 > run.log &
  tail -f run.log
  ps -fe | grep train_mle
  ```

* 进程

```shell
nvidia-smi	 查看显卡
kill -9 进程号
CUDA_VISIBLE_DEVICES=0,1,3,4
```

* 创建

```shell
mkdir	创建文件夹
touch 创建文件
```

* 创建虚拟环境

```shell
# 要确保电脑里本来有2的版本才行
virtualenv -p /usr/local/bin/python2.7 venv1			
source venv1/bin/activate
deactivate

# 用conda可以指定任何版本
conda info -e		    查看目前所有虚拟环境
conda create -n your_env_name python=X.X（2.7、3.6等）
conda activate your_env_name
conda install -n your_env_name [package]
conda deactivate
```

* 内存饱满（tap失效）

```
df -h
du -h -x --max-depth=1
按目录查看
rm -rf file
```

* python换国内镜像
	* 指定全局安装源
		* 在unix和macos，配置文件为：**$HOME/.pip/pip.conf**
		* 在windows上，配置文件为：**%APPDATA%\pip\pip.ini**

```shell
[global]
index-url = http://pypi.douban.com/simple
[install]
trusted-host=pypi.douban.com
```

* 查看IP

```shell
ifconfig
```

* scp

![image-20191109160004687](../images/image-20191109160004687.png)

## 5 基础知识

<img src="../images/image-20200430162701846.png" alt="image-20200430162701846" style="zoom: 33%;" />

### 5.1 文件目录类

#### **ls**：显示目录![image-20200501135456626](../images/image-20200501135456626.png)

![image-20200514214835668](../images/image-20200514214835668.png)

#### **cat** :查看文件内容![image-20200501141648666](../images/image-20200501141648666.png)

#### **more**:显示过一页的文件![image-20200501143204741](../images/image-20200501143204741.png)

#### **head tail**：显示头尾![image-20200501143324433](../images/image-20200501143324433.png)

#### **cp:**复制

![image-20200501143622060](../images/image-20200501143622060.png)

![image-20200501145555544](../images/image-20200501145555544.png)

#### **rm**:删除

![image-20200501145810067](../images/image-20200501145810067.png)

![image-20200501145900146](../images/image-20200501145900146.png)

#### **find**:查找

![image-20200501145949168](../images/image-20200501145949168.png)

#### **grep:搜索匹配字符**

![image-20200501150236385](../images/image-20200501150236385.png)

#### **tar**:压缩

![image-20200501150654880](../images/image-20200501150654880.png)

![image-20200501150836980](../images/image-20200501150836980.png)





### 5.2 进程控制类

#### ps:查看进程

![image-20200503232439062](../images/image-20200503232439062.png)

 

```shell
#查看本用户的进程
ps
#查看系统和每位用户的全部进程
ps -aux
#查看xx用户的进程
ps -aux|grep xx
```

#### top:动态监视任务

![image-20200503232946592](../images/image-20200503232946592.png)

#### kill:控制进程

* 正常使用kill -9 xxx

![image-20200503233319906](../images/image-20200503233319906.png)

![image-20200503233622123](../images/image-20200503233622123.png)

#### nice:修改优先级

![image-20200503233742197](../images/image-20200503233742197.png)

#### renice:改变运行中的优先级

![image-20200503234100181](../images/image-20200503234100181.png)

#### &:后台运行

![image-20200503234240574](../images/image-20200503234240574.png)

#### 进程挂起和恢复

![image-20200503234320634](../images/image-20200503234320634.png)



### 5.3 用户权限类

![image-20200504222220624](../images/image-20200504222220624.png)

![image-20200504224258836](../images/image-20200504224258836.png)

![image-20200504224414150](../images/image-20200504224414150.png)

####  chmod:修改权限

![image-20200504224555513](../images/image-20200504224555513.png)

#### chown:修改文件所有者

![image-20200504224722780](../images/image-20200504224722780.png)

#### chgrp:修改文件所属用户组

![image-20200504224910810](../images/image-20200504224910810.png)



### 5.4 编译调试类

![image-20200507093428674](../images/image-20200507093428674.png)

![image-20200507093548320](../images/image-20200507093548320.png)

#### gcc:程序编译工具

![image-20200507094248456](../images/image-20200507094248456.png)

![image-20200507094749020](../images/image-20200507094749020.png)

![image-20200507094943907](../images/image-20200507094943907.png)

#### 静态库编译

![image-20200507095332682](../images/image-20200507095332682.png)

#### 动态库编译

![image-20200507095439827](../images/image-20200507095439827.png)

#### gdb:程序调试工具

![image-20200507095654031](../images/image-20200507095654031.png)



### 5.5 文件命令类

#### open

![image-20200510174457766](../images/image-20200510174457766.png)



