---
title: Git学习笔记
date: 2017-10-03 22:46:03
tags:
- git
categories:
- 学习笔记
description: Git学习笔记	
---

> [廖雪峰的Git教程](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)
>
> [Git操作详解——阮一峰](http://www.ruanyifeng.com/blog/2014/06/git_remote.html)
>
> [Git权威指南](http://www.worldhello.net/gotgit/)

### 安装Git

git --version
查看git版本

git config
配置git

### 新建本地版本库

版本库（repository）、仓库一个意思，不同说法

可以简单理解成一个目录，这个目录里面的所有文件都可以被Git管理起来，每个文件的修改、删除，Git都能跟踪，以便任何时刻都可以追踪历史，或者在将来某个时刻可以“还原”。

+ 方法一

  新建一个文件夹learngit，进入文件夹，使用`git init`命令初始化（新建）一个版本库

  ```
  $ cd learngit
  $ git init
  ```

+ 方法二

  直接使用`git init <name>`命令，git自动新建一个名为<name>的文件夹同时初始化

  ```
  $ git init learngit
  ```

### 添加修改到版本库

工作区进行工作（修改，新建，删除）-git add-工作区修改添加到暂存区(stage)-git commit-暂存区提交修改

![](https://www.liaoxuefeng.com/files/attachments/001384907702917346729e9afbf4127b6dfbae9207af016000/0)

git add
添加新建文件、修改到暂存区

git commit -m "***"
提交暂存区的修改

git log
查看提交日志

git status
查看工作区状态 git status -s -b， -s显示精简输出，-b显示当前工作分支名称

git branch
查看当前工作分支名称

### 版本回退

版本库中HEAD指针移动到指定的那个版本，同时用该版本更新工作区

+ 方法一

  `$ git reset --hard commit_id`

  commit_id是每个版本的版本号，版本号没必要写全，前几位就可以了，Git会自动去找。当然也不能只写前一两位，因为Git可能会找到多个版本号，就无法确定是哪一个了。

  `$ git log`可以查看提交日志，有每次提交的commit_id（版本号）

  `$ git log --pretty=oneline`简介输出提交日志

  `$ git reflog`显示整个本地仓储的commit, 包括所有branch的commit, 甚至包括已经撤销的commit, 只要HEAD发生了变化, 就会在reflog里面看得到. git log只包括当前分支的commit.

+ 方法二

  `$ git reset —hard HEAD^`

  HEAD是版本库中指向当前版本的指针，HEAD 表示当前版本， HEAD^ 表示上一个版本，HEAD^^ 表示上上个版本

可以用`git diff`命令检测工作区、暂存区、版本库是否同步

`$ git diff` 比较工作区和暂存区

`$ git diff --cached` 比较暂存区和版本库

`$ git diff HEAD` 比较工作区和版本库

### 撤销修改

`git checkout`这个命令有多个用途，撤销修改，切换分支

#### 撤销工作区修改

+ 方法一：手动恢复工作区文件的修改

+ 方法二：`$ git checkout -- file`

  撤销工作区修改，如果暂存区里有add，就和暂存区的版本一致，如果没有，就和版本库中最近一次commit版本一致

git checkout -- file 中 -- 用来区分 分支／文件名 ，-- 的名称叫做double dash，是bash的内置命令，用来标记可选命令选项的结束。即在它后面的带 -- 的字符串，不被当做是一个命令选项。

**例如**

有个文件的名字就叫master，和master分支名字一摸一样，那么 git checkout master就是切换到master分支，如果想撤销master文件中的修改，git checkout -- master 就不会把master当作分支名。所以只要文件名不和分支名或其他命令关键字重复，git checkout file也是撤销，不会变成切换分支

#### 撤销暂存区修改

git reset HEAD file 撤销暂存区的add，工作区的修改没变，只是状态变为未暂存：Changes not staged for commit

### 删除文件

删除文件也是一种修改

版本库中也需要删除该文件 并提交

```
$ git rm file
$ git commit -m "..."
```

### 远程仓库

项目托管到github

#### 注册GitHub账户

#### 生成sshkey

​	ssh-keygen -t rsa -C "GitHub账户邮箱"

​	一直回车
​	成功生成ssh

#### 添加sshKey到GitHub账户

​	登陆GitHub，settings，SSH and GPG Keys, New SSh Keys,将本地生成的ssh文件夹下的公钥（id_rsa.pub）中的内容复制，点击add ssh key 添加成功

​	为什么GitHub需要SSH Key呢？因为GitHub需要识别出你推送的提交确实是你推送的，而不是别人冒充的，而Git支持SSH协议，所以，GitHub只要知道了你的公钥，就可以确认只有你自己才能推送。

​	**多台电脑ssh key添加到一个GitHub账户**

​	当然，GitHub允许你添加多个Key。假定你有若干电脑，你一会儿在公司提交，一会儿在家里提交，只要把每台电脑的Key都添加到GitHub，就可以在每台电脑上往GitHub推送了。

​	***我有一台电脑，注册了2个GitHub账户，一个工作，一个博客，如何操作？***

#### 建立GitHub远程仓库

​	上述步骤只是注册了GitHub账户，并且添加了你电脑上的ssh key，保证你的电脑和你的GitHub能够相互通信，但GitHub仓库!=GitHub账户，GitHub远程仓库还需要建立，点击猫咪回到首页，start a project 或者 new repository都可创建一个新的github远程仓库

​	点击后，输入一个仓库名称，点击“Create repository”按钮，就成功地创建了一个新的Git仓库

#### git remote

​	为了便于管理，Git要求每个远程主机都必须指定一个主机名。`git remote`命令就用于管理主机名。

​	不带选项的时候，`git remote`命令列出所有远程主机。

> ```
> $ git remote
> origin
> ```

​	使用  `-v`选项，可以参看远程主机的网址。

> ```
> $ git remote -v
> origin  git@github.com:jquery/jquery.git (fetch)
> origin  git@github.com:jquery/jquery.git (push)
> ```

​	上面命令表示，当前只有一台远程主机，叫做origin，以及它的网址。

​	克隆版本库的时候，所使用的远程主机自动被Git命名为`origin`。如果想用其他的主机名，需要用`git clone`命令的`-o`选项指定。

> ```
> $ git clone -o jQuery https://github.com/jquery/jquery.git
> $ git remote
> jQuery
>
> ```

​	上面命令表示，克隆的时候，指定远程主机叫做jQuery。

​	`git remote show`命令加上主机名，可以查看该主机的详细信息。

> ```
> $ git remote show <主机名>
>
> ```

​	`git remote add`命令用于添加远程主机。

> ```
> $ git remote add <主机名> <网址>
>
> ```

​	`git remote rm`命令用于删除远程主机。

> ```
> $ git remote rm <主机名>
>
> ```

​	`git remote rename`命令用于远程主机的改名。

> ```
> $ git remote rename <原主机名> <新主机名>
> ```

​	**GitHub仓库不添加本地ssh key，仍然可以关联，但无法push或clone**

#### git clone

​	远程操作的第一步，通常是从远程主机克隆一个版本库，这时就要用到`git clone`命令。

> ```
> $ git clone <版本库的网址>
> ```

​	比如，克隆jQuery的版本库。

> ```
> $ git clone https://github.com/jquery/jquery.git
>
> ```

​	该命令会在本地主机生成一个目录，与远程主机的版本库同名。如果要指定不同的目录名，可以将目录名作为`git clone`命令的第二个参数。

> ```
> $ git clone <版本库的网址> <本地目录名>
>
> ```

​	`git clone`支持多种协议，除了HTTP(s)以外，还支持SSH、Git、本地文件协议等，下面是一些例子。

> ```
> $ git clone http[s]://example.com/path/to/repo.git/
> $ git clone ssh://example.com/path/to/repo.git/
> $ git clone git://example.com/path/to/repo.git/
> $ git clone /opt/git/project.git 
> $ git clone file:///opt/git/project.git
> $ git clone ftp[s]://example.com/path/to/repo.git/
> $ git clone rsync://example.com/path/to/repo.git/
> ```

​	SSH协议还有另一种写法。

> ```
> $ git clone [user@]example.com:path/to/repo.git/
>
> ```

​	通常来说，Git协议下载速度最快，SSH协议用于需要用户认证的场合。各种协议优劣的详细讨论请参考[官方文档](http://git-scm.com/book/en/Git-on-the-Server-The-Protocols)。

#### git fetch

​	一旦远程主机的版本库有了更新（Git术语叫做commit），需要将这些更新取回本地，这时就要用到`git fetch`命令。

> ```
> $ git fetch <远程主机名>
>
> ```

​	上面命令将某个远程主机的更新，全部取回本地。

​	`git fetch`命令通常用来查看其他人的进程，因为它取回的代码对你本地的开发代码没有影响。

​	默认情况下，`git fetch`取回所有分支（branch）的更新。如果只想取回特定分支的更新，可以指定分支名。

> ```
> $ git fetch <远程主机名> <分支名>
>
> ```

​	比如，取回`origin`主机的`master`分支。

> ```
> $ git fetch origin master
>
> ```

​	所取回的更新，在本地主机上要用"远程主机名/分支名"的形式读取。比如`origin`主机的`master`，就要用`origin/master`读取。

​		`git branch`命令的`-r`选项，可以用来查看远程分支，`-a`选项查看所有分支。

> ```
> $ git branch -r
> origin/master
>
> $ git branch -a
> * master
>   remotes/origin/master
>
> ```

​	上面命令表示，本地主机的当前分支是`master`，远程分支是`origin/master`。

​	取回远程主机的更新以后，可以在它的基础上，使用`git checkout`命令创建一个新的分支。

> ```
> $ git checkout -b newBrach origin/master
>
> ```

​	上面命令表示，在`origin/master`的基础上，创建一个新分支。

​	此外，也可以使用`git merge`命令或者`git rebase`命令，在本地分支上合并远程分支。

> ```
> $ git merge origin/master
> # 或者
> $ git rebase origin/master
>
> ```

​	上面命令表示在当前分支上，合并`origin/master`。

#### git pull

​	`git pull`命令的作用是，取回远程主机某个分支的更新，再与本地的指定分支合并。它的完整格式稍稍有点复杂。

> ```
> $ git pull <远程主机名> <远程分支名>:<本地分支名>
> ```

​	比如，取回`origin`主机的`next`分支，与本地的`master`分支合并，需要写成下面这样。

> ```
> $ git pull origin next:master
> ```

​	如果远程分支是与当前分支合并，则冒号后面的部分可以省略。

> ```
> $ git pull origin next
>
> ```

​	上面命令表示，取回`origin/next`分支，再与当前分支合并。实质上，这等同于先做`git fetch`，再做`git merge`。

> ```
> $ git fetch origin
> $ git merge origin/next
>
> ```

​	在某些场合，Git会自动在本地分支与远程分支之间，建立一种追踪关系（tracking）。比如，在`git clone`的时候，所有本地分支默认与远程主机的同名分支，建立追踪关系，也就是说，本地的`master`分支自动"追踪"`origin/master`分支。

Git也允许手动建立追踪关系。

> ```
> git branch --set-upstream master origin/next
>
> ```

​	上面命令指定`master`分支追踪`origin/next`分支。

​	如果当前分支与远程分支存在追踪关系，`git pull`就可以省略远程分支名。

> ```
> $ git pull origin
>
> ```

​	上面命令表示，本地的当前分支自动与对应的`origin`主机"追踪分支"（remote-tracking branch）进行合并。

​	如果当前分支只有一个追踪分支，连远程主机名都可以省略。

> ```
> $ git pull
>
> ```

​	上面命令表示，当前分支自动与唯一一个追踪分支进行合并。

​	如果合并需要采用rebase模式，可以使用`--rebase`选项。

> ```
> $ git pull --rebase <远程主机名> <远程分支名>:<本地分支名>
>
> ```

​	如果远程主机删除了某个分支，默认情况下，`git pull` 不会在拉取远程分支的时候，删除对应的本地分支。这是为了防止，由于其他人操作了远程主机，导致`git pull`不知不觉删除了本地分支。

​	但是，你可以改变这个行为，加上参数 `-p` 就会在本地删除远程已经删除的分支。

> ```
> $ git pull -p
> # 等同于下面的命令
> $ git fetch --prune origin 
> $ git fetch -p
> ```

#### git push

​	`git push`命令用于将本地分支的更新，推送到远程主机。它的格式与`git pull`命令相仿。

> ```
> $ git push <远程主机名> <本地分支名>:<远程分支名>
> ```

​	注意，分支推送顺序的写法是<来源地>:<目的地>，所以`git pull`是<远程分支>:<本地分支>，而`git push`是<本地分支>:<远程分支>。

​	如果省略远程分支名，则表示将本地分支推送与之存在"追踪关系"的远程分支（通常两者同名），如果该远程分支不存在，则会被新建。

> ```
> $ git push origin master
> ```

​	上面命令表示，将本地的`master`分支推送到`origin`主机的`master`分支。如果后者不存在，则会被新建。

​	如果省略本地分支名，则表示删除指定的远程分支，因为这等同于推送一个空的本地分支到远程分支。

> ```
> $ git push origin :master
> # 等同于
> $ git push origin --delete master
> ```

​	上面命令表示删除`origin`主机的`master`分支。

​	如果当前分支与远程分支之间存在追踪关系，则本地分支和远程分支都可以省略。

> ```
> $ git push origin
> ```

​	上面命令表示，将当前分支推送到`origin`主机的对应分支。

​	如果当前分支只有一个追踪分支，那么主机名都可以省略。

> ```
> $ git push
> ```

​	如果当前分支与多个主机存在追踪关系，则可以使用`-u`选项指定一个默认主机，这样后面就可以不加任何参数使用`git push`。

> ```
> $ git push -u origin master
> ```

​	上面命令将本地的`master`分支推送到`origin`主机，同时指定`origin`为默认主机，后面就可以不加任何参数使用`git push`了。

​	不带任何参数的`git push`，默认只推送当前分支，这叫做simple方式。此外，还有一种matching方式，会推送所有有对应的远程分支的本地分支。Git 2.0版本之前，默认采用matching方法，现在改为默认采用simple方式。如果要修改这个设置，可以采用`git config`命令。

> ```
> $ git config --global push.default matching
> # 或者
> $ git config --global push.default simple
> ```

​	还有一种情况，就是不管是否存在对应的远程分支，将本地的所有分支都推送到远程主机，这时需要使用`--all`选项。

> ```
> $ git push --all origin
> ```

​	上面命令表示，将所有本地分支都推送到`origin`主机。

​	如果远程主机的版本比本地版本更新，推送时Git会报错，要求先在本地做`git pull`合并差异，然后再推送到远程主机。这时，如果你一定要推送，可以使用`--force`选项。

> ```
> $ git push --force origin 
> ```

​	上面命令使用`--force`选项，结果导致远程主机上更新的版本被覆盖。除非你很确定要这样做，否则应该尽量避免使用`--force`选项。

​	最后，`git push`不会推送标签（tag），除非使用`--tags`选项。

> ```
> $ git push origin --tags
> ```

### 分支管理

**创建分支**
git branch <分支名>
git checkout -b <分支名>
git checkout命令加上-b参数表示创建并切换，相当于以下两条命令：
$ git branch <分支名>
$ git checkout <分支名>

**查看分支**
git branch
git branch命令会列出所有分支，当前分支前面会标一个*号。

**合并分支**
git merge <分支名>
git merge命令用于合并<分支名>分支到当前分支。
git merge 默认使用“Fast-forward"快速合并模式，当两个分支分别有新的提交时，快速合并会产生冲突，需要手动修改冲突

**删除分支**
git branch -d <分支名>



# issues

2018.1.23

desc：昨天想把做完的股票管理应用放到github上。

新建了一个github仓库，写了readme。so，现在这个远程仓库除了readme什么也没有。

webstorm打开股票应用，now，本地的仓库和远程的仓库是两个素不相识的路人。现在使用`git remote`查看远程仓库没有任何反应，应为还没有给远程仓库指定主机名，本地仓库还不知道有远程仓库这么个人。



先给新建的git仓库起个名字：

```
git remote add stock git@github.com:Yu-Sen/StockManageApp.git
```

现在本地仓库终于知道这个名叫stock的git仓库了，使用`git remote -v`可以看出端倪：

```
EthandeMBP:webpackDemo ethan$ git remote -v
test    git@github.com:Yu-Sen/test.git (fetch)
test    git@github.com:Yu-Sen/test.git (push)
```

（本地仓库已经add，commit过了）现在我可以把本地仓库的内容push到git仓库上了吧：

```
git push
```

Ah ho，报错了：

```
EthandeMBP:webpackDemo ethan$ git push
fatal: No configured push destination.
Either specify the URL from the command-line or configure a remote repository using

    git remote add <name> <url>

and then push using the remote name

    git push <name>
```

报错原因：本地仓库的master分支和远程仓库的master分支还没有建立追踪关系，git push的完整写法是`git push 主机名 本地分支:远程分支`，只有当本地分支和远程分支存在追踪关系的时候才可以省略参数

`git push 主机名 本地分支`：省略远程分支名，当指定的本地分支有追踪的远程分支时（两个分支通常同名），如果远程分支不存在，则会自动新建

`git push 主机名`：省略本地分支名和远程分支名，当当前所在的本地分支有追踪的远程分支时

`git push`：省略主机名、本地分支、远程分支，当前所在本地分支有且只有一个追踪的远程分支时

```
EthandeMBP:webpackDemo ethan$ git push test
fatal: The current branch master has no upstream branch.
To push the current branch and set the remote as upstream, use

    git push --set-upstream test master
```

再次使用`git push test`命令，push失败，提示当前分支master没有关联分支，使用`—set-upstream`来设置关联分支：

```
EthandeMBP:webpackDemo ethan$ git push --set-upstream test master
To github.com:Yu-Sen/test.git
 ! [rejected]        master -> master (fetch first)
error: failed to push some refs to 'git@github.com:Yu-Sen/test.git'
hint: Updates were rejected because the remote contains work that you do
hint: not have locally. This is usually caused by another repository pushing
hint: to the same ref. You may want to first integrate the remote changes
hint: (e.g., 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```

又报错了？好吧，错误说远程仓库中含有本地仓库没有的文件，所以push被拒绝，在push前需要先pull一下，如果看到关于fast-forwards的提示，在`git push —help`中查看具体说明：

```
EthandeMBP:webpackDemo ethan$ git pull
fatal: No remote repository specified.  Please, specify either a URL or a
remote name from which new revisions should be fetched.
```

直接使用git pull报错，原因是当前本地分支没有追踪任何远程分支，只有当前本地分支追踪了远程分支时，才可以省略

好吧，这次写全一点：

```
EthandeMBP:webpackDemo ethan$ git pull test master
From github.com:Yu-Sen/test
 * branch            master     -> FETCH_HEAD
fatal: refusing to merge unrelated histories
```

fatal，致命错误！无法合并，why？因为在较新版本的git中，如果要pull合并两个不同的项目，需要加一句`—allow-unrelated-histories`。

