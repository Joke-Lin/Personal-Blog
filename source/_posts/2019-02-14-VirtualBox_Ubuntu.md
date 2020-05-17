---
title: 'Virtual Box 配置虚拟机 Ubuntu'
date: 2019-02-14
categories: 
- 随笔
- ELSE
tags:
- linux
---

## 虚拟机的简单概念

> 摘自百度百科 

​	虚拟机（Virtual Machine）指通过软件模拟的具有完整硬件系统功能的、运行在一个完全隔离环境中的完整计算机系统。
​	虚拟系统通过生成现有操作系统的全新虚拟镜像，它具有真实windows系统完全一样的功能，进入虚拟系统后，所有操作都是在这个全新的独立的虚拟系统里面进行，可以独立安装运行软件，保存数据，拥有自己的独立桌面，不会对真正的系统产生任何影响 ，而且具有能够在现有系统与虚拟镜像之间灵活切换的一类操作系统。虚拟系统和传统的虚拟机（Parallels Desktop ，Vmware，VirtualBox，Virtual pc）不同在于：虚拟系统不会降低电脑的性能，启动虚拟系统不需要像启动windows系统那样耗费时间，运行程序更加方便快捷；虚拟系统只能模拟和现有操作系统相同的环境，而虚拟机则可以模拟出其他种类的操作系统；而且虚拟机需要模拟底层的硬件指令，所以在应用程序运行速度上比虚拟系统慢得多。

<!-- more -->

## 1. 下载 Virtual Box

### 下载位置

`https://www.virtualbox.org/wiki/Downloads`

下载相应的版本即可

![Download_Virtual_Box](\assets\ArticleImg\2019\virtualBox_Ubuntu_1.jpg) 

## 2. 下载Ubuntu镜像文件

### 下载位置

**官方网站：**`https://www.ubuntu.com/download/desktop`

**网易镜像：**`http://mirrors.163.com/ubuntu-releases/` => **ubuntu-18.10-desktop-amd64.iso**  

选一个合适的版本即可 这里采用的是Ubuntu-18.10, 国内镜像可能会快一些，你也可以采用其他镜像源

## 3. 开始安装

### - 新建虚拟机 

![add a VM](\assets\ArticleImg\2019\virtualBox_Ubuntu_2.jpg)

后面都只需要默认即可

### - 配置选项

在这里设置内存大小，处理器等设置，内存、处理器酌情增加，太小巨卡

![set VM](\assets\ArticleImg\2019\virtualBox_Ubuntu_3.jpg)

在第二个小光盘出点击选择一个虚拟光盘文件，也就是前面下载的Ubuntu镜像文件

![set VM](\assets\ArticleImg\2019\virtualBox_Ubuntu_4.jpg)



### - 双击开启安装

语言可以先设置为中文简体，后面都只需要默认即可

![apply](\assets\ArticleImg\2019\virtualBox_Ubuntu_5.jpg)

![apply](\assets\ArticleImg\2019\virtualBox_Ubuntu_9.jpg)

![apply](\assets\ArticleImg\2019\virtualBox_Ubuntu_6.jpg)

![apply](\assets\ArticleImg\2019\virtualBox_Ubuntu_7.jpg)

![apply](\assets\ArticleImg\2019\virtualBox_Ubuntu_8.jpg)

### - 最后重启即可

### - 注意事项

[BIOS可能未开启虚拟化](https://jingyan.baidu.com/article/ab0b56305f2882c15afa7dda.html)

一般不行就删掉重新来一遍，一般都可以 

处理器最好选择2个以上，不然容易中途安装失败