## TP-Link TL-MR3420 v1路由上运行`WallProxy` v2.1.x ##

  * 软硬件环境：本例中的路由是老款TP-Link TL-MR3420 v1改16M Flash，64M RAM，固件刷`OpenWrt`-dreambox，外挂4G U盘到根目录。其实跑`WallProxy`，16M Flash已经够了，但想跑更多软件，最好还是外挂上一个U盘，而且`OpenWrt`-dreambox的固件外挂U盘很方便。现在很多路由都比这款配置高，基本都有128M RAM，跑`WallProxy`肯定没问题。更多`OpenWRT`信息请到这里获取：https://openwrt.org，http://www.openwrt.org.cn。
  * 本例中路由配置如下：
> > ![http://wallproxy.googlecode.com/git/wiki/OpenWRT.img/001.png](http://wallproxy.googlecode.com/git/wiki/OpenWRT.img/001.png)
> > ![http://wallproxy.googlecode.com/git/wiki/OpenWRT.img/002.png](http://wallproxy.googlecode.com/git/wiki/OpenWRT.img/002.png)
  * U盘挂载情况：
> > ![http://wallproxy.googlecode.com/git/wiki/OpenWRT.img/003.png](http://wallproxy.googlecode.com/git/wiki/OpenWRT.img/003.png)
> > ![http://wallproxy.googlecode.com/git/wiki/OpenWRT.img/004.png](http://wallproxy.googlecode.com/git/wiki/OpenWRT.img/004.png)
  * 安装步骤：
    1. 在`OpenWRT`上安装libffi、python-mini、python、python-openssl、pyopenssl、libncurses、terminfo、screen。要在`OpenWRT`上跑gevent，必须首先自行编译OpenWRT的SDK环境，然后在SDK环境中编译greenlet、libevent2（一定要在gevent之前编译）和gevent。这里需要说明的是虽然我成功编译了gevent1.0rc2，但它却在OpenWRT中自动退出，最后只能放弃，转而编译gevent0.13.8，该版本可以在OpenWRT中流畅地运行不出任何问题。因为不同路由使用的CPU会不同，SDK环境也就会不同，而且初次建立SDK需要几个小时时间，所以我只能建适合自己路由使用的SDK，不过我在这里给出建立package所需要的Makefile，https://wallproxy.googlecode.com/files/sdk_mf.zip，自己动手去编译适合自己用的软件包吧。更多OpenWRT\_SDK和OpenWRT软件包的信息，请参考这里：http://wiki.openwrt.org/doc/howto/obtain.firmware.sdk和http://wiki.openwrt.org/doc/devel/packages。先给大家看看WallProxy的运行环境：
![http://wallproxy.googlecode.com/git/wiki/OpenWRT.img/013.png](http://wallproxy.googlecode.com/git/wiki/OpenWRT.img/013.png)
      * 由于python体积太大，需要路由外接u盘或移动硬盘才能安装。
      1. openwrt下格式化U盘，挂载U盘。op不能写ntfs格式的u盘，windows系统无法将u盘格式化为ext4格式。 在web管理界面的磁盘应用-磁盘信息下可以看到U盘已识别为 /dev/sda，但因为是ntfs（或者fat）格式好像无法挂载。先格式化U盘为ext4格式。代码：
```
opkg install kmod-fs-ext3 （未测试是否必需）
opkg install e2fsprogs （未测试是否必需）
mkfs.ext4 /dev/sda (按 Y 确认)
```
      1. 回到web界面，在磁盘应用-磁盘信息下，复制uuid（双引号内内容）。磁盘应用-挂载点-挂载点，点“添加”，基本设置里，设备栏从下拉列表选择u盘，文件系统选择 ext4，在高级设置里填入uuid。然后保存应用。刷新。在已挂在的文件系统已经能看到 /mnt/sda1.
        * 状态如下：
> > > > ![http://wallproxy.googlecode.com/git/wiki/OpenWRT.img/014.png](http://wallproxy.googlecode.com/git/wiki/OpenWRT.img/014.png)]
> > > > ![http://wallproxy.googlecode.com/git/wiki/OpenWRT.img/015.png](http://wallproxy.googlecode.com/git/wiki/OpenWRT.img/015.png)]
      1. 将python安装到u盘。
```
mkdir /mnt/sda1/packages/ （新建软件包目录）
echo dest usb /mnt/sda1/packages/ >> /etc/opkg.conf （修改opgk配置文件）
opkg --dest usb install python （将python安装到U盘）
ln -s /mnt/sda1/packages/usr/bin/python /usr/bin/python （为python创建快捷方式，如省略此步，需键入 /mnt/sda1/packages/usr/bin/python 才能启动python）
opkg –dest usb install pyopenssl python-openssl （安装ssl协议）
```
      1. 记住安装软件时不要漏了--dest usb，这样才会安装到U盘或硬盘上。
    1. 下载`WallProxy`并解压，配置服务器端（过程省略，看官方教程）；将解压后local文件夹改名为`wallproxy`并删除`OpenWRT`不需要的文件。因为我觉得`WallProxy`的WEB管理页挺实用的，所以只删除了WIN需要的文件，你也可以根据自己的需要来删除文件.

> > > ![http://wallproxy.googlecode.com/git/wiki/OpenWRT.img/005.png](http://wallproxy.googlecode.com/git/wiki/OpenWRT.img/005.png)
    1. 修改proxy.ini文件，主要就是监听的IP地址和端口，每人的实际情况也许会不同，根据自己的情况来修改。因为我的路由IP是192.168.1.1，而且端口8087被`GoAgent`占用了，所以我把监听地址及端口改成了192.168.1.1:9087。用WinSCP上传`wallproxy`文件夹到路由/etc目录中。
    1. 在路由/etc/`wallproxy`目录中新建一个文件，取名startup，给予执行属性，负责`wallproxy`的后台启动并守护进程，
> > > ![http://wallproxy.googlecode.com/git/wiki/OpenWRT.img/007.png](http://wallproxy.googlecode.com/git/wiki/OpenWRT.img/007.png)
> > > ![http://wallproxy.googlecode.com/git/wiki/OpenWRT.img/008.png](http://wallproxy.googlecode.com/git/wiki/OpenWRT.img/008.png)
> > > startup文件内容如下：
```
while [ 1 ] ; do
	if [ $(ps -ef | grep "/var/wallproxy/startup.py" | grep -v "grep" | wc -l) -eq 1 ];
	then
		sleep 3s
	else
		python /var/wallproxy/startup.py
	fi
	done
```
    1. 在路由/etc/init.d目录中新建一个文件，取名`wallproxy`，同样给予执行属性，负责`wallproxy`的自动后台启动，
> > > ![http://wallproxy.googlecode.com/git/wiki/OpenWRT.img/009.png](http://wallproxy.googlecode.com/git/wiki/OpenWRT.img/009.png)
> > > `wallproxy`文件内容如下：
```
 #!/bin/sh /etc/rc.common

START=99

start() {
	if [ $(ps -ef | grep '/var/wallproxy/startup.py' | grep -v 'grep' | wc -l) -eq 1 ];
	then
		echo WallProxy already running...
		exit 0
	else
	[ -d /var/wallproxy ] || {
		cp -a -f /etc/wallproxy /var
	}
	screen -d -m /var/wallproxy/startup
	fi
}

stop() {
	kill -9 $(ps -ax|grep '/var/wallproxy/startup'|grep -v 'grep'|awk '{print$1}')
	screen -wipe
}
```
    1. 现在，我们可以在`OpenWRT`的自动启动中看到`wallproxy`的选项了。
> > > ![http://wallproxy.googlecode.com/git/wiki/OpenWRT.img/010.png](http://wallproxy.googlecode.com/git/wiki/OpenWRT.img/010.png)
> > > 我们可以在这里启动或停止`wallproxy`。
    1. 直接按启动按钮或重启`OpenWRT`都可以启动`wallproxy`，在`OpenWRT`中检查`wallproxy`的进程：
> > > ![http://wallproxy.googlecode.com/git/wiki/OpenWRT.img/011.png](http://wallproxy.googlecode.com/git/wiki/OpenWRT.img/011.png)
    1. 设置好浏览器的代理，现在你就可以享受`wallproxy`给你带来的乐趣了！


---

备注：
  * 如果你在家以外的地方使用路由的代理功能，必须做好端口映射。另外，为了数据不泄露，最好用stunnel做好加密传输。

> ![http://wallproxy.googlecode.com/git/wiki/OpenWRT.img/012.png](http://wallproxy.googlecode.com/git/wiki/OpenWRT.img/012.png)

---

编写者：mr.baolei，wwqgtxx