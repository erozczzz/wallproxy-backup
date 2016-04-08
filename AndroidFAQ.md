# AndroidFAQ #
  1. 在那里下载？[downloads](https://code.google.com/p/wallproxy/downloads/list)
  1. 如何使用？
    * 快速指南
```
①【可选】安装 BusyBox Google Play
导入 GAEProxy 根证书，详细请见 教程
②部属自己的 Wallproxy Fetch Server ,详情请见：项目主页
③下载并安装 GAEProxy 最新版。
④填入你的 appspot 地址，形如：https://app-id.appspot.com/fetch.py
⑥勾选 “开启服务”，浏览互联网。
```
    * gaeproxy有关问题FAQ
```
1. 是否一定需要 ROOT 权限？
    一定需要
2. 是否一定需要 iptables 模块？
    一定需要。
3. 如何确认自己的 ROM 支持 iptables？
    一般来说，只要系统原生支持 tether 的都支持 iptables。手动确认的方法是在 adb 下，执行 “iptables –L –t nat”，若提示找不到 module 则可以认为是不支持 iptables。
4. 是否 ROM 中包含名为 iptables 的可执行文件，即可以认为是支持 “iptables”？
    准确的来说，应该是需要 ROM 的 kernel 中编译进了 Netfilter / NAT 模块，并至少支持 DNAT 和 REDIRECT 两种转发模式中的一种。一些第三方 ROM 会裁剪掉 iptables 相关的模块，却又提供了 iptables 的二进制文件。
5. 为什么播放 Youtube 总是断流？
    GAEProxy 与 Youtube 官方客户端存在一定的兼容性问题，表现为无法播放，或播放到一半处出现卡顿或断流。作为一个临时的解决方案，请直接在浏览器中访问 Youtube 移动页面，经测试，大部分网页中的 Youtube 视频都可以正常播放。
6. 为什么会无法访问一些 https 网站？
    两种情况会导致访问 https 网站出错：1. 未导入 GAEProxy 的根证书；2. DNS 污染。对于第一种情况，只需要按照说明导入根证书即可。
    而第二种情况则较为复杂，由于 Android 系统没有开放刷新 DNS 缓存的接口，一旦被 DNS 污染，需要尝试开启/关闭飞行模式，或直接重启系统。通过开启「自动启动」选项，可以确保手机的 DNS 解析处在被保护状态。
7. 为什么无法访问 Twitter 或使用 Twitter 的官方客户端？
    Twitter 移动页面及客户端对于 Google App Engine 做了并发限制，具体表现为访问 mobile.twitter.com 出现 403 错误。可以通过使用第三方 Twitter 客户端来解决此类问题，推荐 Twicca、Twidere 及 Tweedle 三款客户端。
8. 为什么无法在某些局域网环境中使用？
    GAEProxy 不支持二级代理，并需要确保此网路可以访问到公开的 DNS 服务器。因此， GAEProxy 无法在一些典型的公司网络和 WAP 类型的移动网络中使用。
9. 升级到新版本后，为什么会无法使用？
    每次版本更新后，由于测试不充分等原因，确实有可能引入一些 Bug。遇到类似情况，请前往项目网站下载老版程序重新安装，以确认遇到的问题是否是由更新造成。另外，建议每次大版本号更新时，请卸载之前版本再重新安装。
10. 用泰坦备份或其他备份工具恢复 App 后为什么无法使用？
    请不要使用备份工具，备份和恢复 GAEProxy，因为会破坏数据区的执行文件和权限，造成 App 无法正常运行。
11. 为什么开启代理之后依然无法 ping 通一些网站？
    由于我们并没有转发 UDP 流量，因此也不会转发 ICMP 数据包。在无法直接连通的情况下，自然也无法正常 ping 到对方服务器。然而，无法 ping 通服务器并不代表无法与其通信。
```
```
本段内容引用自[https://code.google.com/p/gaeproxy]有改动
```
  1. 请问如何手动升级GAEproxy 2.x.x apk的内核为 GAEProxy 最新版 + WallProxy 最新版？
    * 升级wallproxy
```
    如果 WallProxy 电脑版升级后, 将新版 WallProxy 的 src.zip 目录打包放到 SDCard 的 WallProxy 目录即可, 配置文件 Proxy.ini 请谨慎修改.
```
    * 升级gaeproxy
```
    自己拿apktool解开apk和新版gaeproxy的apk，对照修改，改完编译为apk，请注意apk文件需要重新签名(不建议新手修改，较为复杂)
```
  1. 如何自定义配置
```
    SD 卡可用时将把配置文件放到 SDCard 的 WallProxy 目录, 以便自己修改; 同时可通过 http://127.0.0.1:8086 访问 WEB 配置界面.

    会根据 GAEProxy 配置覆盖 [listen]port, [gae]enable, [pac]enable 等参数, 如需自定义将被覆盖的参数, 以如下形式设置, 例如:


[gae]
;第一个 AppID (下一行) 会被覆盖
appid = appid
;实际生效第二个 AppID (下一行) 的设置
appid = appid1|appid2

    可以示自己需要修改(最好通过网页界面修改，防止乱码)
```
  1. 如何开启智能代理？
```
    如果在gaeproxy配置界面中 AppID 以 "8086-" 开头 (例如 8086- 或 8086-appid1|appid2), 将启用智能代理.

    注意：需要关闭gaeproxy的国内路由功能，否则智能代理功能无效。
```
  1. 也可以通过 PC 为手机提供代理, 方法如下:
```
01. 有些 Android 的 Wifi 自带 http 代理设置功能, 但往往仅针对 ROM 内置的浏览器有效 (受 Android 本身的限制, 如需要代理可以全局作用于所有程序, 请安装 TransProxy v3.8b, 注意, 该程序需要 ROOT 权限, 否则无效); iPhone 的 Wifi 选项自带 http 代理或 PAC 模式, 且设置后可原生作用于所有程序;

02. 修改电脑上 Proxy.ini 中的 listen 地址为 0.0.0.0, 检查 pac 一节 enable 为 1 且 file 前面有个英文分号 ";", 然后电脑上运行 WallProxy;

03. 将手机上代理设置指向 "电脑的 IP:8086" 作为 http 代理, 这样就可以给手机提供代理了, 而且还是该走代理的走代理, 不该走代理的不走代理.(当然也可以用8087作为代理端口使用，则不使用智能代理功能)
```
  * android2.2系统无法使用？
```
gaeproxy项目通告

2.2 及以下系统请使用 0.19.1 或之前的版本。从 0.20.X 版开始，仅支持 2.3 以上系统。

由于目前wallproxy for android使用的gaeproxy0.20.2不兼容android2.2导致无法使用，请用户关注
```
> > https://code.google.com/p/wallproxy/issues/detail?id=713
```
等待作者更新
```

---

编写者：www.ehust，wwqgtxx