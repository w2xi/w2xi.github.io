---
title: VSCode配置Git Bash
date: 2022-03-11 13:15:01
tags:
  - CMD
categories:
  - VSCode
---

最近打算学习下小程序的开发，但是操作系统是Ubuntu，压根搞不了啊。

虽然Github上有大佬贡献了Linux版的小程序开发者工具，但是我根本不敢用，原因是不稳定，需要各种折腾，版本很旧，我感觉对我来说花大量的时间去捣鼓这些太浪费时间了，还不如去装个Windows系统。

众所周知，小程序官方的开发者工具只有 Windows和MacOS 版本的，腾讯压根就不会去考虑Linux用户，就像QQ，微信一样，都是社区大佬在做贡献。

没办法，所以只能装双系统了。

这里记录下在Win10的VSCode上配置 Git Bash 的问题。

## VSCode

**VSCodeUserSetup-ia32-1.65.2**

版本: 1.65.2

## 配置

1. 在VSCode中，File -> Preferences -> Settings 或者 <code>Ctrol + ,</code>，找到配置区域，右上角点击 Open Settings (JSON)，打开 settings.json。

2. 插入以下代码:
```
...
"git.enabled": true,
"git.path": "D:\\Software\\Git\\cmd\\git.exe",
"terminal.integrated.profiles.windows": {
  "bash": {
        "path": "D:\\Software\\Git\\bin\\bash.exe",
        "args": []
    }
},
"terminal.integrated.defaultProfile.windows": "bash"
```

3. 输入<code>Ctrl + `</code>，打开终端，在右上角就可以看到 bash (Deafult) 的下拉项了，点击切换到 Git Bash 终端，大功告成！


> **注意: 不同版本的 VSCode 的配置很可能时不一样的**


## 参考

[https://zhuanlan.zhihu.com/p/467033985](https://zhuanlan.zhihu.com/p/467033985)