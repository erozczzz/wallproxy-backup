## dd-wrt wallproxy 部署教程 ##

  * 这个教程测试环境为ASUS RT-N13U B1 最新版DDWRT
  * 如果你没用过wallproxy的话最好在PC端做好服务端部署和验证.
这样做下面事情会简单一些.
  * 1~6阶段是准备工作,如果你已经挂载U盘和装好openwar可直接跳至第七步.
  * 准备工作：路由器打开SSH，分区为ext3的U盘或者移动硬盘，SSH客户端 WinSCP
  * 用WinSCP连接到路由器，/mnt/是我的U盘挂载后的路径，以下这个路径都要改成你自己的U盘挂载的路径，可以在USB设置的“已连接设备”里面，产品名称那里看得到挂载is mounted on 后面的就是你的路径了。
  * 下面开始部署：
    1. 用电脑的相应软件格式化U盘为EXT3，并格式化出64M的swap
> > > 建立swap文件，我的共建立了64MB，如果你已经在使用swap分区，此步骤就可以省略：
    1. 在U盘上新建一个opt文件夹：
```
mkdir /mnt/sda_part1/opt
```
    1. 挂载U盘到opt目录：
```
mount -o bind /mnt/sda_part1/opt /opt
```
    1. 安装openware：
```
cd /opt

wget https://wallproxy.googlecode.com/files/optware-install.sh -O - | tr -d '\r' > optware-install.sh
sh optware-install.sh
```
    1. 更新一下optware和安装busybox：
```
/opt/bin/ipkg update
/opt/bin/ipkg install busybox
```
    1. 建立swap文件，我的共建立了64MB，如果你已经在使用swap分区，此步骤就可以省略：
```
dd if=/dev/zero of=/tmp/mnt/sdb1/swapfile/swapfile bs=1024 count=65536 
```
    1. 把swap挂载上：
```
/opt/bin/busybox mkswap /tmp/mnt/sdb1/swapfile/swapfile 
/opt/bin/busybox swapon /tmp/mnt/sdb1/swapfile/swapfile 
```
    1. 下载并安装wallproxy服务器端：详见 [图文教程2](Tutorial2.md)配置不算很复杂，不过第一次要仔细看好每个步骤。这是用wallproxy的前提条件。全部部署后用PC端软件验证一下，如正常可进入下一步。
    1. 拷贝wallproxy客户端到路由器，在路由器上建立/opt/wallproxy/目录，打开wallproxy目录下的local文件夹，把除了.exe的所有上传到/opt/wallproxy/目录下。
      * 目录图：
> > > > ![http://wallproxy.googlecode.com/git/wiki/OpenWRT.img/005.png](http://wallproxy.googlecode.com/git/wiki/OpenWRT.img/005.png)
    1. 打开proxy.ini，修改
```
[listen]
;监听ip端口
ip = 127.0.0.1
port = 8086
```
```
[gae]
;是否启用GAE服务端
enable = 1
;服务端appid（多个用|分隔，个数不限）
appid = appid1|appid2
;防盗用key
password = 
```
    1. 安装python26和OpenSSL：#10几兆下载比较慢耐心等。。。
```
/opt/bin/ipkg install screen
/opt/bin/ipkg install python26  
/opt/bin/ipkg install openssl
/opt/bin/ipkg install py26-openssl
```
    1. 运行wallproxy测试一下：
```
/opt/bin/python2.6 /opt/wallproxy/startup.py
```
      * 如果见到以下信息，就运行成功了
```
------------------------------------------------------------------------------
Welcome to use wallproxy, Copyright (C) 2009-2012 HustMoon Studio
Version: wallproxy/2.1.13 (python/2.7.3, gevent/None)
Listen : 127.0.0.1:8086
------------------------------------------------------------------------------
```
    1. 在启动命令下加入以下脚本后可开机启动
```
mount -o bind /mnt/sda_part1/opt /opt
/opt/bin/screen -d -m  python2.6 /opt/wallproxy/startup.py
```
    1. 之后在浏览器的代理选项填入"路由器IP:8087"或"路由器IP:8086"就能代理了.


---

编写者：wwqgtxx