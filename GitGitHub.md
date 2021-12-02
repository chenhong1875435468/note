# Git/Github Turtiol

## 一，版本控制

##### 定义：

​	是管理文件、目录或工程等内容修改历史，方便查看更改历史记录，备份以及恢复以前版本的软件工程技术

##### 功能：

~~~
实现跨区多人协同开发

追踪和挤在一个或者多个文件的历史记录

组织和保护你的源代码和文档

统计工作量

并行开发，提高开发效率

跟踪记录整个软件的开发过程

减轻开发人员的负担，节省时间，同时降低人为错误
~~~

​	简单的说就是用于管理多人协同开发项目成本的技术

##### 常见工具：

​	Git，SVN，CVS，VSS，TFS，VisualStudioOnline

##### 版本控制分类：

​	1，本地版本控制：RCS

​		记录文件每次的更新，可以对每个版本做一个快照，或者记录补丁文件，适合个人用

​	2，集中版本控制：SVN

​		所有的版本数据都保存在服务器上，协同开发者从服务器上同步更新或上传自己的修改

​	3，分布式版本控制：Git

​		所有版本信息仓库全部同步到本地的每个用户，这样就可以在本地查看所有版本的历史，可以离线在本地提交，只需要在联网时 push 到相应的服务器		或者其他用户那里，有1与每个用户那里保存的都是所有版本的数据，只要有一个用户的设备没有问题就可以恢复所有的数据，但这增加了本地存储空间		的占用。

​		特点：每个人拥有全部的代码，不会因为网络问题不能使用。但有风险，员工可能会跑路。

##### 面试题——Git和SVN区别：

​	1，前者是分布式版本控制系统，后者是集中式版本控制系统

​	2，前者互相随便改，改完发送给对方；后者需要在中央服务器获得最新版本，完成工作后在推送给中央服务器，且必须联网工作。



## 二，Git 基本配置

1，查看所有配置

~~~git
$ git config -l
~~~

2，查看系统配置

~~~git
$ git config --system --list
~~~

3，查看全局配置

~~~git
$ git config --global --list
~~~

​	配置文件地址：Git\etc\config

​	用户配置地址：User\Administrator\\.gitconfig

~~~git
[user]
	name = DengBowen
	email = 2631702650@qq.com
~~~

4，配置 git 用户

~~~git
$ git config --global user.name "DengBowen"
$ git config --global user.email "2631702650@qq.com"
~~~



## 三，Git 工作原理

##### 工作区域：

​	Git 有三个工作区域：工作目录，暂存区，资源库，如果加上远程的 Git 仓库就可以分为四个工作区域。四个区域间转换关系如下：

​	Working Directory(工作区) 通过 git add files 存入 Stage\index（暂存区）

​	Stage\index（暂存区） 通过 git commit 存入 Repository（本地资源库）

​	Repository（本地资源库） 通过 git push 存入 Remote Directory（远程仓库）

​	

​	Remote Directory（远程仓库）通过 git pull 取到 Repository（本地资源库）

​	Repository（本地资源库）通过 git pull 取到 Stage\index（暂存区）

​	Stage\index（暂存区）通过 git pull 取到 Working Directory(工作区)

##### 本地资源库：

​	在 git 本地文件中会有一个叫 HEAD 文件，里面 ref: refs/heads/master 指向主分区，一个 Git 有一个主分支，但代码一般提交到自己的分支

##### 远程资源库：

​	一般为 GitHub 和 Gitee ，代码托管的服务器

​	也可以自己搭建自己的 git 服务器，比如通过下载 GitLab 并部署在 Linux 上。

##### ***\*分支***：

​	1，查看分支：

~~~git
git branch
~~~

​	2，查看远程分支

~~~git
git branch -r
~~~

​	3，新建分支

~~~git
git branch [name]
~~~

​	4，新建并切换分支

~~~git
git checkout -b [name]
~~~

​	5，合并分支到当前分支

~~~git
git merge [name]
~~~

​	6，删除分支

~~~git
git branch -d [name]
~~~

​	7，删除远程分支

~~~git
git push origin --delete [name]
#或者
git branch -dr [name]
~~~



多个分支如果并行执行，就可以避免代码冲突，即各改各的

## 四，Git 项目搭建

##### 本地仓库搭建：

​	在 Git Bash 中使用命令：

~~~git
 git init
~~~

##### 克隆远程仓库：

​	在 Git Bash 中使用命令：

~~~git
git clone 网址...
~~~

## 五，Git 文件操作

##### 提交本地文件：

​	1，cd 文件夹至项目文件夹内

​	2，通过以下命令查看文件是否被跟踪等状态

~~~~git
git status
~~~~

​	3，通过以下命令添加单个被追踪文件

~~~~git
git add [filename]
~~~~

​	4，通过以下命令添加全部文件为被追踪状态

~~~~git
git status.
~~~~

​	5，通过以下命令提交暂存区中被追踪的文件

