# Awesome Linux kernel
Linux 内核相关的有趣资源，欢迎补充。

## 入门
- [bb-kernel](https://github.com/RobertCNelson/bb-kernel/tree/am33x-rt-v5.4) : This is just a set of scripts to rebuild a known working kernel for ARM devices.

## 虚拟化
- [LC-3 虚拟机](https://justinmeiners.github.io/) : 只有几百行
- [dockerpi](https://github.com/lukechilds/dockerpi) : 其实是一百行左右的 Dockerfile，在其中运行 qemu 模拟 raspberrypi 的硬件环境法
- [OSX-KVM](https://github.com/kholia/OSX-KVM) : 利用 kvm 实现运行 OSX 的虚拟机
- [Docker-OSX](https://github.com/sickcodes/Docker-OSX) : 类似 dockerpi, 提供安装 [OSX-KVM](https://github.com/kholia/OSX-KVM) 的自动安装
- [macos virtualbox](https://github.com/myspaghetti/macos-virtualbox) : 提供一个脚本，在 virtualbox 中间运行 macos
- [v86](https://github.com/copy/v86/) : 使用 js 写的 x86 硬件虚拟化，可以在网页上运行机器
- [windows95 in electron](https://github.com/felixrieseberg/windows95) : 利用 v86 实现运行 windows95 在 electron 中间

#### KVM
- [Learning KVM - implement your own kernel](https://david942j.blogspot.com/2018/10/note-learning-kvm-implement-your-own.html)
  - [源码](https://github.com/david942j/kvm-kernel-example)
- [kvmexample](https://github.com/dpw/kvm-hello-world)
- [kvmsample](https://github.com/soulxu/kvmsample) : 几百行，guest 就是一个三行的汇编
- [kvmtool](https://github.com/kvmtool/kvmtool) : 没有维护, 大约20000 行
- [简易的KVM firmware的简单Rust 实现](https://news.ycombinator.com/item?id=19883626) : @todo 不是很懂 firmware 在此处的含义

## 进程
- [Kernel Thread Sanitizer](https://github.com/google/ktsan/wiki)
- [cfs](https://mp.weixin.qq.com/s?src=11&timestamp=1591080226&ver=2375&signature=K2jSqjhp-0l6XyTMq2HpeRjHcThcAXKYScK2X3SIdSh5K01c1tF723WBf3y834RS7KoK18OlSRROZe3uA8JRTwsp3p8VkIei7tRK5ibDllsoWytRhLxkolG*79iX9Jc9&new=1)

## 网络
- [epoll 原理](https://zhuanlan.zhihu.com/p/63179839)

## sysadmin
- [Linux sysadmin chanllenge](https://github.com/snori74/linuxupskillchallenge) : 一共二十个教程
- [Linux Tools Quick Tutorial](https://linuxtools-rst.readthedocs.io/zh_CN/latest/base/03_text_processing.html) : 文本处理的部分强烈推荐
- [Linux Productivity Tools](https://news.ycombinator.com/item?id=23229241)
- [Test your sysadmin skills](https://github.com/trimstray/test-your-sysadmin-skills)
- [Linux sysadmin interview questions](https://github.com/chassing/linux-sysadmin-interview-questions)
- [Java 程序员眼中的 Linux](https://github.com/judasn/Linux-Tutorial)
- [常用工具的 checksheet](https://github.com/guodongxiaren/LinuxTool/blob/master/gcc.md)
- [Linux journey](https://linuxjourney.com/) : 界面精美

## 安全
- [make linux fast again](https://make-linux-fast-again.com/)
- [a13xp0p0v](https://github.com/a13xp0p0v) : linux 安全的工程师，多个项目可以作为参考，linux-kernel-defence-map 可重点关注
- [Andrey Konovalov](https://github.com/xairy) : 很厉害
- https://github.com/r0hi7/BinExp : binary exploitation
- https://github.com/milabs/awesome-linux-rootkits
- https://github.com/xairy/linux-kernel-exploitation

## distribution
- [snakeware](https://news.ycombinator.com/item?id=23391380)
- https://github.com/linuxkit/linuxkit : 制作自己的发行版
- https://github.com/ivandavidov/minimal : mininal 发行版

## 教程
- [eudyptula](https://github.com/agelastic/eudyptula) : 划分为 20 任务，项目已停止，不过还是有价值
- [Linux kernel labs](https://linux-kernel-labs.github.io/refs/heads/master/labs/introduction.html) : 强烈推荐
- [raspberry pi os](https://github.com/s-matyukevich/raspberry-pi-os) : 利用 raspberrypi 学习内核
- https://gitee.com/tinylab 
- https://devarea.com/labs
  - https://devarea.com/introduction-to-network-filters-linux/#.Xm3bn3UzYUE
  - https://devarea.com/linux-kernel-development-creating-a-proc-file-and-interfacing-with-user-space/#.Xm3biXUzYUE
- https://github.com/figozhang/runninglinuxkernel_4.0 : 奔跑吧linux内核 @todo 似乎讲解过总线

## trace
- [eBPF 介绍](https://news.ycombinator.com/item?id=22953730)
- https://github.com/zoidbergwill/awesome-ebpf
- [Debug Hacks](https://book.douban.com/subject/6799412/) 内核调试的老技术

## tiny os
- [Os tutorial](https://github.com/cfenollosa/os-tutorial) : 讲解清晰，但是部分完工
- [OS in Rust](https://github.com/phil-opp/blog_os)
- [biscuit](https://github.com/mit-pdos/biscuit) : 使用 go 写的 POSIX-subset OS
- [清华的 rcore](https://github.com/chyyuu/rCore_tutorial)
- [zcore](https://github.com/rcore-os/zCore)
    - [相关文档](https://zhuanlan.zhihu.com/p/137733625)
- [南京大学的os lab](https://github.com/sslab-gatech/cs3210-rustos-public)
    - [相关文档](https://tc.gts3.org/cs3210/2020/spring/info.html)
- [rust kernel](https://blog.stephenmarz.com/)
    - https://github.com/sgmarz/osblog
- https://github.com/MRNIU/SimpleKernel

## 文章合集
- [wowotech](http://www.wowotech.net/) :star: :star: :star: :star: :star:
- [linux inside](https://0xax.gitbooks.io/linux-insides/content/) :star: :star: :star: :star: :star:
- [low level programming university](https://github.com/gurugio/lowlevelprogramming-university/blob/master/README_cn.md) :star: :star: :star: :star:
- [知乎专栏:术道经纬](https://zhuanlan.zhihu.com/p/93289632) :star: :star:
- [gatieme 的笔记](https://github.com/gatieme/LDD-LinuxDeviceDrivers) :star: :star:
- [LoyenWang](https://www.cnblogs.com/LoyenWang/) :star: :star:
- [dsahern's blog](https://people.kernel.org/dsahern/) :star: 
  - [The CPU Cost of Networking on a Linux Host](https://news.ycombinator.com/item?id=23189372)
- https://devarea.com/labs/#.Xm3c_XUzYUE
- https://github.com/novelinux/linux-4.x.y
- https://terenceli.github.io/archive.html
- https://unixism.net/2020/04/io-uring-by-example-article-series/

## 文摘
- [Fuchsia Overview](https://news.ycombinator.com/item?id=23364172) : hn 关于 Fuchaia 的评价
- [知乎 : 如何学习内核 ?](https://www.zhihu.com/question/304179651/answer/561395663)
- https://embeddedbits.org/how-is-the-linux-kernel-tested/
- https://news.ycombinator.com/item?id=22987747 : telefork() 将进程发送到另一个计算机上
- [meltdown 和 spectrum 相关](https://mp.weixin.qq.com/s?__biz=Mzg2MjE0NDE5OA==&mid=2247484455&idx=1&sn=3ce685da00fb31579c08ce585bfda135&chksm=ce0d178ef97a9e98fc8e77cc57a9efe91cba607ee235d5c1e28bce23453b72298f7372b04f18&scene=0&xtrack=1#rd)

## project
- [syzkaller](https://github.com/google/syzkaller/blob/master/docs/setup.md) : @todo 暂时不知道如何实现 fuzzer 的
- [kernelci](https://kernelci.org/)
- https://github.com/linux-test-project/ltp
- https://systemd.io/ 了解一下其中的内容
- https://github.com/jarun/keysniffer
- https://github.com/orhun/kmon : 内核包管理器，
- [idea4good](https://gitee.com/idea4good) : 和内核没有什么关系，只是利用 fb 和 shmem，绕过 X 来实现显示让人觉得很有意思

## another os
- [popcorn os](https://news.ycombinator.com/item?id=23060695) linux kernel 的基础上为异构体系统提供支持
- [minos](https://github.com/minosproject/minos) : 国人开发的 RTOS
- https://github.com/demikernel/demikernel
- https://github.com/bottlerocket-os/bottlerocket : 基于linux 为容器而生的操作系统, 类似还有很多, 可以在[awesome linux containers](https://github.com/Friz-zy/awesome-linux-containers) 中间找
- https://www.freebsd.org/
- https://www.minix3.org/
- https://github.com/swimos/swim
- https://github.com/dwelch67/raspberrypi : 要啥树莓派，qemu 学习 arm 指令集
- https://news.ycombinator.com/item?id=22564665 : good fellow helps, all kinds of resources
