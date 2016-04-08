

---

## 第一步：下载wallproxy，运行wallproxy ##
  1. 下载wallproxy并解压；最新地址：https://code.google.com/p/wallproxy/
  1. 运行local文件夹下`WallProxy.exe`或者Run.bat（非Windows用户运行startup.py，Windows若提示是否允许安装证书，请允许），可显示下图：
> > ![http://wallproxy.googlecode.com/git/wiki/Tutorial.files/image011.png](http://wallproxy.googlecode.com/git/wiki/Tutorial.files/image011.png)

## 第二步：配置浏览器代理 ##
  1. 设置代理地址127.0.0.1:8087；如需使用PAC，设置http://127.0.0.1:8087/proxy.pac；如需使用`SwitchySharp/AutoProxy`等浏览器扩展（`SwitchySharp用户可导入配置local\misc\SwitchyOptions.bak`），见图文教程（GUI应该选择“禁用切换”）；如需使用智能代理（使无法使用PAC或扩展的程序也做到该走代理走代理，不该走就不走），设置127.0.0.1:8086为代理即可。
  1. 导入http://127.0.0.1:8086/CA.crt为浏览器根证书可消除浏览器证书警告（cmd窗口提示时间与导入后查看到的时间相同基本就是导入成功了，升级版本时请保留cert目录，以免需要再次导入）
  1. 可通过http://127.0.0.1:8086或http://wallproxy访问Web配置界面
  1. 双击托盘图标，可出现控制台窗口，注意不要直接关闭该窗口，正确的方法是再次双击托盘图标来隐藏。
> > ![http://wallproxy.googlecode.com/git/wiki/Tutorial.files/image012.jpg](http://wallproxy.googlecode.com/git/wiki/Tutorial.files/image012.jpg)
> > FWD说明走了转发（没走代理或走了hosts规则，不消耗GAE流量），GAE说明走了GAE，PAAS说明走了PAAS(PHP)。
  1. 托盘左键菜单用于配置代理，右键菜单配置程序。
> > ![http://wallproxy.googlecode.com/git/wiki/Tutorial.files/image013.png](http://wallproxy.googlecode.com/git/wiki/Tutorial.files/image013.png)
    1. 直接连接：强制浏览器不使用代理；
    1. GAE代理：强制浏览器使用127.0.0.1:8087作为代理（除部分google网站外，大部分网站都走GAE）；
    1. 智能代理：强制浏览器使用127.0.0.1:8086作为代理，由wallproxy智能判断是否需要走GAE，如果直连失败，可自动改走GAE；
    1. 自动代理：由浏览器通过PAC判断是否需要走GAE，与智能代理相比性能损失小，但无法做到在直连失败时改走GAE；
    1. 禁用切换：强制IE不使用代理；以上4个选择在每次运行`WallProxy.exe`时都会去修改IE代理为所选择项，而选择“禁用切换”后下次运行不会对IE代理做任何修改。**使用`SwitchySharp/AutoProxy`等浏览器扩展管理代理的用户，请选择此项** 。
  1. 如果需要自定义这些菜单，可以选择“设置代理”菜单项：
> > ![http://wallproxy.googlecode.com/git/wiki/Tutorial.files/image014.png](http://wallproxy.googlecode.com/git/wiki/Tutorial.files/image014.png)
> > 勾选“退出时恢复无代理”可在退出`WallProxy.exe`时将IE代理修改为无代理，以免影响正常使用；勾选“禁用代理切换”效果类似于“禁用切换”菜单。
> > 如果使用如图所示的拨号上网方式，将连接名称填入上图“连接名称”，例如下图“连接 宽带连接”，将“宽带连接”填入即可。
> > > ![http://wallproxy.googlecode.com/git/wiki/Tutorial.files/image015.jpg](http://wallproxy.googlecode.com/git/wiki/Tutorial.files/image015.jpg)
  1. 打开浏览器输入：http://www.facebook.com，并成功访问。

> > ![http://wallproxy.googlecode.com/git/wiki/Tutorial.files/image016.jpg](http://wallproxy.googlecode.com/git/wiki/Tutorial.files/image016.jpg)
  1. 自定义代理规则，访问http://wallproxy/或者http://127.0.0.1:8086/，再点击“自定义规则”，输入要走GAE的规则后保存即可：
> > ![http://wallproxy.googlecode.com/git/wiki/Tutorial.files/image017.jpg](http://wallproxy.googlecode.com/git/wiki/Tutorial.files/image017.jpg)

## 第三步：向浏览器导入证书 ##

> ### 配置方式一：`Firefox + WallProxy` ###
    1. 使用系统代理
> > > ![http://wallproxy.googlecode.com/git/wiki/Tutorial.files/image019.jpg](http://wallproxy.googlecode.com/git/wiki/Tutorial.files/image019.jpg)
    1. 导入根证书
> > > ![http://wallproxy.googlecode.com/git/wiki/Tutorial.files/image020.jpg](http://wallproxy.googlecode.com/git/wiki/Tutorial.files/image020.jpg)
> > > ![http://wallproxy.googlecode.com/git/wiki/Tutorial.files/image021.png](http://wallproxy.googlecode.com/git/wiki/Tutorial.files/image021.png)
    1. 检查导入是否正确（可选），时间相同，说明没有导错：
> > > ![http://wallproxy.googlecode.com/git/wiki/Tutorial.files/image022.jpg](http://wallproxy.googlecode.com/git/wiki/Tutorial.files/image022.jpg)
> > > ![http://wallproxy.googlecode.com/git/wiki/Tutorial.files/image023.jpg](http://wallproxy.googlecode.com/git/wiki/Tutorial.files/image023.jpg)

> ### 配置方式二：配合`Chrome + SwitchySharp`使用方法 ###
> > [.md](.md)**GUI左键菜单选择“禁用切换”** ，按下图导入配置，之后“直接连接”、“GAE代理”、“智能代理”、“自动代理”作用与`WallProxy.exe`相同。
> > > ![http://wallproxy.googlecode.com/git/wiki/Tutorial.files/image024.jpg](http://wallproxy.googlecode.com/git/wiki/Tutorial.files/image024.jpg)
> > > ![http://wallproxy.googlecode.com/git/wiki/Tutorial.files/image025.png](http://wallproxy.googlecode.com/git/wiki/Tutorial.files/image025.png)

> ### 配置方式三：配合`Firefox + FoxyProxy`使用方法 ###
> > [.md](.md)**GUI左键菜单选择“禁用切换”** ，如图所示依次添加“GAE代理”127.0.0.1:8087，“智能代理”127.0.0.1:8086，“自动代理”http://127.0.0.1:8087/proxy.pac。
> > > ![http://wallproxy.googlecode.com/git/wiki/Tutorial.files/image026.jpg](http://wallproxy.googlecode.com/git/wiki/Tutorial.files/image026.jpg)
> > > ![http://wallproxy.googlecode.com/git/wiki/Tutorial.files/image027.jpg](http://wallproxy.googlecode.com/git/wiki/Tutorial.files/image027.jpg)
> > > ![http://wallproxy.googlecode.com/git/wiki/Tutorial.files/image028.jpg](http://wallproxy.googlecode.com/git/wiki/Tutorial.files/image028.jpg)

> ### 配置方式四：配合`Firefox + AutoProxy`使用方法 ###
> > [.md](.md)**GUI左键菜单选择“禁用切换”** ，相比之下`AutoProxy`是比个繁琐的代理扩展，如果找不到以下对话框从哪些菜单找出来，还是改用`WallProxy.exe`或者`FoxyProxy`吧。
> > > ![http://wallproxy.googlecode.com/git/wiki/Tutorial.files/image029.png](http://wallproxy.googlecode.com/git/wiki/Tutorial.files/image029.png)
> > > ![http://wallproxy.googlecode.com/git/wiki/Tutorial.files/image030.png](http://wallproxy.googlecode.com/git/wiki/Tutorial.files/image030.png)
> > > ![http://wallproxy.googlecode.com/git/wiki/Tutorial.files/image031.png](http://wallproxy.googlecode.com/git/wiki/Tutorial.files/image031.png)

## 第四步：申请GAE空间并创建新的的app\_id（已有可跳过） ##
  * 因为上面操作均使用默认Appid，到默认Appid流量有限，所以要想获得流畅的用户体验，需要自行申请Appid
  1. 打开刚才配置好的浏览器浏览器，输入https://appengine.google.com/，输入gmail用户密码登入(如果没有gmail账号请前往https://accounts.google.com注册)。

> > ![http://wallproxy.googlecode.com/git/wiki/Tutorial.files/image001.jpg](http://wallproxy.googlecode.com/git/wiki/Tutorial.files/image001.jpg)
  1. 点击“Create an Application”。
> > ![http://wallproxy.googlecode.com/git/wiki/Tutorial.files/image002.jpg](http://wallproxy.googlecode.com/git/wiki/Tutorial.files/image002.jpg)
  1. 申请GAE需要用手机认证，输入自己的手机号(也可以选择通过电话认证，方法类似)，注意前面需要写+86（如无法收到短信验证码，可尝试在+86后加一空格再输手机号）。
> > ![http://wallproxy.googlecode.com/git/wiki/Tutorial.files/image003.jpg](http://wallproxy.googlecode.com/git/wiki/Tutorial.files/image003.jpg)
  1. 手机收到验证码后输入验证，验证成功后GAE申请完成。
> > ![http://wallproxy.googlecode.com/git/wiki/Tutorial.files/image004.jpg](http://wallproxy.googlecode.com/git/wiki/Tutorial.files/image004.jpg)
  1. 创建新app\_id，比如这里我使用了seoceshi，注意记下该app\_id，后面还会再用到。至于app title,可填可不填。
> > ![http://wallproxy.googlecode.com/git/wiki/Tutorial.files/image005.jpg](http://wallproxy.googlecode.com/git/wiki/Tutorial.files/image005.jpg)
  1. 填好以后进入下个页面，如下： 则表示成功。
> > ![http://wallproxy.googlecode.com/git/wiki/Tutorial.files/image006.jpg](http://wallproxy.googlecode.com/git/wiki/Tutorial.files/image006.jpg)

## 第五步：上传至Google App Engine应用 ##
  1. 运行server文件夹下uploader.bat(非Windows用户运行uploader.py)，输入appid上传（一次只能上传同一个帐号下的appid，多appid用|分隔，提示Set Proxy时尽量输入1来提高上传成功率）；
> > ![http://wallproxy.googlecode.com/git/wiki/Tutorial.files/image007.png](http://wallproxy.googlecode.com/git/wiki/Tutorial.files/image007.png)
    1. 如果出现如下提示，删除旧appid，重新申请新appid；
> > > ![http://wallproxy.googlecode.com/git/wiki/Tutorial.files/image008.jpg](http://wallproxy.googlecode.com/git/wiki/Tutorial.files/image008.jpg)
    1. 如果出现如下提示，到https://www.google.com/settings/security停用“两步验证”；或者在https://accounts.google.com/b/0/IssuedAuthSubTokens 申请一个程序专用密码 使用这个密码来配合gmail账户上传部署服务器就好了。（账户不变，密码使用此专用密码）
> > > ![http://wallproxy.googlecode.com/git/wiki/Tutorial.files/image009.jpg](http://wallproxy.googlecode.com/git/wiki/Tutorial.files/image009.jpg)
> > > > 给新手解释，两步验证就是谷歌推出的进阶安全机制，给应用生成专属密码来访问你的账户，避免主密码被盗。
    1. 如果出现如下提示，说明在提示“Set proxy?”时选择了使用代理上传，若当时选择了1，请确保已经运行可以正常使用的wallproxy，若没有可用代理，请在提示“Set proxy?”时输入0或直接回车。

> > > ![http://wallproxy.googlecode.com/git/wiki/Tutorial.files/image010.jpg](http://wallproxy.googlecode.com/git/wiki/Tutorial.files/image010.jpg)
  1. 访问http://127.0.0.1:8086/#proxy_ini，找到如下部分（56行左右）并修改appid = 后面为自己的appid，点右上角的“保存”；
```
[gae]
;是否启用GAE服务端
enable = 1
;服务端appid（多个用|分隔，个数不限）
appid = appid1|appid2
```
  1. 等待重启完毕，即可畅快使用wallproxy给你带来的一切了。

## 第六步：仔细阅读FAQ文档，成为`Wallproxy`高手 ##
  * https://code.google.com/p/wallproxy/wiki/FAQ


---

编写者：www.ehust，wwqgtxx