~~~git
git commit -m "new file [filename]"
~~~



##### 忽略指定文件：

​	在进行提交时通过忽略文件将 数据库文件，临时文件，设计文件等 从提交文件夹中提出

​	在主目录下建立 .gitignore 文件，此文件有以下规则：

​		1，忽略文件中的空行或者以 # 开头的行

​		2，可以使用 Linux 通配符，例如：* 代表任意多个字符，？代表一个字符，[] 代表可选字符范围，{} 代表可选字符串。

~~~git
*.txt	忽略所有txt格式文件
!lib.txt 	lib.txt除外
/temp	忽略 temp 下的文件
temp/	忽略 temp 以上文件
~~~

​		3，如果名称的最前面有一个 ！，表示例外规则，不会被忽略

​		4，如果名称中最前面一个是 / ，表示要忽略的文件再次目录下，而且子目录的文件不忽略

​		5，如果名称最后面一个是 / ，表示要忽略的是此目录下改名成帝子目录

​	IDEA会自动生成 gitignore 文件



## 六，使用 Git/Gitee/GitHub

##### 创建 gitee 仓库：

1，记住自己的公钥：

我的 联想小新 Pro16 Windows11X64系统 公钥是：

~~~git
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCfMl7qypPAVJ2Cc3tMt786uTs2ZwwTTo02QrE5YOBt2HFFCszvqy/+EXYYyYvFXWbHM65JLSriZ8eoXmxPrdDZAHcAK/BZ6FEvKthwOyaNKQDBHAgHcEpCv1tdHD8+mjFH1PrxJY9cNSdjGBVMlikxHgZbubnjPWm+88pvTVPBFOtDhLHZFwz/OH9trdydbIxHQuWRAvpOkjCBVryoHJ40ogxF9xgU7XcjwSQoHWIeqOdfdqHLd2BCUP1+E4x4dq4bJzJVv61J6tgsgUd/jwCjstjA5aokeY1uxS/3n94LX+l/61Bb3x+1Zk5vhZLJXYRv66fZO87ezzL1DfOIcDNK7vz1k+svat7M+aEvHHUGxBViDn5Ize/Z7HmAN5bz10N+1hGEKlbaQwgk5ycjwsCiSMNfmZ+YxM4LTerlTOwp8qMdU9oYQ6OWwc0T9NkspW2bDy+Zu0uy7f7NCacBP0rn6LTlooMdHx55jFFu6Eq9V3Er+P2qfsGpz8BvaAuWiYM= 26317@LAPTOP-507U88O4
~~~

我的 联想小新 Pro16 Ubuntu20.04.3系统 公钥是：

~~~git
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCnD8EfoRmAcHlGAl+UDPKxsOqFGRb/dCProIUsm+j0jwZ2QuLasIe1IFLPAzu45ECf+2CBOFGxU2c+oH9Ii7mnJO8R+JnRdurbf+u2eFSlUuPxeHFrY3BAuuhZrhfzne9Saccah/u4fGBAxxe366B6x0JRm/TkYJ7gl3Yx3qBR+uLF/pSifUiyC2NsBYJnlLcT57tskhwR+wYuA1FcxECvkiqR8DgXf8txTcTiRbAxNp+uJXGhKzXnb+AHuY2SBDz9D4I6+xso//fju7WT4Sryr3NzpWetzEBLjLb7Jjbx3mHtatkik+XKyPlYT/Ay8LApcSE2QiUSYxzqywYtEUCEtZ862Sf9zsohCBi838mdoQoSvjQupluC5Fd2w/0Y7RlqpUG17miEJQyQQJqqRDCKC5nWtJNtOHUNBLEvxP2pUITbH/6tfX2qR9Iy13elqjSBCtMGZUolZZRaOCI8pRx/Z3AK2EV7PhokXC0X+AfdRO4UZRfWO17NCgYM8v5zo1c= 2631702650@qq.com
~~~



2，创建新的 git 仓库

​	注意许可证上显示的 开源是否可以随意转载，开源是否可以转载并且商用，不可以开源



3，看到仓库上克隆地址，复制，通过如下命令克隆到本地

​	例如：我的仓库地址：git@gitee.com:BowenExtendsDeng/hello-world.git

~~~git
git clone git@gitee.com:BowenExtendsDeng/hello-world.git
~~~



##### 在 IDEA集成 Git：

1，在 Git 文件夹下创建项目

2，把 Git 文件拷贝到项目文件夹下

3，IDEA 中红色文件夹表示被跟踪，绿色表示已提交

​	提交方式：右上角对勾，下方 Terminal 进行 git.commit 以及 git.push 操作

4，用命令提高效率

~~~git
git add.
git commit -m "new file ...."
git push
~~~



## 七，Git深入学习

​	gitee 网下方有更全的 Git 命令学习



## 八，GitHub操作

全球最大同性交友网站

##### 创建 GitHub 仓库：

​	

​	

