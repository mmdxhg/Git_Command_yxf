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

**测试准备及实现**

```shell
1.准备一个文件，文件名称为 passwd.txt ; 文件中的信息如下
mysql-user:root
mysql-passwd:123456

MongoDB-user:root
MongoDB-passwd:456789

2.准备两个分支followerRed followerBlue,其中Red用来添加hive的用户为root,Blue用来添加hive的密码为qwerty

3.在Red分支添加如下内容:hive-user:root,在Bulue分支添加如下内容:hive-passwd:qwerty

4.这两个分支在各自的分支下都要进行add和commit操作.

5.切换回master分支,随后进行合并操作
git merge followerRed
git merge followerBlue

6.页面返回了如下信息,标识当前自动合并出现错误.申请手动修改
Auto-merging passwd.txt
CONFLICT (content): Merge conflict in passwd.txt
Automatic merge failed; fix conflicts and then commit the result.

7.打开文件后,文件的内容如下:

mysql-user:root
mysql-passwd:000000

MongoDB-user:root
MongoDB-passwd:000000

<<<<<<< HEAD
hive-user:root
=======
hive-passwd:qwerty
>>>>>>> followerBlue

注释:在这段代码中 ======= 为分割符号,<<<<<<< HEAD为文件本来的信息,>>>>>>> followerBlue为冲突的内容
也就是说 <<<<<< 到 ====== 与 ======= 到 >>>>>>> 之间的内容就是有冲突,需要手动修改
手动修改后的内容为
mysql-user:root
mysql-passwd:000000

MongoDB-user:root
MongoDB-passwd:000000

hive-user:root
hive-passwd:qwerty

8.修改之后,继续执行add 和 commit命令,不过commit命令与之间的不太一样,不用带上文件名
git add passwd.txt
git commit 
注释:当发生冲突时,命令行的提示符会出现(分支名|merging)的字样,提交之后,这个字样会重新变成(分支名)
```



## 4.Git过滤文件的编写

在使用git进行版本维护的时候，在某些特定的情况下是不需要将一些文件保存到远端服务器当中的，如果想要过滤掉一些不必要的文件可以采用git自带的过滤功能。

### 4.1 过滤文件的介绍及应用

想要完成忽略操作，可以创建一个忽略规则文件  xxxx.ignore （这个文件的前缀名任意，但是扩展名必须是ignore，建议以git.ignore命名）

创建好这个文件之后,可以把这个文件放到一个不经常使用的位置,防止这个文件被误删或者是修改.

```
1.创建一个文件,git.ignore 将这个文件放到window计算机的用户目录下

2.引用这个文件,需要在.gitconfig 文件中进行如下配置
[user]
	name = Layne
	email = Layne@atguigu.com
[core]
	excludesfile = C:/Users/asus/git.ignore
注意：这里要使用“正斜线（/）”，不要使用“反斜线（\）”.这是因为Git是Linux思维,window复制的文件地址链接无法实现需求
```

### 4.2 过滤文件的编写规则

```bash
1 . 忽略特定文件或目录：
文件名.txt     # 忽略特定文件
目录名/        # 忽略特定目录

2 . 使用通配符进行忽略操作 
*：匹配零个或多个字符。
*.log         # 忽略所有以.log结尾的文件

3 . ?：匹配一个字符。
temp?.txt      # 忽略temp1.txt、temp2.txt等文件

4 . **：匹配多级目录。
logs/**/*.log # 忽略所有子目录中的.log文件

5 . 使用反斜杠\来转义特殊字符：
file\?.txt     # 忽略文件名为file?.txt的文件

6 . 使用#来添加注释：
# 这是一个注释
*.tmp          # 忽略所有.tmp文件

7 . 使用!来取反忽略规则，允许特定文件或目录被纳入版本控制：
*.log
!important.log # 忽略所有.log文件，但不忽略important.log文件

8 . 使用/来指定相对路径，以确保只在特定目录下生效：
/build/        # 只在项目根目录下的build目录中生效

9 . 使用[...]来匹配字符范围：
[123].txt      # 匹配1.txt、2.txt、3.txt，忽略其他.txt文件

10 . 使用|来分隔多个规则：
Copy code
*.log|debug.log # 忽略所有.log文件，但不忽略debug.log文件
```

## 5.克隆远端代码

远端的代码是被保存在Github服务器、Gitee服务器、或者是自行搭建的gitlab上.这个时候如若想要获取到相应的代码应该怎么做？

答：将远端代码下载到本地即可。

```
//获取当前链接的代码 https://github.com/mmdxhg/Git_Command_yxf.git
1.直接下载，可以通过进入当前链接获取
```
![image]([https://github.com/lexsaints/powershell/blob/master/IMG/ps2.png](https://github.com/mmdxhg/Git_Command_yxf/blob/master/pic/image-20231004141428851.png))

```
2.通过clone命令获取
git clone https://github.com/mmdxhg/Git_Command_yxf.git

3.通过pull命令获取，不过这种下载方式需要git init 初始化一下
git pull https://github.com/mmdxhg/Git_Command_yxf.git master
```

