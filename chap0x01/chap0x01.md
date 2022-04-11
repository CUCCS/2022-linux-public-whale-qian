# 第一章：Linux基础（实验）

### 一、实验环境。

- Virtualbox
- Ubuntu 20.04 Server 64bit
- 阿里云 云起实验室 提供的【零门槛云上实践平台】

### 二、实验问题。

- 调查并记录实验环境的如下信息：

  - 当前 Linux 发行版基本信息

    （1）

  ```
  cat /etc/issue
  ```

  Ubuntu 20.04 Server 64bit:  ![](C:\Users\23867\Desktop\Ub-inuxversion1.png)

   （2）

  ```
  cat /etc/os-release
  ```

  Ubuntu 20.04 Server 64bit: ![](C:\Users\23867\Desktop\Ub-inuxversion2.png)

  aliyun cloud:![](C:\Users\23867\Desktop\cloud-Linux version2.png)

    （3）

  ```
  less /etc/os-release
  ```

  Ubuntu 20.04 Server 64bit:

  ![](C:\Users\23867\Desktop\Ub-inuxversion3.png)

  (ps.按q退出)

  aliyun cloud:![](C:\Users\23867\Desktop\cloud-Linux version3.png)

  （4）

  ```
  cat /etc/*release*
  ```

  Ubuntu 20.04 Server 64bit:

  ![](C:\Users\23867\Desktop\Ub-inuxversion4.png)

  alicloud:

  ![](C:\Users\23867\Desktop\cloud-Linux version4.png)

  （5）

  ```
  cat /etc/centos-release
  ```

  alicloud:

  ![](C:\Users\23867\Desktop\cloud-Linux version5.png)

  Ubuntu 20.04 Server 64bit:

  ![](C:\Users\23867\Desktop\Ub-Linux version5.png)

  （6）

  ```
  lsb_release -a
  ```

  alicloud:

  ![](C:\Users\23867\Desktop\cloud-Linux version6.png)

  Ubuntu 20.04 Server 64bit:

  l![](C:\Users\23867\Desktop\Ub-Linux version6.png)

  
  
  
  
  - 当前 Linux 内核版本信息

```
uname -r
```

aliicloud:

![](C:\Users\23867\Desktop\cloud-kernel.png)

Ubuntu 20.04 Server 64bit:

![](C:\Users\23867\Desktop\Ub-kernel.png)





- Virtualbox 安装完 Ubuntu 之后新添加的网卡如何实现系统开机自动启用和自动获取 IP？

  （1）查看虚拟网卡

```
ip a
```

![](C:\Users\23867\Desktop\Ub-network.png)

​         (2)进入vim编辑器

```
sudo vim /etc/netplan/00-installer-config.yaml
```

 ![](C:\Users\23867\Desktop\vim.png)       

 (3)手动添加以下内容:

```
enp0s8:
dhcp4: true
```

​     ![](C:\Users\23867\Desktop\vim fix.png)

   (4)退出vim编辑器

退出方法：先按ESC进入命令模式，再输x

再执行

```
sudo netplan apply
```





- 如何使用 `scp` 在「虚拟机和宿主机之间」、「本机和远程 Linux 系统之间」传输文件？

(1)虚拟机和宿主机

  （1.在两个设备上创建文本文件

宿主机创建1.txt,并写入内容 “hello!”

虚拟机创建空文件夹 2

```
     touch 1.txt （写入信息）
     echo "hello!" > 1.txt
     cat 1.txt
```

```
      mkdir 2(为空)
```

​    2)  pwd确定文件的路径

   3)从宿主机到虚拟机的文件传输

```
scp -P 22 root@192.168.56.101:/root/1.txt ./`
```

   4)查看虚拟机文件2中出现了1.txt,打开查看到“hello！”

命令格式将虚拟机文件传给宿主机

![](C:\Users\23867\Desktop\virtual and host scp1.png)

![](C:\Users\23867\Desktop\virtual and host scp2.png)



(2)本机和远程Linux系统（在本地虚拟机Ub20.04.2与阿里云平台间传输）

（1.在两个平台创建两个文本文件

```
touch a.txt   
touch b.txt
```

（2.确认文件路径,用户名，IP

![](C:\Users\23867\Desktop\virtual and local1.png)

 ![](C:\Users\23867\Desktop\virtual and local2.png)



- 如何配置 SSH 免密登录？

（1）设定生成公私钥对：

```
ssh-keygen -b 4096
```

(2) 输入命令

```
ssh-copy-id -i ~/.ssh/id_rsa.pub cuc@192.168.56.101
```

(3）输入虚拟机口令完成配置

(4)实现免密登录

```
ssh 'cuc@192.168.56.101'
```

![](C:\Users\23867\Desktop\ssh-withoutpassward Right1.png)

![](C:\Users\23867\Desktop\ssh-without passward R2.png)

### 三、遇到问题及解决措施。

1.注意在alicloud中不存在目录。

2.vim编辑器的退出。

课堂是没有完全理解vim的相应控制，查阅了相应的内容，确定了先ESC进入命令模式，再:x保存。

参考网页：

http://m.coozhi.com/youxishuma/g4/91890.html

https://www.cnblogs.com/sophie_wang/p/7905219.html

3.免密登录失败

因为发生了Cannot assign requested address的报错，刚开始去查阅了一些网页找了报错的原因以为是Linux分配客户端用尽的原因询问了老师解决方法，但是后来经过老师提醒发现只是因为ip看错输错了（QAQ），在查看和检查方面要更细心一点。

![](C:\Users\23867\Desktop\ssh-without passward F.png)

4.scp的远程及本地的使用。

```
cp  root@ip:/root/XXX.tar.gz xxx.tar.gz
eg: scp root@远程服务器ip地址:/root/xxx.zip(服务器目录) /Users/gaoaifei/xxx.zip(本地存储目录)---在本地端口执行操作
```