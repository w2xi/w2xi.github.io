---
title: 在本地环境的前端项目中配置https
tags:
---

## Foreword

这几天在看一个大佬的博客，看到这个标题 [Local HTTPS](https://shanyue.tech/frontend-engineering/local-https.html) 的时候，一个大大的问号浮现在我的脑海中，本地还能https？

于是带着好奇跟着文章的步骤一步步来，发现果然可行，然后就觉得自己知识面又加宽了:muscle:。

其实很久以前我就听说过HTTPS的鼎鼎大名，知道它是HTTP协议的安全版本，但是也仅此而已，可以说对HTTPS的了解一无所知吧:cry:。

于是今天在网上找了[一篇文章](https://juejin.cn/post/6925296374628122632)稍微了解了一下，知道了它大概的原理，然后就觉得自己知识面又加宽了:muscle:。

逃~):running:

## Installation

引用自:
> 凡是复杂且常见的需求，必有人开发出更简单的工具解放生产力，也必有成熟的解决方案占领市场。

mkcert 的地址：[https://github.com/FiloSottile/mkcert](https://github.com/FiloSottile/mkcert)

因为我的操作系统是Windows，所以按照对应的安装方式来，[For Windows](https://github.com/FiloSottile/mkcert#windows)


### mkcert

终端输入:
```bash
mkcert -install
```

输出:
```bash
Created a new local CA 💥
The local CA is now installed in the system trust store! ⚡️
The local CA is now installed in the Firefox trust store (requires browser restart)! 🦊
```

终端输入:
```bash
# 切换到你的项目根目录
cd /path/to/your_project_root_path
# 安装证书
mkcert w2xi.tech '*.w2xi.tech' w2xi.test localhost 127.0.0.1 ::1
```

输出:
```bash
Created a new certificate valid for the following names 📜
 - "w2xi.tech"
 - "*.w2xi.tech"
 - "w2xi.test"
 - "localhost"
 - "127.0.0.1"
 - "::1"

Reminder: X.509 wildcards only go one level deep, so this won't match a.b.w2xi.tech ℹ️

The certificate is at "./w2xi.tech+5.pem" and the key at "./w2xi.tech+5-key.pem" ✅

It will expire on 19 June 2024 🗓

```

### Configuration

vue.config.js

```javascript
module.exports = {
  //...
  devServer: {
    https: true,
    key: fs.readFileSync('./w2xi.tech+5-key.pem'),
    cert: fs.readFileSync('./w2xi.tech+5.pem'),
  }
}
```

## End

阅读大佬的博客，深刻地感受到了自己的渺小。

## Reference

[https://shanyue.tech/frontend-engineering/local-https.html](https://shanyue.tech/frontend-engineering/local-https.html)

[https://github.com/FiloSottile/mkcert](https://github.com/FiloSottile/mkcert)

[https://juejin.cn/post/6925296374628122632](https://juejin.cn/post/6925296374628122632)

[https://www.cnblogs.com/you-men/p/13955113.html](https://www.cnblogs.com/you-men/p/13955113.html)