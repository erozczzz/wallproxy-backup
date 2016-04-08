## FAQ ##

  * 参数含义问题
    1. https\_mode 含义
      * https\_mode = 0: 对于 https 全走直连, 可以有正常的证书, 但无法访问 https://twitter.com 等网站;
      * https\_mode = 1: 对于 https 全走代理, 证书全是伪造证书, 可以访问 https://twitter.com 等网站;
      * https\_mode = 2: 对于 https 通过域名构造一个 url 去判断是否需要代理, 证书部分正常部分伪造, 因为又多了一次判断会更慢一点点, 而且可能遗漏部分需要走代理的网站.
    1. find\_handler 含义
      * 如果 find\_handler = 0, 在 8087 端口上不做任何判断, 直接转 GAE, 包括 `YouTube` 的直播等;
      * 如果 find\_handler = 1, 在 8087 端口上做 `YouTube` 直播等判断, 但不判断 gfwlist 规则.
    1. max-threads 值设为 0 或 1 或其他值有何区别?
      * 设为 0 时最大线程数是 AppID 个数与 10 中较小者, 设为1时是单线程, 设为其他值时是 AppID 个数与设置值中较小者.
    1. `[gae]fetch_mode` 说明?
      * fetch\_mode = 0: 时平滑缓冲 (边读边写), 但无法确保稳定性；
      * fetch\_mode = 1: 全部读完再写；
      * fetch\_mode = 2: 对分段下载读完再写, 其它边读边写. 1 和 2 皆无法保证平滑缓冲.
    1. `[pac]`段设置
