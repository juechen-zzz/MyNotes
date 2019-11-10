### Linux安装CUDA10.0

#### 查看设备信息

* 查看操作系统

```shell
cat /etc/issue
```

* 查看显卡

```shell
nvidia-smi
```

* 查看显卡驱动

```shell
cat /proc/driver/nvidia/version
```

![image-20191110123518410](../images/image-20191110123518410.png)



### 安装CUDA

* 下载地址 https://developer.nvidia.com/cuda-toolkit-archive
* ![image-20191110124502716](../images/image-20191110124502716.png)
* 多版本CUDA的转换

```shell
# 删除旧版本的软连接
sudo rm -rf cuda
# 建立新版本的软连接，前面的路径是需要的版本的cuda的安装路径
sudo ln -s /usr/local/cuda-9.1 /usr/local/cuda  
```

### 安装CUDNN

* 下载地址 https://developer.nvidia.com/rdp/cudnn-archive