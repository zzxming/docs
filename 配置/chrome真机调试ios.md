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

参考文章和视频中监听的是 9000 端口，但我监听 9000 端口会出现 inspect 页全空白的情况，监听 9221 端口就正常了
```
remotedebug_ios_webkit_adapter --port=9221
```

之后打开 chrome 进入 `chrome://inspect/#devices`，点击 Port forwarding，增加监听端口 `9221` ，url `localhost:9221`

在 Remote Target 会出现当前手机打开的页面，点击 `inspect` 即可查看手机页面


# 参考

文章：
* [【Chrome】Chrome-devtools：对ios-safari移动端的H5页面进行调试（ios-webkit-debug-proxy）](https://juejin.cn/post/6844903629762068494)

* [移动端调试（ios手机safari+chrome调试(windows)）](https://blog.csdn.net/yanzhitaipi/article/details/79446772)

* [Windows 使用 Chrome 调试运行在 iOS 设备的网页](https://www.zhangbj.com/p/971.html)，这个流程是测试真的可以链接成功的

视频：
* [windows系统下真机调试ios H5移动端项目](https://www.bilibili.com/video/av677366508)，流程视频

* [web移动端页面的真机调试方法](https://www.bilibili.com/video/BV1944y1e762)