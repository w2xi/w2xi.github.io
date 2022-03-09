---
title: >-
  [已解决] 如何修复 Kernel Panic - Not Syncing: VFS: Unable to Mount Root FS on
  Unknown-Block(0,0)
tags:
  - Linux
categories:
  - Linux
date: 2022-03-09 19:45:11
---


某天早上来到公司打开电脑，结果发现并没有进入那个我熟悉的界面，而是出现了大段的错误信息，那个时候我是非常郁闷且崩溃的:sob:，今天的心情似乎都不怎么好了。

虽然是第一次碰到这种问题，但是我面不改色，毫不慌张，因为我以前被 Ubuntu 折腾过好几次了:joy:，可以说是‘免疫’了:smiley:。

当然，如果问题解决不了，最坏的结果也就是重装系统而以。

开干。

## Error Message

![Kernel-Panic](kernel-panic-error.jpg)

```
Kernel Panic - Not Syncing: VFS: Unable to Mount Root FS on Unknown-Block(0,0)
```

## Solution

### 第一步: 选择旧的内核进入系统

重启你的电脑，然后在引导界面，选择 **Advanced options for Ubuntu** 菜单，你将看到一些可选的内核项，在这里我的是
**5.13.0-30-generic**(也就是出问题的内核版本)，所以我们需要选择一个以前的内核版本，经过尝试发现 **5.11.0-27-generic** 也可以成功进入的。

### 第二步: 找到根分区的文件系统

通过使用老版本的内核成功进入系统后，打开终端，输入: <code>sudo fdisk -l</code>，可以看到磁盘分区的信息:

```
设备            起点       末尾       扇区   大小 类型
/dev/sda1       2048     780287     778240   380M EFI 系统
/dev/sda2     780288  391405567  390625280 186.3G Linux 文件系统
/dev/sda3  391405568  453906431   62500864  29.8G Linux swap
/dev/sda4  453906432 3907028991 3453122560   1.6T Linux 文件系统
```

其实我们的目的是要找到 **根分区**(<code>/</code>) 的文件系统，从上面的信息中并不能直观地得到答案，输入 <code>df -l</code>，输出如下:

```
文件系统            1K-块     已用       可用 已用% 挂载点
...
/dev/sda2       191197068 19273824  162141228   11% /
...
```
于是我们知道根分区的文件系统是 **/dev/sda2**。

### 第三步: 找到出问题的内核版本

如果你不知道在你电脑没有出问题的时候的内核版本是多少，在终端输入: <code>dpkg --list | grep linux-image</code>, 输出如下:

```
ic  linux-image-5.11.0-25-generic              5.11.0-25.27~20.04.1                  amd64        Signed kernel image generic
rc  linux-image-5.11.0-27-generic              5.11.0-27.29~20.04.1                  amd64        Signed kernel image generic
rc  linux-image-5.11.0-34-generic              5.11.0-34.36~20.04.1                  amd64        Signed kernel image generic
rc  linux-image-5.11.0-36-generic              5.11.0-36.40~20.04.1                  amd64        Signed kernel image generic
rc  linux-image-5.11.0-37-generic              5.11.0-37.41~20.04.2                  amd64        Signed kernel image generic
rc  linux-image-5.11.0-38-generic              5.11.0-38.42~20.04.1                  amd64        Signed kernel image generic
rc  linux-image-5.11.0-40-generic              5.11.0-40.44~20.04.2                  amd64        Signed kernel image generic
rc  linux-image-5.11.0-41-generic              5.11.0-41.45~20.04.1                  amd64        Signed kernel image generic
rc  linux-image-5.11.0-43-generic              5.11.0-43.47~20.04.2                  amd64        Signed kernel image generic
ic  linux-image-5.11.0-44-generic              5.11.0-44.48~20.04.2                  amd64        Signed kernel image generic
ii  linux-image-5.11.0-46-generic              5.11.0-46.51~20.04.1                  amd64        Signed kernel image generic
rc  linux-image-5.13.0-25-generic              5.13.0-25.26~20.04.1                  amd64        Signed kernel image generic
rc  linux-image-5.13.0-27-generic              5.13.0-27.29~20.04.1                  amd64        Signed kernel image generic
iF  linux-image-5.13.0-28-generic              5.13.0-28.31~20.04.1                  amd64        Signed kernel image generic
iF  linux-image-5.13.0-30-generic              5.13.0-30.33~20.04.1                  amd64        Signed kernel image generic
rc  linux-image-5.8.0-43-generic               5.8.0-43.49~20.04.1                   amd64        Signed kernel image generic
rc  linux-image-5.8.0-63-generic               5.8.0-63.71~20.04.1                   amd64        Signed kernel image generic
ii  linux-image-generic-hwe-20.04              5.13.0.30.33~20.04.17                 amd64        Generic Linux kernel image
```

那个最新的就是我们需要的。在这里，我的是 **5.13.0-30-generic**。

### 最后

通过上面的几个步骤，我们知道了:
  + 根分区(/)的文件系统是 **/dev/sda2**
  + 我们原先的内核版本是 **5.13.0-30-generic** (出问题的内核版本)

在终端输入以下命令:

```
sudo mount /dev/sda2 /mnt
sudo mount --bind /dev /mnt/dev
sudo mount --bind /dev/pts /mnt/dev/pts
sudo mount --bind /proc /mnt/proc
sudo mount --bind /sys /mnt/sys
sudo chroot /mnt
```

然后更新 initramfs

```
update-initramfs -u -k 5.13.0-30-generic
```
:information_source: **5.13.0-30-generic** 是出问题的内核版本，用你的内核版本替换它。


最后，更新 grub

```
update-grub
```

更新成功后，重启你的电脑，你应该可以正常的进入你的系统了。

大功告成！

## Reference

[[SOLVED] How to Fix Kernel Panic - Not Syncing: VFS: Unable to Mount Root FS on Unknown-Block(0,0)](https://www.geekswarrior.com/2019/07/solved-how-to-fix-kernel-panic-on-linux.html)
