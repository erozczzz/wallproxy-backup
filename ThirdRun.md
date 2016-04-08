## Third Run插件 ##
  * 插件介绍：
    * third插件作用：运行第三方python程序，因为在同一个进程内可以共用模块，应该可以节省一点内存。
    * 使用方法：
      1. 修改配置文件
        * 手工修改proxy.ini，加入
```
[third] 	
;位于misc目录下的第三方python程序 
appp = "appp.py", "-a", wait=5
sogou = "sogou.py","", wait=6
```
        * 或者手工修改config.py，加入
```
from plugins import third; third = install('third', third)
third.run('sogou.py'); third.run('tcpdns.py');
```
      1. 可运行的python程序如sogou.py或者tcpdns.py等放入misc目录下面，即可以让程序由wp启动。
    * 如何运行appp这种后面需要带参数的软件？
      * 如果使用proxy.ini设置
```
[third] 
;位于misc目录下的第三方python程序 
appp = "app.py", "arg1", "arg2", "argn", wait=5
```
      * 如果使用config.py
```
third.run('app.py', 'arg1', 'arg2', 'argn', wait=5)
```
    * wait参数（可选）：wait秒后外部程序应该初始化完毕(例如sogou.py启动了监听端口)，所运行的程序在这之后不应该再访问或修改sys.argv等变量，默认值为5，可酌情修改。
    * （Windows下）不做修改的外部程序在wallproxy通过WEB重启时不会重启，如果需要也重启，注册一个stop函数，例如sougou.py在server.serve\_forever()前加一行：
```
register_stop(server.shutdown)
```
    * dnsbridge.py未测，只是为了表明可以用来运行一个dns代理程序，google了下就选了这个单文件的dnsbridge.py，如果不可行就改用其他的。
  * 如何通过Third Run插件运行APPP？
    1. 仔细查看http://fartersoft.com/appp/有关APPP的说明，配置APPP的客户端和服务端。
    1. 把appp-0.1.2.zip里面的appp.ini和appp.py放入misc文件夹
    1. proxy.ini加入
```
[third]         
;位于misc目录下的第三方python程序 
appp = "appp.py", "-a", wait=3
```
    1. 就可以使APPP与`Wallproxy`一起引导了。
  * 提示`Windows`用户：您应该自行安装配置python运行环境，`Wallproxy`集成的python包并不完整，只保证正常运行`Wallproxy`，基本不会考虑其他程序会类库的需求。


---

编写者：www.ehust，wwqgtxx

参与者：ablmdream(高级`Wallproxy`使用者)