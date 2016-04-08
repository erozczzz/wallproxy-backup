重要通知：[请各位童鞋注意和广传：请各位同志积极参与美国白宫关于“任何助长互联网审查、建立网络封锁墙的人，都应该被拒进入美国境内“的投票](https://code.google.com/p/wwqgtxx-goagent/wiki/toupiao)

---

## 公告 ##
  * `WallProxy v2.1.14` 版发布, 下载链接如下:
    * `Windows` 用户更新或其他系统 (如 Linux, Mac, OpenWRT, Tomato，DDWRT) 用户请下载 http://goo.gl/D2UJv (0.5MB), 更新时直接覆盖原有版本(请注意更新后重新修改Appid信息).
    * `Windows` 用户首次使用请下载完整版 http://goo.gl/dFtij (5.4MB).
    * 备用链接：[完整版](https://wallproxy.googlecode.com/archive/win.zip)(v2.1.13)
  * 暂时兼容 `GoAgent 1.X` 版(理论上兼容 `GoAgent 2.X` 版 `GAE` 端，不兼容 2.X 版 `Paas` 和 `Php` 端，不影响使用，但为了获得更好的体验，建议上传)与旧版 `WallProxy`, 扩展了旧版的 `hosts/pac` 功能, `YouTube` 视频直播两不误, 增加 ini 配置文件, 保留多线程下载, 改善转发性能, 转发失败自动改走 GAE.
  * 新版 `WallProxy` 由原作者对旧版 `WallProxy` 重写, 因为 `GoAgent` 用户较多, 为了方便大多数人利用原有 `GoAgent` 的服务端, GAE 插件部分以兼容 `GoAgent` 为主, 而非旧版 `WallProxy`.
  * 运行环境: Python v2.5, v2.6 和 v2.7, 建议 v2.7 + Gevent + pyOpenSSL (完整版的 `WallProxy` 已包含了 Windows 下的运行环境).
  * 客户端更新记录：
    * v2.1.14(2013.1.25) `PAC支持使用`[dnsResolveEx](http://msdn.microsoft.com/en-us/library/windows/desktop/gg308475(v=vs.85).aspx)`解析ip(IE等浏览器使用dnsResolve只能解析出IPv4地址，用dnsResolveEx才能解析出IPv6)；proxy.ini添加third插件支持`.
<a href='Hidden comment: 不要单用版本号，不靠谱的。
* v2.1.13.2 增加 third 插件, 方便在路由器上节约同时启动多个 python 程序所占用内存, 重新配置proxy.ini.
* v2.1.13.1 修复有关 ntlm 验证的 bug; 修复 php 版的 bug.
* v2.1.13 修复 v2.1.12 引入的重启 bug; 修改配置文件解决部分 Google 网站异常及 Google Music / Youtube 的上传支持; 更新到 gevent 1.0 rc2; 修复 PAAS PHP 服务端 payload bug.
'></a>
  * 服务端更新记录：
    * v2.0.14(2012.10.27) 减小服务端的超时时间, 需要重新上传服务端.
  * 关于是否需要重新上传: 这里有两条更新记录, 上一个版本小于第二条就需要重新上传, 例如这里是 v2.0.14, 如果之前没用过或者是 v2.0.10 就需要, 否则不需要.
  * 如何在手机 (例如 [Android](https://code.google.com/p/wallproxy/downloads/detail?name=GAEProxy%20v2.1.10.apk#makechanges)) 等 [移动设备上使用](AndroidFAQ.md)?

## 简易教程（[图文教程2.0<<<<务必仔细阅读>>>>](Tutorial2.md)） ##
  1. [申请GAE](https://appengine.google.com/)并创建appid（程序内置一些 **公共appid** `[`[不保证安全性](https://code.google.com/p/wallproxy/issues/detail?id=55#c4)`]`，下载后也可直接使用`[跳过4、5步]`，目前公共appid每天很早就流量耗尽，显示 **503 Service Unavailable** ，故为获得更好体验，强烈建议申请并上传自己的）；
  1. 下载wallproxy并解压；
  1. 运行local文件夹下`WallProxy.exe或者Run.bat`（非Windows用户运行startup.py，Windows若提示是否允许安装证书，请允许）；
  1. 上传：运行server文件夹下uploader.bat(非Windows用户运行uploader.py)，输入appid上传（一次只能上传同一个Gmail帐号下的appid，多appid用|分隔，提示Set Proxy时可[输入1来提高上传成功率](https://code.google.com/p/wallproxy/issues/detail?id=53#c2)）；
  1. 访问http://127.0.0.1:8086/#proxy_ini，找到如下部分（56行左右）并修改appid = 后面为自己的appid，点右上角的“保存”之后即可使用了；
```
[gae]
;是否启用GAE服务端
enable = 1
;服务端appid（多个用|分隔，个数不限）
appid = appid1|appid2
```
  1. 代理地址127.0.0.1:8087；如需使用PAC，设置http://127.0.0.1:8087/proxy.pac；如需使用`SwitchySharp/AutoProxy`等浏览器扩展（`SwitchySharp用户可导入配置local\misc\SwitchyOptions.bak`），见图文教程（GUI应该选择“禁用切换”）；如需使用智能代理（使无法使用PAC或扩展的程序也做到该走代理走代理，不该走就不走），设置127.0.0.1:8086为代理即可。
  1. 导入http://127.0.0.1:8086/CA.crt为浏览器根证书可消除浏览器证书警告（IE用户如果第2步已选择允许就不用再向IE导入了，不要重复导入；cmd窗口提示时间与导入后查看到的时间相同基本就是导入成功了，升级版本时请保留cert目录，以免需要再次导入）
  1. 可通过http://127.0.0.1:8086或http://wallproxy访问Web配置界面

---

## FAQ(高级玩家必读，新手提出问题前必读，建议所有用户使用`Wallproxy`之前必读) ##

## [https://code.google.com/p/wallproxy/wiki/FAQ](FAQ.md) ##

### 注意：本FAQ页会经常更新，建议大家经常查阅，也欢迎大家在Issue提出更新建议，我们会酌情修改 ###



---

## 计划 ##
  * 重新编写（进行中）
  * Web配置界面（进行中，暂时需要直接编辑文件，作为替代，实现了ini转py）
  * 支持通配符证书（已实现）
  * PAC支持China IP List等IP列表，修复bug（已实现）
  * 改善对GAE的支持（进行中，暂时先兼容goagent）
  * 更通用的多ip连接，而非仅仅针对.appspot.com（已实现）
  * 结合gevent，改善并发访问（已实现）
  * 更方便的局域网共享，更方便对用户进行身份认证（已实现）
  * 完善作为http、https、socks4、socks5代理使用，可socks转https/http，https转http（已实现）
  * 完善对http、https、socks4、socks5代理的支持，支持basic/digest认证，修复bug（已实现）

---

  * This project is used for issues and wikis of [wallproxy@github](https://github.com/wallproxy/wallproxy/)。