```
[pac]
enable = 1
;file = proxy.pac
rulelist = http://autoproxy-gfwlist.googlecode.com/svn/trunk/gfwlist.txt|userlist.ini
```
      * enable为0：不更新PAC，不使用智能代理（与8087端口区别是可以看`YouTube`直播等）
      * enable为1且file没有被;注释，生成PAC，不使用智能代理
      * enable为1且file被;注释，不生成PAC，使用智能代理（与PAC效果相当，因为是wp内部判断，所以手机等不支持PAC的设备也可以做到该走代理的走代理，不该走的不走）
      * rulelist：生成PAC/智能代理的列表（会在misc/pac\_backup.dat留备份）
      * 如果需要在开启智能代理同时用PAC，应该使用8087端口上的http://127.0.0.1:8087/proxy.pac，并且最好将`[gae]`find\_handler设为1
    1. 其他内容参见 `WallProxy` 的 proxy.ini 里的注解说明, 若有疑问可以到 [Issues](https://code.google.com/p/wallproxy/issues/) 中提出.

  * 运行使用以及概念方面的问题
    1. 访问 https 网站提示证书无效/警告?
      * 由于GAE的限制，只能使用伪造证书来支持https，将CA.crt导入为根证书，以后遇到https链接就不会弹出证书无效的警告了。首次运行程序后会生成cert/CA.crt（直接提供根证书存在严重安全隐患，所以wallproxy默认不提供，需要首次运行后自动生成，注意升级新版本时请保留此文件，以免需要重新导入），导入后最好将以前添加例外的网站全部删除以避免冲突。
      * 证书导入方法：
        * Windows下IE等浏览器：工具->Internet选项->内容->证书->受信任的根证书颁发机构->导入->选择CA.crt文件->依次确认；
        * Firefox依次操作：首选项->高级->加密->查看证书->证书机构->导入->选择CA.crt文件->选中所有选项->确定；
        * Linux下Chrome按照https://code.google.com/p/chromium/wiki/LinuxCertManagement 操作。
        * OPERA导入证书方法：首选项→高级→安全性→管理证书→证书颁发机构->导入->选择CA.crt文件->依次确认；
        * Mac OS下safari添加方式：
          1. 实用工具－>钥匙串访问->菜单栏“文件”－>导入项目(目的钥匙串：系统)需要输入密码
          1. [ 最后两步可以等价于：将证书直接拖拽至“系统”钥匙串目录下，需要输入密码］
          1. 导入成功后找到“`Wallproxy CA`”，右键“显示简介”，展开信任，选择“总是信任”
          1. 除此之外，还需要依次导入了cert/cert下所有证书，此时才不会出现''证书信任警告'，所有网页显示正常。
      * 每次版本更新时有两个选择:
        * 将上次导入的根证书删除,再把新的根证书添加进去.
        * 直接将上一个版本的cert文件夹复制到新版本目录中去.
      * 检查是否导入正确：
        * IE：在“受信任的根证书颁发机构”按W定位到“`WallProxy CA`”，双击->详细信息->注意“有效期从/到”时间与控制台提示是否相同
        * Firefox：在“证书机构”按W定位到“`WallProxy CA`”，双击->详细信息->注意“不早于/不晚于”时间与控制台提示是否相同
      * [IE 导入无效?](http://code.google.com/p/wallproxy/issues/detail?id=52#c4)
      * [判断导入是否正确了](https://code.google.com/p/wallproxy/issues/detail?id=51#c2)
      * 升级版本不想重新导入证书, 请保留之前版本的 cert 目录, 以免重新导入根证书.
    1. 如何防止你的Appid被匿名使用(盗用)？
      * 目前`Wallproxy`最新版是支持的，注意:本功能不会影响网速，请放心使用。
      * 注意：此密码只是作为反盗用使用，不应该设置你的邮箱密码等重要密码，因为密码只是简单的编码传输，并且只作为判断是否被盗用的依据，不同与旧版作为加密密匙使用，使用总要密码可能会增加密码泄露的问题，是您的账户安全性降低。
      * 配置方法：
        1. 先在server\python\wsgi.py文件中开头修改：
```
__password__ = '你想要设置的密码'
```
        1. 重新上传服务端。
        1. 然后在proxy.ini中的`[gae]`段落下如下这样配置即可。
```
password = 你刚才设置的密码(不包括双引号)
```
    1. 为什么有些时候需要php模式？如何使用php模式？
      * GAE的访问能力有限，只能提供美国加利福尼亚洲山景城谷歌公司的ip地址，所以不能访问部分网站，同时有一些网站也限制GAE对其的访问，所以有些时候需要使用php模式来摆脱这些限制。
      * (如部分日本网站只允许日本本国ip访问，不允许GAE访问，此时如果要访问这些网站就需要使用php模式)
      * 使用方法：
        1. 申请一个免费的php空间，然后通过在线代码编辑器或者ftp客户端把server/php/index.php文件上传到你申请到php网站的根目录。假设为http://wallproxy.php.com/index.php
        1. 访问你的index.php地址，比如`http://wallproxy.php.com/index.php`，如果没有问题的话，说明部署成功。
        1. 编辑proxy.ini`[paas]`字段 enable=1和fetchserver=你的index.php地址，重启`Wallproxy`即可。
        1. 还可以把`[paas]`的proxy字段设置为 127.0.0.1:8087 ，使用GAE加速php访问，防止你的php空间被封。
      * 图文教程暂时缺少，欢迎大家补充。
    1. 访问Google网站提示“网站的名称与证书上的名称不一致”?
      * Google 很多站点都是可以通用 IP 的, wallproxy会从一些Google IP中选择一个较快较稳定的IP用于这些站点（例如`GAE、GMail、搜索`等），由于[SNI](http://en.wikipedia.org/wiki/Server_Name_Indication)的存在，并不会出现证书问题；但也有部分浏览器不支持`SNI`（例如XP下的IE、部分手机浏览器等，可通过访问https://alice.sni.velox.ch/查看浏览器是否支持），不支持时将出现“网站的名称与证书上的名称不一致”警告，这时有以下几种方法来消除该警告（任选一种即可）：
        * 在proxy.ini中将`[google_hk]truehttps`设为空（可能最保险的改法是把truehttps的内容设置到notruehttps）
        * 在proxy.ini中将`[google_hk]sites`设为空（无法加速Google站点访问，不建议）
        * 在userlist.ini或浏览器扩展中将Google站点不走wallproxy（无法获得良好上网体验也无法加速Google站点访问，不建议）
        * 在浏览器设置中忽略证书不匹配错误（例如IE取消“对证书不匹配地址发出警告”，Chrome加“--ignore-certificate-errors”参数启动，严重影响安全性，不建议）
    1. 如何开机启动?
      * 加入开始菜单的启动文件夹开机运行即可.
      * 建议方法: proxy.exe 右键菜单选择 "发送到桌面快捷方式", 生成的桌面快捷方式上右键选择 "属性", 在目标后面加上 --hide --single, 如果觉得图标太难看可以顺便 "更改图标", 例如换成 GUI 的. "确定" 后复制该快捷方式到 "开始" 菜单的 "启动" 文件夹.
      * 或者直接通过使用 [GUI](https://code.google.com/p/wallproxy/issues/detail?id=49#c1) 来完成设置.
    1. 智能代理, 全局代理和 PAC 有什么区别?
      * 智能代理：由 `WallProxy` 判断是否需要走 GAE, 不需要的通过 `WallProxy` 进行本地转发走直连或代理 (如果设置了), 不消耗 GAE 的流量.
      * 全局代理: `WallProxy` 不做任何判断所有都走 GAE, 会消耗 AppID 的流量.
      * 使用智能代理或全局代理时, 可以自行通过访问 http://www.ip138.com 查看 IP 变化的区别.
      * PAC: 由浏览器判断访问的 URL 是否需要走代理 (根据 GFWList 和用户自定义规则判断), 该功能需要浏览器支持 (Android 不支持, iOS 支持).
    1. PAC地址之间的区别？
      * 下面说明均是在默认配置下的情况
      * 使用http://127.0.0.1:8087/proxy.pac会在需要代理时走127.0.0.1:8087的GAE；
      * 使用http://127.0.0.1:8086/proxy.pac会在需要代理时走127.0.0.1:8086，如果没有开启智能代理，效果与8087端口基本相同，如果开启了智能代理，会重复判断，所以建议使用上面的地址；
      * 使用`http://外部ip或域名:8087/proxy.pac`，会在需要代理时走 外部ip:8087 的GAE；
      * 也就是说只要能够访问到pac文件，代理地址就是正确的，即使监听地址或ip变化了，也不需要修改pac配置。
    1. 多目标 PAC/智能代理设置
      * [多目标PAC/智能代理设置 1](https://code.google.com/p/wallproxy/issues/detail?id=45#c1)
      * [多目标PAC/智能代理设置 2](https://code.google.com/p/wallproxy/issues/detail?id=42#c10)
      * [多目标PAC/智能代理设置 3](https://code.google.com/p/wallproxy/issues/detail?id=37#c23)。
    1. 与 `GoAgent` 的区别?
      * 服务端目前通用, 可直接使用 `GoAgent` v1.10.0 以上版本的服务端, 包括最新的 `GoAgent` v2.x 的服务端, 并无兼容性问题.
      * 区别主要在本地端, 深度加强和优化了很多功能, 特别是看 `YouTube` 的性能.
      * 可以这样理解: `WallProxy` = `GoAgent` + `CCProxy` + `AutoProxy` + ...
    1. 为什么要写old插件？
      * 有些人认为新版`wallproxy`是基于`goagent`的，old插件从开始写到测试完毕到写出基本可用的ini转py代码只用了半天时间，因为新旧版`wallproxy`是一脉相承的，有着基本相同的架构，将旧版插件改写为新版只需要稍作修改；
      * 另一方面，这次的old.py没有进行任何处理，可以作为对于如何编写插件的一种示范，如果有人要编写兼容`APJP`、`Sogou`之类的插件，可以对照进行；
    1. 新版`Wallproxy` 的编写目的？
      * `Wallproxy`从1.0开始采用框架+插件的形式，本来是希望有人能编写相应插件的；但最初的`goagent`则主要基于gapp加从wp吸取的伪造CA和分段下载。
      * 为了修复原有框架中存在的一些bug并实验一种隐藏源码的方式，wp2.0就出来了，原wp中`ProxyServer.py`、util/httpheaders.py、util/proxylib.py、util/urlfetch.py、util/urlinfo.py的内容整合到了现有的proxy.py中，原有的wpconfig.py和proxy.conf对应现有的config.py，原有的plugins/fakehttps.py改为了proxy.py中的固有功能，plugins/autoproxy.py对应内置插件pac，plugins/rawproxy.py对应内置插件util.Forward。
      * 从复活目的就可以看出wp重心并不在服务端，加上goagent用户多，为了尽快弄个大多数人都可以很方便上手的东西出来，并且可以利用广泛存在的goagent服务端，之前就没有采用原有的plugins/gaeproxy.py插件，而是去兼容goagent，对应于新版plugins.py；为了兼容goagent的配置文件，就弄了个make\_config.py来进行配置文件转换。
      * plugins.py有些东西确实是基于goagent编写的，但它只是新版wp的一个可选部分。
      * old.py正是为兼容旧版而生，完全是自主设计和实践的。但正如名字所示，不推荐继续使用，因为存在大量过时的限制和bug.
    1. 是否支持根据国内 IP 或者教育网 IP 选择代理?
      * 支持, 详见 [PAC 设置方式](https://code.google.com/p/wallproxy/issues/detail?id=37).
    1. 支持作为 http/https/Socks 4/Socks 5 使用是什么意思? 能否为 QQ 进行代理?
      * 因为默认情况下会把 Socks 4/Socks 5转为 http/https 的请求 (性能当然还是直接设为 http 好些), 这种转换要求代理目标是 http 协议的. 而 QQ 不是 http 协议的, 不能使用这种转化, 必须显示转给支持 https (非伪造证书的 https)/Socks 代理/其他支持 Socks 转发的服务端进行处理
      * GAE 是受限的代理, 只能代理 http, 而 https 则采用伪造证书将 https 转为 http 来实现; GAE 不支持作为 https/Socks 4/Socks 5 使用. 如果你有 Socks 4 代理/Socks 5 代理/能够访问 https 网站且证书不是伪造证书的 http 代理三者中的一种, 则 `WallProxy` 可以在这三者中进行转换或者转为 http 代理, 反之纯 http 而 https 需要伪造证书的不能转换为上面三种情况.
      * 不可以为 QQ 代理, 因为 `WallProxy` 不支持 https/Socks: 例如 PC 1 能上网, PC 2 不能上网, 可以在 PC 1 上运行 `WallProxy` (不启用 GAE/PAAS 服务端), 在 PC 2 上设置使用 PC 1 作为浏览器/QQ 等程序的代理, 从而使 PC 2 也能上网; 但是 GAE 不支持 https (非伪造证书)/Socks, 所以通过 QQ -> `WallProxy` -> GAE 来上 QQ 是不可能的.
    1. talk.google.com 错误解决方法
      * GAE 只能作为 http (以及伪 https) 代理使用, Chrome 在启动时会用 https 代理去连接 talk.google.com:5222, 但所用的并非是 https 协议的, 走不了 "GAE 代理", 选择 "智能代理" 或者 "PAC" 就能让这个请求不走 GAE 从而不出现错误.
    1. 上传多个服务端有时候要输入多次帐号和密码?
      * Cookies 文件有问题, 删除 server 文件夹下的 .appcfg\_cookies 后重新操作.
    1. 如何禁用 GUI 的代理切换功能或者退出 GUI 后恢复无代理?
      * [图文解释](https://code.google.com/p/wallproxy/issues/detail?id=29#c1)
    1. 纯 IPv6 复杂环境设置方式?
      * [参考文档](https://docs.google.com/document/d/1gyOvYBsEFskfk_YHubYMF0ZXKgWO9rf6cVOIPj765xg/edit)
      * 建议联系 rpzrpz123#gmail.com 详问.
      * [也可以在本项目 Issue 中反馈, 标题加上 <纯 IPv6 问题>](https://code.google.com/p/wallproxy/issues/entry)
    1. 运行依赖的 Python 库?
      * [参考](https://code.google.com/p/wallproxy/issues/detail?id=30#c4)
      * 首页也有说明
    1. `WallProxy 看 YouTube` 直播的原理?
      * 直播不支持分段下载，不可能通过GAE观看。
      * 所以新版wallproxy通过解析真实IP 并通过发送 crlf 连接防止被 reset的方式看直播
    1. 访问 google.com 禁止跳转到 google.com.hk?
      * 访问 http://www.google.com/ncr, 即可解决问题
    1. 访问twitter,自动跳转为mobile.twitter.com并返回403 Forbidden，如何解决？
      * 由于mobile.twitter.com对GAE限制流量，所以需要使用电脑版twitter登陆并使用
      * 检查查自己的id是不是以android开头或者apple,iphone,ipad啥的开头的了，总之不要使用有移动性质的单词作为appid的一部分。
    1. 配置多appid有什么用？
      * 最新版的GAE服务端已经是python27，所以多appid现在只是起到增加流量配额的作用。
      * 提高并发的作用已经被GAE内置的功能取代了。(同时也可以防止大量并发导致Appid被google封)
      * 在下载文件和看电影的时候用于多线程下载，提高速度。(appid在多线程时随机选择，无法更改)
    1. 支持gfwlist么？可以自定义list么？
      * 完全支持。
      * 实现gfwlist请使用8086端口的智能代理，或PAC文件实现gfwlist智能判断的功能。
      * 自定义list可以编辑/local/misc/userlist.ini文件，参考里面的已有的配置编写。
      * 当然，也可以通过浏览器插件autoproxy/switchysharp来实现支持gfwlist。(插件请设置代理端口为8087防止重复判断)
    1. 如何筛选合适的 hosts?
      * [方法](https://code.google.com/p/wallproxy/issues/detail?id=26#c18)
    1. 与旧版等效的 onlyone 参数 (对某些网站只使用一个 AppID)?
      * `WallProxy` 除看电影或下载文件之外, 只使用单 AppID 模式
      * 也就是说onlyone参数默认打开，且无法关闭。
    1. 为什么我使用的多个Appid只有第一个(或前几个有流量)？
      * 多appid单线程（例如访问网页）只用第一个，流量用完才换下一个；多线程（例如看视频）会用到其他appid，所以如果只访问网页的话，后面的在appengine一般是看不到访问记录的。
    1. 需要一个自动代理的 PAC 和一个搜狗代理 + 自动代理的 PAC?
      * [设置方法](https://code.google.com/p/wallproxy/issues/detail?id=612)
    1. 如果使用了两步验证, 上传 `WallProxy` 到 Google App Engine 时的密码?
      * 只要在https://accounts.google.com/b/0/IssuedAuthSubTokens 申请一个程序专用密码 使用这个密码来配合gmail账户上传部署服务器就好了。（账户不变，密码使用此专用密码）
    1. 我申请Appid名字输入错误，还可以在GAE删掉这一个Appid么？(或因为别的原因想要删除原来的Appid)
      * 可以的。
      * 步骤如下：
        1. 请先打开应用管理页https://appengine.google.com
        1. 进去要删除的应用-->Administration-->Application Settings-->Disable or Delete Application(位置偏下的，现实只能Disable Application的，接着才能删除应用的）
        1. Google为了防止有人还在使用此应用会提醒24小时的备份或转移时间的。
        1. 24小时后，你的应用就会被删掉的。
      * 如果只是删除应用的程序，不需要这样的，只要再上传一次，可能覆盖，或同时存在，只要不是默认的程序，就可以被删掉的（位置进去要删除的应用-->Main-->Versions）
    1. 为什么现在 Google 相关站点不再经由 GAE 代理而是走的 FWD? 控制台显示这些又都分别代表什么意思?
      * Google 很多站点都是可以通用 IP 的, 而且通过 https 访问, 外界 (如 GFW) 根本不知道实际访问的地址! G+, Google Drive 都可以通过 www.google.com 的 IP 访问, 这也是为什么即使 GAE 被封了但能通过 www.google.com 的 IP + https 在国内使用的根本原因; 既然 G+, Google Drive 都能够通过 IP + https 的方式直接访问 <> 目标站点, 那当然就没必要通过 IP + https <> GAE <> 去访问目标在站点, 这样既能提高速度, 又能节约流量, 何乐而不为?
      * 控制台显示主要有 FWD, GAE, WP, PAAS 和 OGA;
      * 如果用智能代理 (8086 端口), 最常见的是 FWD, GAE 和 WP, 若配置了 PAAS, 则会有 PAAS, 配置了 old 插件后会有 OGA;
      * FWD 是根据配置文件转发给下级代理 (前提是设置了其它外部代理) 或 hosts (匹配配置文件中的 hosts 规则. 这样由 hosts 处理, 不走 GAE (可省 AppID 的流量, 同时可以避免一些 Google 的站点走 GAE 后验证码出现), 或直接连接 (不匹配任何规则);
      * GAE 是匹配了 GAE 规则, 如默认的 gfwlist 及 userlist;
      * WP 代表由 `WallProxy` 处理后返回给浏览器, 如匹配的强制 https 规则.
    1. Windows下上传出现：”'ascii' codec can't decode byte 0xca in position”错误怎么处理？
      * 这是一般是因为在你windows注册表这个路径下有非ASCII的key：HKEY\_CLASSES\_ROOT\MIME\Database\Content Type
      * 解决方法：
        1. 在键盘上使用快捷键“win + R”打开运行对话框。
        1. 输入regedit再回车。
        1. 进入HKEY\_CLASSES\_ROOT\MIME\Database\Content Type 。
        1. 把非ASCII的KEY都删掉。
        1. 再重新上传。
    1. 如何查看正在运行中的`Wallproxy`客户端版本号？
      * 方法一：查看控制台输出：
```
------------------------------------------------------------------------------
Welcome to use wallproxy, Copyright (C) 2009-2012 HustMoon Studio
Version: wallproxy/2.1.13 (python/2.7.3, gevent/1.0rc2)
         ~~~~~~~~~~~~~~~
Listen : 127.0.0.1:8086
------------------------------------------------------------------------------
```
      * 方法二：开了 `WallProxy` 后, 在浏览器里直接访问 http://127.0.0.1:8086, 然后进入 `[状态]` 即可查看版本

  * 有关项目本身的问题
    1. 项目的各个成员分别是做什么的？
      * 可以见这里：https://code.google.com/p/wallproxy/people/list。有详细的说明
    1. `GoogleCode`上面的`wallproxy-plugins`和`wallproxy-plus`项目是做什么的？
      * 这是以前的 Project Home, 现在都迁移到这里来了，也就是说那些项目均是无用的过时项目，不用再访问了。
    1. Issues 中的条目为何越来越少? 以前的一些 Issues 如何查找?
      * 选择 All Issues 后, 再点击 Search (也可以通过写上关键词搜索你需要的 Issues)
      * [图解](https://code.google.com/p/wallproxy/issues/detail?id=649)




---

编写者：www.ehust，wwqgtxx，heeroyuyj