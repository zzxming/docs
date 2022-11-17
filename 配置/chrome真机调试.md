# 对ios-safari移动端的H5页面进行调试（ios-webkit-debug-proxy）

# 安装 scoop

查看PowerShell版本，低于5.1需升级。
```
Get-Host | Select-Object Version
```

修改执行策略
```
set-executionpolicy unrestricted -s cu
```

安装`scoop`
```
iex (new-object net.webclient).downloadstring('https://get.scoop.sh')
```

# 安装 ios_webkit_debug_proxy
```
scoop bucket add extras
scoop install ios-webkit-debug-proxy
```

# 安装 remotedebug-ios-webkit-adapter
```
npm install remotedebug-ios-webkit-adapter -g
```

# 在 iOS 设置 Safari 中启用远程调试
```
设置 => Safari 浏览器 => 高级 => web检查器 => 启用
```

# 打开iTunes，数据线链接手机

iphone连接上PC后，会有“是否信任该计算机”的提示，都按确认，iTunes也将会检索到该iphone设备。

# windows命令行运行

```
remotedebug_ios_webkit_adapter --port=9221
```
如果运行完上面的命令还是空白页面，就再开一个命令行运行下面命令

运行命令后会显示`listing devices on :9221`，链接手机会显示`Connected :9222 to iPhone (f4b9c8d4169042313862235fb3c6d7a4aa50677b)`

```
ios_webkit_debug_proxy -f chrome-devtools://devtools/bundled/inspector.html
```

参考文章和视频中监听的是 9000 端口，但我监听 9000 端口会出现 inspect 页全空白的情况，监听 9221 端口就正常了


之后打开 chrome 进入 `chrome://inspect/#devices`，点击 Port forwarding，增加监听端口 `9221` ，url `localhost:9221`

在 Remote Target 会出现当前手机打开的页面，点击 `inspect` 即可查看手机页面

如果出现 `HTTP/1.1 404 Not Found` 则需要开一下 vpn，因为链接不上 google

如果还是空白，重新开几次就好

<br>

# 使用安卓模拟器进行调试（mumu模拟器）

在安装完成 mumu模拟器 后，根据安装路径找到 `bin` 文件夹下的 `adb_server.exe`，使用 cmd 进入对应路径，运行下面命令使 mumu模拟器 与 pc 进行连接

windows 下的端口是 7555，mac 会有不同，不同模拟器的端口也不同
```
adb_server.exe connect 127.0.0.1:7555
```

进入 mumu模拟器，进入设置->开发者选项->USB调试，将其打开，然后回到 chrome，进入 chrome://inspect/#devices 可以看到 mumu模拟器 浏览器打开的页面，点击 inspect 即可开始调试



# 参考

文章：
* [【Chrome】Chrome-devtools：对ios-safari移动端的H5页面进行调试（ios-webkit-debug-proxy）](https://juejin.cn/post/6844903629762068494)

* [移动端调试（ios手机safari+chrome调试(windows)）](https://blog.csdn.net/yanzhitaipi/article/details/79446772)

* [Windows 使用 Chrome 调试运行在 iOS 设备的网页](https://www.zhangbj.com/p/971.html)，这个流程是测试真的可以链接成功的

视频：
* [windows系统下真机调试ios H5移动端项目](https://www.bilibili.com/video/av677366508)，流程视频

* [web移动端页面的真机调试方法](https://www.bilibili.com/video/BV1944y1e762)