## NTLM环境配置教程 ##
  * 实现方式：由于`Wallproxy`本身不支持NTLM验证，所以我们使用CNTLM，CNTLM可以把NTLM代理转为一个普通的代理，所以这个问题就解决啦，官方网站：http://sourceforge.net/projects/cntlm/
  * 具体cntlm的使用方法可以参考官方wiki：http://sourceforge.net/apps/mediawiki/cntlm/index.php?title=Main_Page
  * 给大家一个简单一些的方法吧，应该对于大多数情况都可以适用。
    1. 第一步是下载cntlm：[官方下载地址](http://sourceforge.net/projects/cntlm/files/cntlm/cntlm%200.92.3/) 考虑到很多公司电脑是没有管理员权限的，建议下载cntlm-0.92.3-win32.zip这个免安装的zip包。（[cntlm-0.92.3-win32.zip下载](https://wallproxy.googlecode.com/files/cntlm-0.92.3-win32.zip)）
    1. 第二步就是解压缩，找个顺手的地方放进去就行。
    1. 第三步修改配置文件cntlm.ini，主要是以下几个地方（请注意配置文件中使用了明文密码，有潜在的安全风险）：
```
  Username 这个修改成你的账户名，也就是xxx\yyy的yyy部分
  Domain 这个修改成你账户的域名，也就是xxx\yyy的xxx部分
  Password 这里输入你账户的密码
  Proxy 这里是公司ISA服务器或者代理服务器的地址（含端口），例如aaaaa:80
  Listen cntlm的监听端口，默认就好，也可以自己修改，这个数字会在wallproxy的配置文件中用到
```
    1. 第四步创建一个指向cntlm.exe的快捷方式，在Target的最后加上 -c cntlm.ini，不然的话默认读取的是C:\Program Files\cntlm中的cntlm.ini，会报错。
    1. 第五步，双击快捷方式启动cntlm，会有一个黑色小窗口一闪而过，貌似是Cygwin，不确定，任务管理器中可以看到cntlm.exe在运行。
    1. 第六步，修改wallproxy的配置文件，如果之前修改了Listen的值，这里port需要对应修改：
```
  [proxy]
  ;是否需要通过其他代理上网
  enable = 1
  ;代理地址
  host = 127.0.0.1
  port = 3128
  ;代理帐号（如果是ntlm等没有直接支持的认证方式，将username设为空）
  username = 
  password = 
```
    1. 第七步，重启wallproxy，就可以在NTLM环境中使用`Wallproxy`了
  * 为了安全也可以使用cntlm.exe -H获取用于验证的hash，但是每次换密码都要重新来一次，太麻烦。。。


---

编写者：wwqgtxx

内容提供者：gwjwin，liang1985