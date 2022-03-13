---
title: Ubuntu环境下更新chrome
date: 2022-03-13 12:11:41
tags:
  - Chrome
categories:
  - Linux
---

最近打算写一篇 JavaScript 深浅拷贝的博客，Google搜索的时候偶然间发现了这篇文章 [Deep-copying in JavaScript using structuredClone](https://web.dev/structured-clone/)，浏览器竟然提供了 <code>structuredClone()</code> API —— 一个内置的深拷贝函数，直到今天我才知道 :joy:，这件事情引起了我的思考: 我似乎从来没有刻意去关注前端最新的知识和技术，直到它已经出来很久了，才会以某种方式出现在我的面前。

额，跑题了，重新回到话题，<code>structuredClone()</code>的浏览器兼容性:

![Chrome](https://raw.github.com/alrra/browser-logos/master/src/chrome/chrome_48x48.png) | ![Firefox](https://raw.github.com/alrra/browser-logos/master/src/firefox/firefox_48x48.png) | ![Edge](https://raw.github.com/alrra/browser-logos/master/src/edge/edge_48x48.png) | ![Safari](https://raw.github.com/alrra/browser-logos/master/src/safari/safari_48x48.png) | ![Opera](https://raw.github.com/alrra/browser-logos/master/src/opera/opera_48x48.png)
:---: | :---: | :---: | :---: | :---: | 
 98 ✔ |  94 ✔ |  98 ✔ | 15.4 ✔ | 84 ✔ |

但是我的 Chrome 版本是 97 的，所以要尝试这个API的话就要更新版本。

我使用的操作系统是 Ubuntu 20.04，chrome 无法自动更新，需要手动更新。

## 更新 Chrome

### 方式一: 终端命令
在终端输入以下命令:
```bash
sudo apt-get update

sudo apt-get --only-upgrade install google-chrome-stable
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

## 测试

开发者工具 -> 控制台，输入:

<code>structuredClone</code>

输出:

```javascript
ƒ structuredClone() { [native code] }
```
