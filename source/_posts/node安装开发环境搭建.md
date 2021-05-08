---
title: node安装开发环境搭建
date: 2017-10-04 17:24:57
tags:
- Node
categories:
- 环境搭建
description: 介绍安装、卸载node的几种方法
---

> 操作系统：macOS Sierra

# 一 官网下载

**安装**：官网下载安装node

**卸载**：

首先运行脚本

```
lsbom -f -l -s -pf /var/db/receipts/org.nodejs.pkg.bom | while read f; do sudo rm /usr/local/${f}; done
```

接着

```
sudo rm -rf /usr/local/lib/node /usr/local/lib/node_modules /var/db/receipts/org.nodejs.*
```

```
cd /usr/local/lib
sudo rm -rf node*
```

进入 `/usr/local/include` 删除含有 `node` 和 `node_modules` 的目录

```
cd /usr/local/include
sudo rm -rf node*
```

如果是用 brew 安装的node，用下面的命令卸载

```
brew uninstall node
```

进入个人主文件夹，检查各种 local、lib、include 文件夹，删除名字含有`node`和`node_modules`的文件

进入 `/usr/local/bin` 删除 node 执行文件

```
cd /usr/local/bin
sudo rm -rf /usr/local/bin/npm
sudo rm -rf /usr/local/bin/node
ls -las 仔细查看，全局安装的npm包一般会在这个目录下创建软连接，发现就删除
```

其他清理工作

```
sudo rm -rf /usr/local/share/man/man1/node.1
sudo rm -rf /usr/local/lib/dtrace/node.d
sudo rm -rf ~/.npm
```

# 二 通过brew安装

**安装brew**:

官网安装命令

```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

**卸载brew**：

```
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/uninstall)"
```

# 三 通过nvm安装

**安装NVM**：

官网安装命令

```
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.5/install.sh | bash
```

[安装教程](http://www.cnblogs.com/greenteaone/p/5065981.html)

**卸载NVM**：

```
rm -rf ~/.nvm 
```

[教程](http://blog.csdn.net/q617610589/article/details/51456152)

