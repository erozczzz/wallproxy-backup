## 在HG320 Tomato路由上运行2.1.x版WP，带pyopenssl，gevent和pycrypto支持 ##

  * 软硬件环境：HG320改256M内存（原来的64M内存基本上也够用，实际占用内存在40M以下），刷sady 0115版tomato固件，一个U盘。参考http://muramato.blogspot.com/2012/04/tomatogoagent.html
  * 操作步骤：
    1. 格式化u盘为ext3，并且把U盘挂载为/opt目录。64M以上内存可以不用按教程开启u盘swap分区。直接格式化成一个分区即可，格式化后需要重启路由。
    1. 按教程安装optware （这玩意儿用处就和appstore差不多，只不过是命令行版的。）
    * A 初级用户：搭建python2.6.8+py26-openssl环境：
      1. 用下面命令安装
```
ipkg install python2.6 
ipkg install py26-openssl
```
      1. 这样就已经可以使用wallproxy基本功能了（GAE+PAAS都可以正常使用）。
    * B 高级用户：参考《Tomato中python2.7+gevent1.0rc2环境的搭建》https://maskv.com/technology/272.html搭建完整功能环境。gevent可以减轻系统压力，提高并行处理效率，减少同时运行的线程数。没有gevent现象参考这个：https://code.google.com/p/wallproxy/issues/detail?id=90。Windows版WP已经内置了gevent，启动时候第一行信息可以看出来。示意贴图：![http://wallproxy.googlecode.com/git/wiki/Tomato.img/008.png](http://wallproxy.googlecode.com/git/wiki/Tomato.img/008.png)
      1. 首先卸载路由当中的python2.6及其相关组件，运行：
```
ipkg -force-removal-of-dependent-packages remove python26
rm -rf /opt/local/lib/python2.6 #若挂载路径非/opt请自行修改
rm -rf /opt/lib/python2.6
```
      1. 之后更新、安装所需要的依赖包：
```
ipkg update
ipkg upgrade
ipkg install busybox buildroot make grep openssl openssl-dev libuclibc++ wget-ssl python27 py27-setuptools
```
      1. 安装完成之后，我们要修复几个py27-setuptools当中的小错误。
        1. 首先easy\_install的安装路径莫名其妙的指向了python2.5，不知是否只有我遇到了这个问题。修改 /opt/lib/python2.7/distutils/distutils.cfg 文件，将
```
site-dirs  = /opt/local/lib/python2.5/site-packages
```
> > > > > 修改为
```
site-dirs  = /opt/local/lib/python2.7/site-packages
```
        1. 其次easy\_install的安装临时目录指向了/tmp目录，会造成安装某些组件时tmp剩余空间不足而导致的安装失败，我们要手动将其指定到挂载的U盘当中。
          * 在/opt中创建tmp文件夹，修改 /opt/lib/python2.7/site-packages/setuptools/command/easy\_install.py 文件，找到412行：
```
tmpdir = tempfile.mkdtemp(prefix="easy_install-")
```
> > > > > > 将其修改为：
```
tmpdir = tempfile.mkdtemp(prefix="easy_install-",dir="/opt/tmp")
```
      1. 然后就可以使用easy\_install安装greenlet与cython了（此步骤花费时间奇长无比）：
```
easy_install-2.7 greenlet
```

> > > > 注：Cython非必须组件，可跳过此步骤直接编译安装Gevent，若出错再尝试安装Cython
```
easy_install-2.7 cython
ln -s /opt/local/bin/cygdb /opt/bin/cygdb 
ln -s /opt/local/bin/cython /opt/bin/cython
```
      1. 之后下载gevent：
```
cd /opt
/opt/bin/wget https://github.com/downloads/SiteSupport/gevent/gevent-1.0rc2.tar.gz --no-check-certificate
tar zxvf gevent-1.0rc2.tar.gz
cd gevent-1.0rc2
```
> > > > 直接编译会报错，因为使用了mips2的sync指令，在Goagent的issues上找到了答案。
> > > > 修改 /libev/ev.c 文件，找到604行的：
```
#define ECB_MEMORY_FENCE         __asm__ __volatile__ ("sync"     : : : "memory")
```
> > > > 修改为：
```
#define ECB_MEMORY_FENCE         __asm__ __volatile__ (".set mips2; sync; .set mips0"     : : : "memory")
```
> > > > 执行
```
python2.7 setup.py install
```
      1. 下载并安装pyOpenSSL
```
cd /opt
wget http://pypi.python.org/packages/source/p/pyOpenSSL/pyOpenSSL-0.12.tar.gz
tar zxvf pyOpenSSL-0.12.tar.gz
cd pyOpenSSL-0.12
python2.7 setup.py install
```
      1. 安装结束之后，运行wallproxy可能会提示.python-eggs错误，我们可以手动指定缓存目录。
        * 修改wallproxy的startup.py文件，在：
```
import os
```
> > > > > 下一行添加：
```
os.environ['PYTHON_EGG_CACHE'] = '/opt/.python-eggs'
```
        * 或者可以将 gevent-1.0rc2-py2.7-linux-mips.egg 解包：
