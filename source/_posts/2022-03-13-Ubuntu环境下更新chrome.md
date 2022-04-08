---
title: Ubuntu环境下更新chrome
date: 2022-03-13 12:11:41
tags:
  - Chrome
  - VSCode
categories:
  - Linux
---

我使用的操作系统是 Ubuntu 20.04，chrome 无法自动更新，需要手动更新。

## 更新 Chrome

### 方式一: 终端命令

在终端输入以下命令:
```bash
$ sudo apt-get update
$ sudo apt-get --only-upgrade install google-chrome-stable
```

更新完毕，Chrome 浏览器网址中输入: <code>chrome://version</code>，版本从原来的 97 更新到了 99。

### 方式二: 重新安装.deb包更新Chrome

下载
```bash
wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.
deb
```

测试发现，无论开不开代理下载速度都非常快。

安装:

```bash
dpkg -i ./google-chrome-stable_current_amd64.deb
```
## 更新 VSCode

```bash
$ sudo add-apt-repository -y "deb https://packages.microsoft.com/repos/vscode stable main "
$ sudo apt update
$ sudo apt -y install code
```
## 参考

[How to update Chrome in Ubuntu 20.04](https://askubuntu.com/questions/1275967/how-to-update-chrome-in-ubuntu-20-04)

[How to upgrade Visual Studio Code editor](https://stackoverflow.com/questions/42960703/how-to-upgrade-visual-studio-code-editor)