# Git 命令行操作大全

## 0.文件名称的成功显示

保证Git命令行中内容的成功显示，避免出现乱码和中文显示错误的问题

```
如果无法正常显示中文而不是乱码信息，可以执行如下命令
git config --global core.quotepath false

```

参考链接如下：[git status 显示中文和解决中文乱码_git status 中文-CSDN博客](https://blog.csdn.net/u012145252/article/details/81775362#解决git bash 终端显示中文乱码)

## 1.如何优雅的创建一个Git项目

**在使用Git来管理代码之前，需要明确几个主要的概念**

Git将整个代码的版本控制工作用工作区、暂存区、本地库、远端服务这四个部分区分开来。

工作区：存在于Git服务控制下，但是没有与Git本身产生任何的练习，不具备参与版本控制的资格

暂存区：已经进入到Git的控制之下，是提交到本地库的前一个步骤

本地库：当前文件会被Git监管，拥有了版本控制的资格

远端服务：为了能够将想要保存的信息留存在远端服务器当中，可以将本地库中的文件提交到远端服务中。GItHub就是远端服务当中最出名的一个。

**创建Git用户签名和对应的邮箱**

```shell
在 git 提交代码之前，需要先 设置git的用户签名 和 签名对应的邮箱
1.设置用户签名
git config --global user.name  用户名
2.设置用户签名对应的邮箱
git config --global user.email 邮箱

3.验证是否配置成功
去到windows 下面的 用户目录下  观察 .getConfig 的文件

4.注意
当前的这个签名是用来区分一个机器上的不同操作者的身份的。因为在提交代码的版本信息中能够
看到用户的签名信息，所以是用来确认当前代码是谁提交的。

这里的用户签名和将来要登陆的任何远程库（代码托管中心）都没有任何关系,但是仍要创建
```

**初始化和提交文件**

```shell
1.初始化一个Git工作区
git init
2.查看当前工作区内的文件状态
git status
3.将文件提交到暂存区
git add [fileName]
4.将文件提交到本地库
git commit -m "这是第一次提交" [fileName]
```

## 2.如何将文件推送到远端服务器（以Github为例）

在第一部分，已经将文件提交到了本地库。接下来要做的就是将本地库中的文件信息提交到远端服务器（Github）

### 2.1 通过 ssh 配置Git hub的免密登录

a.在安装了Git之后，在系统盘的用户目录下会出现一个名为.ssh的文件夹。进入到这个文件夹当中使用命令行cmd的方式执行如下命令来生成属于你这台电脑的ssh密钥。

```shell
ssh-keygen -t rsa 
执行这个命令之后，连续敲击三下回车，保证密钥生成结束
```

b.在保证拥有密钥的情况下，进入到github网站。在登录的状态下点击右上角的头像内部的setting按钮，进入到设置页面。

c.在Setting页面左侧的Access的菜单内找到SSH and GPG keys . 然后进入到对应页面.

d.在c步骤进入到的页面当中点击 [New SSH Key] 按钮,将步骤a生成的名为id_rsa.pub的文件中的内容复制到输入框当中.

e.点击保存,ssh免密功能设置成功.

### 2.2 将文件提交到远端仓库

在保证了远端仓库拥有了本地的ssh密钥之后,接下来就可以进行代码的远端提交操作了.

```shell
1.复制远端仓库的ssh链接
位置:创建远端仓库 - 进入远端仓库 - 点击页面中绿色的code按钮 - 在local下点击ssh - 复制ssh链接
2.将ssh链接重新命名
git remote add [called] [ssh_link] 
eg: git remote add gittest https://github.com/mmdxhg/git_test.git
3.将文件提交到远端仓库
git push [called] [branchName]
called ssh链接的假名
branchName 当前要提交的分支的名称
```



## 3.Git的分支

git的分支是一个非常重要的概念，因为Git的应用大多存在于多人协调工作的场景，如果开发人员只有自己那么用起来作用并没有非常明显。分支就是一个专门用来服务于多人协同工作的一项功能。

### 3.1 分支常用的几个命令

```
1.创建一个新的分支，创建新分支的时候会默认复制一份当前git控制下的所有文件
git branch [分支名称]

2.切换分支
git checkout [分支名称]

3.查看当前的分支信息
git branch -v

4.将其他分支合并到当前分支
git merge [其他分支名称]

```

### 3.2 分支的冲突问题

分支的冲突问题经常出现在多人协作的场景里。假设有这样一个文件Passwd.txt中保存着多个数据库的配置信息,其中Mysql的数据库密码为123456，MongoDB数据库的密码是456789.而在整个业务环境中，Mysql的维护是由工作人员Red负责，MongoDB数据库的维护则是由Blue负责。不巧的是在某一天当中，这两个数据库的密码都要被更改为000000，两人于同一天修改且于同一天进行提交。一旦遭遇这种情况，那么冲突就会发生。

**测试准备**

```
1.准备一个文件，文件名称为 passwd.txt ; 文件中的信息如下
mysql-user:root
mysql-passwd:123456

MongoDB-user:root
MongoDB-passwd:456789

2.准备两个分支followerRed followerBlue,其中Red用来添加hive的用户为root,Blue用来添加hive的密码为qwerty

3.在Red分支添加如下内容:hive-user:root,在Bulue分支添加如下内容:hive-passwd:qwerty

4.这两个分支在各自的分支下都要进行add和commit操作.

5.切换回master分支,随后进行合并操作

6.页面返回了如下信息,标识当前自动合并出现错误.申请手动修改

7.打开文件后,文件的内容如下:


```