```
cd /opt/local/lib/python2.7/site-packages
mv gevent-1.0rc2-py2.7-linux-mips.egg gevent-1.0rc2-py2.7-linux-mips.egg.zip
mkdir gevent-1.0rc2-py2.7-linux-mips.egg
cd gevent-1.0rc2-py2.7-linux-mips.egg
unzip /opt/local/lib/python2.7/site-packages/gevent-1.0rc2-py2.7-linux-mips.egg.zip
```
      1. 再次运行wallproxy即可。
    * C 可选环境： 参考上文，从http://pypi.python.org/pypi/pycrypto/2.6 下载pycrypto 2.6版，直接用python编译，可以通过。编译完成后就可以使用OSP2插件调用原来的高强度加密服务端。
    1. 另外编译时如果内存不够可以执行下列命令，在U盘上临时增加128M虚拟内存空间
```
dd if=/dev/zero of=/opt/swapfile bs=1024 count=131072
/opt/bin/busybox mkswap /opt/swapfile
/opt/bin/busybox swapon /opt/swapfile
```
    1. FTP上传wallproxy代码至/opt/etc，实际只需要 startup.py proxy.ini/proxy.py（可选） misc文件夹 cert文件夹(可选)。上传后可以在terminal里运行python2.6 startup.py进行测试，看log输出有没有错误，看gevent是否加载成功等。调试成功后再用screen -d -m 在后台运行，详细解释见后。默认wp监听在127.0.0.1，这个在电脑本机上用没用问题。要在路由上运行，又能让局域网的机子都能访问，绑定需要改成192.168.1.1 (路由的局域网IP) 或者0.0.0.0 （全局），推荐绑定路由局域网IP。
  * 其他内容：
    1. 可以在路由上运行sogou proxy源代码，用WP引用电信/联通/教育网iplist，用sogou全网加速搞定南北互通/教育网互通等。关于sogou proxy，源代码见http://xiaoxia.org/2011/11/14/update-sogou-proxy-program-with-https-support/，注意ipv4需要把134行的socket.AF\_INET6  改成socket.AF\_INET。iplist可以直接用这个页面上的资源http://clangcn.com/blog/archives/732.html，credit to Clang.
    1. 可以运行APJP的python实现版本APPP，用来弥补WP GAE服务端不能用来访问某些特定网站的不足(如backchina.com)。APJP的服务端部署请参看http://code.google.com/p/apjp/downloads/list, APPP下载请到http://fartersoft.com/appp/。尽管appp没有实现apjp原版的多服务器并行，但是WP可以用https://code.google.com/p/wallproxy/issues/detail?id=685的方法实现多服务端负载平衡。
    1. 这样实现了WP调用sogou proxy，调用appp代理特殊网站，调用原来老的高强度加密客户端，同时用gevent实现了资源占用优化。基本上与PC客户端功能完全一致，实际使用中也没有什么问题，运行很稳定。
    1. 最后可以把自动挂载运行脚本（以python2.7为例）写入路由管理界面，如下图示例。 (注：编译gevent后导致wp或者goagent不能跟随系统启动，原因及解决方案见https://code.google.com/p/wallproxy/issues/detail?id=675。)

> > > ![http://wallproxy.googlecode.com/git/wiki/Tomato.img/007.png](http://wallproxy.googlecode.com/git/wiki/Tomato.img/007.png)
```
sleep 5
mount -o bind /tmp/mnt/sda1 /opt
cd /opt/etc/wallproxy/
screen -d -m python2.7 sogou.py
screen -d -m python2.7 startup.py
cd /opt/etc/appp
screen -d -m python2.7 appp.py -a
```
> > > 卸载前脚本，kill掉所有的screen进程
```
killall - screen
```


---

备注：
  1. 推荐必用工具之SecureCRT SecureFX 7.0集合汉化版，一个包搞定ssh2/telnet和FTP。 http://pan.baidu.com/share/link?shareid=113086&uk=321397830
  1. 在SecureCRT/telnet等terminal里运行ps命令，应该可以看到这些进程：

> > ![http://wallproxy.googlecode.com/git/wiki/Tomato.img/003.png](http://wallproxy.googlecode.com/git/wiki/Tomato.img/003.png)
> > ![http://wallproxy.googlecode.com/git/wiki/Tomato.img/002.png](http://wallproxy.googlecode.com/git/wiki/Tomato.img/002.png)
  1. screen命令是把wallproxy强制在后台运行，那么如何查看后台的进程呢，用ps命令查看pid,如上图，pid就是4774, 运行命令screen -r 4774就可以查看wallproxy的运行状态。要想此时终止wallproxy，可以按ctrl+a，然后输入k，提示的时候输入y，当前screen进程就被kill了。要想继续回到后台运行，那么就ctrl+a, 然后输入d。就回到命令符状态。
  1. 目录图，供参考用。
> > ![http://wallproxy.googlecode.com/git/wiki/Tomato.img/004.png](http://wallproxy.googlecode.com/git/wiki/Tomato.img/004.png)

---


## **提问请到https://code.google.com/p/wallproxy/issues/detail?id=618** ##