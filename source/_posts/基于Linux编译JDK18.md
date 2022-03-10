---
title: 基于Linux编译JDK18
date: 2022-03-11 01:52:53
description: 本文基于Ubuntu虚拟机，使用JDK17编译JDK18
categories:
- 编译器
tags:
- Java
- Linux
---



## 1.概述

JDK都没手动编译过，敢说自己是Java程序员吗？（By 羊哥——[JDK都没手动编译过，敢说自己是Java程序员吗？实战编译Java源码（JDK源码,JVM）视频教程_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1zT4y177Zf?spm_id_from=333.999.0.0)）

- 自己动手编译JDK理论上可以得到更适合自己电脑的JDK
- 可以自己在源码中加入自己的DIY部分，或许会好玩？
- 阅读JDK源码的时候可以修改测试一些功能，在源码中加入自己的笔记

本文基于Ubuntu虚拟机，使用JDK17编译JDK18（应该是目前官方最新的release版本）

## 2.安装Ubuntu虚拟机

安装好虚拟机软件，笔者使用的是VMware

在Ubuntu官网下载好UbuntuISO镜像，笔者下载的是Ubuntu20.04 desktop

下载地址为：

[下载Ubuntu桌面系统 | Ubuntu](https://cn.ubuntu.com/download/desktop)

下载好后启动VMware，设置虚拟机的一些配置信息，建议配置给大一点，毕竟是编译程序，CPU核心多一点会比较快

配置好后挂载ISO镜像，启动虚拟机，配置Ubuntu的一些信息（主要是密码），等待安装完成

![image-20220310144014491](https://s2.loli.net/2022/03/10/rnmFYi1UAbQa7H8.png)

## 3.配置Ubuntu

### 3.1. 配置SSH（可选）

配置SSH工具后就可以在虚拟机外进行SSH连接并操作，当然，可以直接在虚拟机内操作

```shell
sudo apt install openssh-server
```

参考链接：[如何在Ubuntu 20.04上启用SSH - GlaryJoker - 博客园 (cnblogs.com)](https://www.cnblogs.com/livelab/p/13033175.html)



### 3.2. 下载vim（可选）

vim是Linux上普遍使用的终端文本编辑工具，当然，可以选择其他文本工具

```shell
sudo apt install vim
```



### 3.3. 配置安装源

Ubuntu自带的安装源比较慢，此处笔者配置的是阿里云的源

参考链接：

[ubuntu镜像-ubuntu下载地址-ubuntu安装教程-阿里巴巴开源镜像站 (aliyun.com)](https://developer.aliyun.com/mirror/ubuntu)

备份原文件（可选）：

```shell
sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
```

删除原文件：

```shell
sudo rm -rf /etc/apt/sources.list
```

编辑镜像文件：

```shell
sudo vim /etc/apt/sources.list
```

粘贴入镜像站给出的地址：

```
deb http://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse

deb http://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse

deb http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse

deb http://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse

deb http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse

```



## 4.安装编译工具

根据openjdk官方给出的编译指导（[Building the JDK (java.net)](https://openjdk.java.net/groups/build/doc/building.html)），分别需要安装以下工具：



### 4.1. 工具链(Toolchain)

```shell
sudo apt-get install build-essential
```



### 4.2. 基础JDK（Boot JDK）

需要邻近版本的JDK，一般选用前一个版本

```shell
sudo apt-get install openjdk-17-jdk 
```



### 4.3. 链接库（External Library）

#### 4.3.1. FreeType

```shell
sudo apt-get install libfreetype6-dev
```



#### 4.3.2. CUPS

```shell
sudo apt-get install libcups2-dev
```



#### 4.3.3. X11

```shell
sudo apt-get install libx11-dev libxext-dev libxrender-dev libxrandr-dev libxtst-dev libxt-dev
```



#### 4.3.4. ALSA

```shell
sudo apt-get install libasound2-dev
```



#### 4.3.5. libffi

```shell
sudo apt-get install libffi-dev
```



### 4.4. 构建工具（Build Tools）

#### 4.4.1. Autoconf

需要最低版本2.69

```shell
sudo apt-get install autoconf
```



#### 4.4.2. GNU Make

需要最低版本3.81



#### 4.4.3. GNU Bash

需要最低版本3.2



## 5. 配置Configure

具体配置项请看：

[Building the JDK (java.net)](https://openjdk.java.net/groups/build/doc/building.html)

此处默认配置



## 6.  配置Make

具体配置项请看：

[Building the JDK (java.net)](https://openjdk.java.net/groups/build/doc/building.html)

此处默认配置



## 7. 编译JDK

生成配置

```shell
bash configure
```

- 如果依赖库没装完或者存在问题，请根据输出的提示安装或者修改

编译全部

```shell
make all
```



## 8. 编译输出

编译完成后编译后的文件在`build`文件夹下

查看文件：

```shell
zhanny@ubuntu:~/Downloads/jdk18$ ls build/linux-x86_64-server-release/
bootcycle-spec.gmk  build.log.old  configure.log      images    make-support
buildjdk-spec.gmk   buildtools     configure-support  jdk       spec.gmk
build.log           compare.sh     hotspot            Makefile  support

```

其中`images`就是release文件夹

```shell
zhanny@ubuntu:~/Downloads/jdk18$ ls build/linux-x86_64-server-release/images/
docs  docs-javase  docs-reference  gengraphs  jdk  jmods  sec-bin.zip  symbols  test

```

`images`下的`jdk`的`bin`下就是常见的Java编译器

```shell
zhanny@ubuntu:~/Downloads/jdk18$ ls build/linux-x86_64-server-release/images/jdk/bin/
jar                  jconsole.debuginfo   jlink                 jstack.debuginfo
jar.debuginfo        jdb                  jlink.debuginfo       jstat
jarsigner            jdb.debuginfo        jmap                  jstatd
jarsigner.debuginfo  jdeprscan            jmap.debuginfo        jstatd.debuginfo
java                 jdeprscan.debuginfo  jmod                  jstat.debuginfo
javac                jdeps                jmod.debuginfo        jwebserver
javac.debuginfo      jdeps.debuginfo      jpackage              jwebserver.debuginfo
java.debuginfo       jfr                  jpackage.debuginfo    keytool
javadoc              jfr.debuginfo        jps                   keytool.debuginfo
javadoc.debuginfo    jhsdb                jps.debuginfo         rmiregistry
javap                jhsdb.debuginfo      jrunscript            rmiregistry.debuginfo
javap.debuginfo      jimage               jrunscript.debuginfo  serialver
jcmd                 jimage.debuginfo     jshell                serialver.debuginfo
jcmd.debuginfo       jinfo                jshell.debuginfo
jconsole             jinfo.debuginfo      jstack

```



## 9. 参考资料

\[1][Building the JDK (java.net)](https://openjdk.java.net/groups/build/doc/building.html)

\[2][openjdk/jdk18: JDK 18 development (github.com)](https://github.com/openjdk/jdk18)

\[3][真·手把手，从头教你编译JDK - 简书 (jianshu.com)](https://www.jianshu.com/p/d03ef2e89801)

\[4][实战:自己编译JDK - 简书 (jianshu.com)](https://www.jianshu.com/p/108c984f8872)




