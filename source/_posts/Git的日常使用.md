
---
title: Git的快速使用
description: Git以及GitHub快速入门
categories: 
- 开发工具
tags:
- Git
- Github
---

## 1. Git简介

- Git最初是Linus花了两周时间自己用C写了一个分布式版本控制系统

- 特点：

  - 分布式，每一个主机都有完整版本库
  - 开源免费性能好

  注：类似GitHub这种中心环境的存在是为了交换方便，换言之，即使不存在这种中心设备，Git也是可以正常使用的

  

## 2. Git安装

笔者使用OS是CentOS Linux，Git的安装命令为

```shell
yum install git
```

## 3. 创建仓库

创建新文件夹

```shell
mkdir daily-scripts
cd daily-scripts/
```

初始化Git仓库

```shell
git init
```

添加文件到目录中

```shell
mv ../POI聚类分析.ipynb POI聚类分析.ipynb 
```

添加文件到仓库

```shell
git add POI聚类分析.ipynb
```

添加文件夹

```shell
 git add jupyter-note-book/
```

添加多个文件（夹）

```shelll
git add jupyter-note-book/ python-file/
```

注意：Git是不允许提交一个空的目录到版本库上的

提交文件到仓库

```shell
git commit -m "Add a file POI聚类分析.ipynb"
```

解释：

- Git的工作流如图所示：

  ![img](https://s2.loli.net/2022/02/28/BDz5JSbxjw8AGTF.png)

  第一次add命令将改动添加到缓冲区，第二次的commit命令将改动添加到版本库中

- \- m "..." 表示此次提交的版本的描述信息

## 4. 添加远程库

笔者在GitHub上创建了一个仓库daily-scripts

根据Github的提示，可以把一个已有的本地仓库与之关联，然后，把本地仓库的内容推送到GitHub仓库

```shell
git remote add origin https://github.com/zhnny/daily-scripts.git
```

下一步，就可以把本地库的所有内容推送到远程库上：

```shell
git push -u origin master
```

注意：第一次push的时候，提示输入用户名和密码，需要去GitHub的Settings中去生成Token

关于连接远程仓库使用的ssh key和Token，具体可参考博客[[GitHub使用Personal access token](https://www.cnblogs.com/chenyablog/p/15397548.html)]

push完成后，GitHub中对应的repo中就有一个相同的仓库了

GitHub上的仓库既可以作为备份，又可以让其他人通过该仓库来协作

## 5. 克隆远程库

克隆GitHub上的仓库

```shell
git clone https://github.com/zhnny/PyTorch-Learning-Note.git
```

Git支持多种协议，包括`https`，但`ssh`协议速度最快

ssh协议需要设置公钥，https不需要，但是https在push时要Token

以下步骤将引导完成生成SSH密钥并将公钥添加到GitHub帐户（参考[Checking for existing SSH keys - GitHub Docs](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/checking-for-existing-ssh-keys)）

- cd到home目录

  ```shell
  cd ~
  ```

- 查看是否存在密钥（如果存在密钥可以上传）

  ```shell
  ls .ssh/
  ```

- 生成密钥(使用GitHub电子邮箱替换)

  ```shell
  ssh-keygen -t ed25519 -C "your_email@example.com"
  ```

  接下来一直默认就好，除非你知道你在做什么

- 复制公钥

  ```shell
  cat .ssh/id_ed25519.pub
  ```

- 将公钥粘贴到你的GitHub账户Settings中的SSH keys中

  ![image-20220228215435546](https://s2.loli.net/2022/02/28/vXpe5N2LQUzYbHP.png)

完成ssh key配置后就可以使用ssh协议克隆repo

```shell
git clone git@github.com:zhnny/PyTorch-Learning-Note.git
```

push时也可以直接使用ssh协议

```shell
git push git@github.com:zhnny/PyTorch-Learning-Note.git
```

而不必再使用Token

## 6. 参考资料

[Checking for existing SSH keys - GitHub Docs](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/checking-for-existing-ssh-keys)

[从远程库克隆 - 廖雪峰的官方网站 (liaoxuefeng.com)](https://www.liaoxuefeng.com/wiki/896043488029600/898732792973664)

[GitHub使用Personal access token - 小旭2021 - 博客园 (cnblogs.com)](https://www.cnblogs.com/chenyablog/p/15397548.html)