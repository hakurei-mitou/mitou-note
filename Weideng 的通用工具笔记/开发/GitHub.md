# GitHub

## github1s

将项目 url 的域名作修改：

```
https://github.com/...

# 改为

https://github1s.com/...

其中，https:// 一般是浏览器自己处理，可以省略。
```

即可快速以 VS Code 界面在浏览器中快速阅读项目代码。

对文件只读，不支持自行安装扩展，适合快速浏览。

## github.dev

将项目 url 的域名作修改：

```
https://github.com/...

# 改为

https://github.dev/...
```

即可以 VS Code 界面在浏览器中查看项目代码。

可修改文件，可自行安装扩展，但速度比 github1s 慢。

不可运行命令行终端。

## gitpod

将项目 url 的域名作修改：

```
https://github.com/...

# 改为

https://gitpod.io/#/github.com/...
```

即可以 VS Code 界面在浏览其中启动一个虚拟操作环境，基于 docker 。

可运行命令行终端，甚至支持 docker 内部运行 docker 。

相当于一个在线的容器环境，每个月 50 小时免费时间。

也可利用本地 VS Code 打开该在线容器环境，就像 remote ssh 一样。