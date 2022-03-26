---
title: 你必须知道的几种git commit消息撤回的方式
date: 2022-03-26 18:14:59
tags:
  - Git
---


完成了项目的某个功能的开发，需要提交到本地仓库:

```bash
$ git add .
$ git commit -m 'feat: 完成某个功能的开发的一个简短描述'
```

提交后发现，竟然有一个文件忘记保存了，咋办？

难道再来一遍 <code>git add .</code>， <code>git commit -m '...'</code> ？

:joy: :joy: :joy:

上面发生的事是我前几天亲身经历的，那个时候我在想: 我可以撤回刚刚提交的那个commit吗？最好撤回到执行 <code>git add .</code> 操作之后，或者撤回到执行 <code>git add .</code> 操作之前也是可行的。

那我们该怎么做呢？可能你已经猜到了，git这个强大的工具肯定可以帮我们解决这些问题。

现在就让我们一起来学习一下具体的操作吧～ 

:rocket::rocket::rocket:

## 特殊的第一次提交

```bash
$ mkdir demo && cd demo
$ git init
$ echo 'Hello git' > README.md
$ git add .
$ git commit -m 'docs: add README.md'
$ git log
commit befb44f340a4695e1adfe2605de0d271e0e0141c (HEAD -> master)
Author: w2xi <43wangxi@gmail.com>
Date:   Sat Mar 26 19:11:46 2022 +0800

    docs: add README.md
```

如果我们想要撤回刚刚提交的commit，当执行以下操作:

```bash
$ git reset --soft HEAD~1
fatal: 有歧义的参数 'HEAD~1'：未知的版本或路径不存在于工作区中。
使用 '--' 来分隔版本和路径，例如：
'git <命令> [<版本>...] -- [<文件>...]'
```

它会直接报错，原因是 <code>git reset --soft HEAD~1</code> 是重置当前 HEAD 指向上一个 commit，但是现在历史记录中只有一个 commit。

> 在第一次提交之前没有任何内容，因为每个提交都是指父提交。这使得第一次提交是特殊的(孤儿提交)，因此无法引用以前的“状态”。

### git update-ref -d HEAD

这个命令会删除 HEAD 引用，但是对文件的修改并不会丢失。

```bash
$ git update-ref -d HEAD
$ git log
fatal: 您的当前分支 'master' 尚无任何提交
$ cat README.md
Hello git
```

**我们通过这个命令撤销了仓库的第一次commit**

Good job :rocket:

其实还有一种方式就是直接删除 <code>.git</code>，只是稍后又需要 <code>git init / git remote add origin <your_repo_url></code> 稍显麻烦。

## git reset

让我们删除 .git 目录，重头再来。

```bash
$ rm -rf .git
$ git init 
$ echo 'Hello git' > README.md
$ git add .
$ git commit -m 'docs: add README.md'
$ echo 'test git reset action' >> README.md
$ git add .
$ git commit -m 'docs: update README.md'
$ git log
commit 0722863452786c1ed467656b232e788bb16de59f (HEAD -> master)
Author: w2xi <43wangxi@gmail.com>
Date:   Sat Mar 26 20:34:20 2022 +0800

    docs: update README.md

commit bf3906438b9a0f9fd01c5d25c4249a14f307a634
Author: w2xi <43wangxi@gmail.com>
Date:   Sat Mar 26 20:33:53 2022 +0800

    docs: add README.md
```

现在git log 有两条记录了，执行以下命令:

```bash
$ git reset --soft HEAD~1
$ git log
commit bf3906438b9a0f9fd01c5d25c4249a14f307a634 (HEAD -> master)
Author: w2xi <43wangxi@gmail.com>
Date:   Sat Mar 26 20:33:53 2022 +0800

    docs: add README.md
$ git status
位于分支 master
要提交的变更：
  （使用 "git restore --staged <文件>..." 以取消暂存）
        修改：     README.md
```

可以看到，<code>git reset --soft HEAD~1</code> 命令撤销最近的一个commit，但是不撤销 <code>git add .</code>，这不正是我们需要的吗:grinning:？ 

### 几个常用参数

#### --mixed 

这个是默认参数。

意思是：不删除工作空间改动代码，撤销 <code>commit</code>，并且撤销 <code>git add .</code> 操作。

<code>git reset --mixed HEAD^</code> 和 <code>git reset HEAD^</code> 效果是一样的。


#### --soft  

不删除工作空间改动代码，撤销 <code>commit</code>，不撤销  <code>git add .</code>
 
#### --hard

删除工作空间改动代码，撤销 <code>commit</code>，撤销 <code>git add .</code>

注意完成这个操作后，就恢复到了上一次的 <code>commit</code> 状态。

## git revert

测试前的记录信息:

```bash
$ git log
commit 151bb40521fd8d791c8ec79511140bf879c33755 (HEAD -> master)
Author: w2xi <43wangxi@gmail.com>
Date:   Sat Mar 26 20:57:43 2022 +0800

    docs: add README.md
$ git revert HEAD
删除 README.md
[master 35c5602] Revert "docs: add README.md"
 1 file changed, 2 deletions(-)
 delete mode 100644 README.md
$ git log
commit 35c56028fa559a6afad965e74ba017e93864b7fc (HEAD -> master)
Author: w2xi <43wangxi@gmail.com>
Date:   Sat Mar 26 20:59:48 2022 +0800

    Revert "docs: add README.md"
    
    This reverts commit 151bb40521fd8d791c8ec79511140bf879c33755.

commit 151bb40521fd8d791c8ec79511140bf879c33755
Author: w2xi <43wangxi@gmail.com>
Date:   Sat Mar 26 20:57:43 2022 +0800

    docs: add README.md
```
它的原理是: 在当前提交后面，新增一次提交，抵消掉上一次提交导致的所有变化。它不会改变过去的历史，所以是首选方式，没有任何丢失代码的风险。


## 最后

**顺便说一下，如果commit注释写错了，只是想改一下注释，只需要：**
```bash
$ git commit --amend
```
此时会进入默认vim 或 nano编辑器，修改注释完毕后保存就好了。

## 参考

[https://www.cnblogs.com/lfxiao/p/9378763.html](https://www.cnblogs.com/lfxiao/p/9378763.html)

[https://www.ruanyifeng.com/blog/2019/12/git-undo.html](https://www.ruanyifeng.com/blog/2019/12/git-undo.html)

[https://cloud.tencent.com/developer/ask/77445](https://cloud.tencent.com/developer/ask/77445)





