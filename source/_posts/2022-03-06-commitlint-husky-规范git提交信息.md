---
title: commitlint + husky 规范git提交信息
tags:
  - Git
categories:
  - FrontEnd
date: 2022-03-06 10:19:36
---

![commitlint-svg](commitlint.svg)

<p align="center">图片来自 <a href="https://github.com/conventional-changelog/commitlint">commitlint</a></p>


## 前言

偶然间看到了这篇文章 [if 我是前端团队Leader，怎么制定前端协作规范?](https://cloud.tencent.com/developer/article/1482183)

于是便了解到了 git 提交规范，并在工作中实践了 [AngularJS提交信息规范](https://github.com/angular/angular/blob/master/CONTRIBUTING.md#commit)，所以在这个周末便写了这篇文章。

## 杂乱无章的现状

来看看我和同事写过的 git 提交信息:
```
fix prod env
fix bugs: #2981 #2976 #2875 #2974
debug
update
merge conflict
fix zentao bug
```
这里只列出了一部分提交信息，其实还存在大量的像 update, debug 等让人无语的的提交信息 😂。你可以从这种提交信息中知道它干了什么吗？😭

## 规范

[AngularJS的提交信息规范](https://github.com/angular/angular/blob/master/CONTRIBUTING.md#commit) 在社区中享有盛名。

当然还存在其它的规范。不过本文只介绍 AngularJS提交信息规范。

### 好处

+ 提高可读性
+ 信息分类，重点突出。按关键字筛选，如过滤掉代码样式的提交
+ 快速定位重要的提交，提高效率 🚀
+ 容易生成CHANGELOG.md

## 提交信息格式

每个提交信息由 <code>header</code>, <code>body</code>和<code>footer</code> 组成，各自用空行隔开。

只有 <code>header</code> 是必须的，<code>body</code> 和 <code>footer</code> 都是可选的。

```
<header>
<BLANK LINE>
<body>
<BLANK LINE>
<footer>
```

### Header

```
<type>(<scope>): <short summary>
  │       │             │
  │       │             └─⫸ Summary in present tense. Not capitalized. No period at the end.
  │       │
  │       └─⫸ Commit Scope: animations|bazel|benchpress|common|compiler|compiler-cli|core|
  │                          elements|forms|http|language-service|localize|platform-browser|
  │                          platform-browser-dynamic|platform-server|router|service-worker|
  │                          upgrade|zone.js|packaging|changelog|docs-infra|migrations|ngcc|ve|
  │                          devtools
  │
  └─⫸ Commit Type: build|ci|docs|feat|fix|perf|refactor|test
```

<code>\<type\></code>和 <code>\<short summary\></code> 是必须的，<code>\<scope\></code> 是可选的。

#### type

必须是以下其中之一:

+ build: 构建系统或外部依赖的变更
+ ci: CI 配置文件和脚本的变更
+ docs: 更新文档
+ feat: 新功能，通常是能够让用户觉察到的变化，小到文案或样式修改
+ fix: 修复bug
+ perf: 提升性能的变更
+ refactor: 重构，代码优化，但不包括修复bug以及添加新功能。比如 代码封装，变量重命名，消除魔术数字，提高代码可读性等
+ test: 单元测试相关的变更

> :information_source: [AngularJS提交信息规范](https://github.com/angular/angular/blob/master/CONTRIBUTING.md#commit) 中已经移除了下面这两项 type，但是 commitlint 并没有将其移除，而且我个人觉得在开发中还是需要用到的，毕竟适合的才是最好的。
+ chore: 杂项，其他无法归类的变更，比如代码合并
+ style: 代码格式调整，对逻辑无影响。比如为按照 eslint 或 prettier 修改代码格式。注意不是 UI 变更

#### scope

可选。用来说明本次提交影响的范围，即简要说明修改会涉及的部分。在一个项目中保持一致最好，比如页面名、模块名、或组件名。

#### summary

简洁描述本次提交的的改动。需要遵循以下约定:

+ 以动词开头，使用第一人称现在时，比如: change，而不是 changed 或 changes
+ 首字母不要大写
+ 结尾不用句号(.)

### Body

可选。是对 Header 中 <code>short summary</code> 的详细描述。

+ 使用第一人称现在时，比如: fix，而不是 fixed 或 fixes
+ 解释提交信息体的更改动机以及更改的原因
+ 可以将先前行为与新行为进行比较，以说明更改的影响

### Footer

一般用来关闭 issue。

如果当前commit针对某个issue，那么可以在Footer部分关闭这个issue:
```
Closes #234
```

也可以一次关闭多个issue:
```
Closes #123, #245, #992
```

> :information_source: 关闭某个issue并非必须使用 Closes 关键字，其实还有其他的关键字可以使用，除非你要严格地遵守 Angular 提交规范。
> 参考这篇文章: [通过提交信息来关闭issue](https://segmentfault.com/a/1190000013973453)

### Revert commits

如果当前提交用于还原先前的提交，它应该以 revert: 开头，后跟还原提交的 <code>header</code>

提交信息 body 内容应该包含:

+ Body 部分的格式是固定的，必须是: This reverts commit <SHA>，其中的hash是被撤销 commit 的 SHA1 标识符是
+ 明确描述还原提交消息的原因

[比如(来自Vue.js)](https://github.com/vuejs/vue/commit/d5ade28652b07303ac6b713813792752ae5e4e04):
```
revert: feat: expose all scoped slots on this.$slots

This reverts commit 0129b0e.
```

## 工具

现在开发项目基本都是团队合作，所以为了保证大家都能遵循该规范，

可以使用 [commitlint](https://github.com/conventional-changelog/commitlint) + [husky](https://github.com/typicode/husky) 配套工具来规范代码提交。

commitlint 是校验git提交信息的工具，husky 用于在项目中方便地添加 git hooks。

## commitlint

### 全局安装

```bash
npm install -g @commitlint/cli @commitlint/config-conventional
```
### 本地安装

```bash
npm install @commitlint/config-conventional @commitlint/cli -D
```

### 配置规范

```bash
echo "module.exports = {extends: ['@commitlint/config-conventional']}" > commitlint.config.js
```
## 测试

需要全局安装才能在命令行使用commitlint
```
# 错误示例，类型后跟一个冒号和一个空格
$ echo "docs:添加新的文档" | commitlint

⧗   input: docs:添加新的文档
✖   subject may not be empty [subject-empty]
✖   type may not be empty [type-empty]
✖   found 2 problems, 0 warnings 
    (Need help? -> https://github.com/conventional-changelog/commitlint#what-is-commitlint )
# 正确示例
$ echo "docs: 添加新的文档" | commitlint

⧗   input: docs: 添加新的文档
✔   found 0 problems, 0 warnings 
    (Need help? -> https://github.com/conventional-changelog/commitlint#what-is-commitlint )
# 或者
$ commitlint "docs: asdf"

⧗   input: build(npm): 添加package.json
✔   found 0 problems, 0 warnings 
    (Need help? -> https://github.com/conventional-changelog/commitlint#what-is-commitlint )
```

## husky

### 安装

```bash
npm install husky -D
```

### 配置

```
# Activate hooks (会在项目根下生成.husky目录)
npx husky install

# Add hook
npx husky add .husky/commit-msg 'npx --no -- commitlint --edit "$1"'
```

编辑 package.json，添加 prepare 脚本:

```json
{
  "scripts": {
    "prepare": "husky install"
  }
}
```

> :information_source: 这样做的目的是，当第一次运行该项目，执行 <code>npm i</code> 时会自动执行 prepare 脚本从而激活 hooks。否则，hooks 没有被激活则无法通过 commitlint 来检验提交信息。

### 测试

```
# 错误提交提交
$ git commit -m "忽略node_modules"
husky > commit-msg (node v10.15.3)

⧗   input: 忽略node_modules
✖   subject may not be empty [subject-empty]
✖   type may not be empty [type-empty]
✖   found 2 problems, 0 warnings 
    (Need help? -> https://github.com/conventional-changelog/commitlint#what-is-commitlint )


husky > commit-msg hook failed (add --no-verify to bypass)
# 正确提交
$ git commit -m "build(npm): 忽略node_modules"
husky > commit-msg (node v10.15.3)

⧗   input: build(npm): 忽略node_modules
✔   found 0 problems, 0 warnings 
    (Need help? -> https://github.com/conventional-changelog/commitlint#what-is-commitlint )


[master 8728336] build(npm): 忽略node_modules
1 file changed, 1 insertion(+)
create mode 100644 .gitignore
```

### 禁用 husky

某一次提交想要禁用husky，可以添加参数--no-verify

```bash
git commit --no-verify -m "your_message"
```

## 建议

并非要完全遵守 [AngularJS提交规范](https://github.com/angular/angular/blob/master/CONTRIBUTING.md#commit)。这个规范肯定是最适合 AngularJS 团队的，并不一定完全适合你和你的团队。结合自身或者团队的需求，利用配套的工具，我们完全可以做出合适的选择。

## 其它工具

[commitizen](https://github.com/commitizen/cz-cli)

[commit-msg-linter](https://github.com/legend80s/commit-msg-linter)

## 参考

[Angular-commit-message-format](https://github.com/angular/angular/blob/master/CONTRIBUTING.md#commit)

[Angular提交信息规范](https://zj-git-guide.readthedocs.io/zh_CN/latest/message/Angular%E6%8F%90%E4%BA%A4%E4%BF%A1%E6%81%AF%E8%A7%84%E8%8C%83/)

[Git 提交信息规范和配套工具](https://juejin.cn/post/6941018697846751262)

[AngularJS的git commit规范推广](https://qi-zhou.github.io/2016/10/27/AngularJS%E7%9A%84git-commit%E8%A7%84%E8%8C%83%E6%8E%A8%E5%B9%BF/)