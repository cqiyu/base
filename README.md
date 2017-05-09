
本文演示如何在本地创建仓库和分支仓库，然后上传到GitHub,环境假设如下：

演示系统：Mint 17 Linux   
本地仓库：/doc/lihaixin/base   
远端仓库地址：https://github.com/lihaixin/base.git    
已经在本地配置好Git环境了，具体可以参考昨天的推文

本文主要内容


1. 创建一个GitHub仓库
2. 创建一个本地仓库
3. 推送本地仓库内容到GitHub仓库
4. 本地增加一个分支，推送到GitHub
5. 在另外一台电脑克隆仓库进行分支操作修改

### 一、创建一个GitHub仓库
登录GitHub创建一个base仓库，见下图，这里就不详细描述了，注意不勾选初始化生成README,防止冲突
![在GitHub创建一个base 仓库](https://raw.githubusercontent.com/lihaixin/base/master/images/1.png)

### 二、创建一个本地仓库

#### 2.1 初始化本地仓库
```
cd /doc/lihaixin/
Gitname=base
mkdir $Gitname
cd $Gitname
mkdir images
git init
echo # $Gitname > README.md
echo ".git" >.dockerignore
echo "" >.gitignore
echo "" >README.md
```

#### 2.2 创建一个Dockerfile文件
```
li@mint /doc/lihaixin/base $ vi Dockerfile 
#
# Dockerfile for base:alpine3.5
#

FROM alpine:3.5
MAINTAINER Lee <noreply@lihaixin.name>
LABEL "vendor"="lihaixin.name" \
      "author"="Haixin Lee"
```

#### 2.3 测试Dockerfile
输出如下信息 

```
li@mint /doc/lihaixin/base $ docker build -t lihaixin/base:alpine3.5 .
Sending build context to Docker daemon  4.608kB
Step 1/3 : FROM alpine:3.5
3.5: Pulling from library/alpine
627beaf3eaaf: Already exists 
Digest: sha256:58e1a1bb75db1b5a24a462dd5e2915277ea06438c3f105138f97eb53149673c4
Status: Downloaded newer image for alpine:3.5
 ---> 4a415e366388
Step 2/3 : MAINTAINER Lee <noreply@lihaixin.name>
 ---> Running in 7f70c00e35fe
 ---> 4df3ed812db3
Removing intermediate container 7f70c00e35fe
Step 3/3 : LABEL "vendor" "lihaixin.name" "author" "Haixin Lee"
 ---> Running in 2dcb0de7c05c
 ---> c4333225710c
Removing intermediate container 2dcb0de7c05c
Successfully built c4333225710c
Successfully tagged lihaixin/base:alpine3.5

```

查看生成的镜像


```
li@mint /doc/lihaixin/base $ docker images lihaixin/base
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
lihaixin/base       alpine3.5           c4333225710c        5 minutes ago       3.99MB
li@mint /doc/lihaixin/base $ 
```

运行容器，查看版本号
```bash
li@mint /doc/lihaixin/base $docker run --rm lihaixin/base:alpine3.5 cat /etc/alpine-release
3.5.2
```

### 三、同步本地仓库到远端GitHub

查看当前状态
```
li@mint /doc/lihaixin/base $ git status
位于分支 master

初始提交

未跟踪的文件:
  （使用 "git add <文件>..." 以包含要提交的内容）

	.dockerignore
	.gitignore
	Dockerfile
	README.md

提交为空，但是存在尚未跟踪的文件（使用 "git add" 建立跟踪）

```
向暂存区中添加文件然后重新查看状态
```
li@mint /doc/lihaixin/base $ git add --all
li@mint /doc/lihaixin/base $ git status
位于分支 master

初始提交

要提交的变更：
  （使用 "git rm --cached <文件>..." 以取消暂存）

	新文件:       .dockerignore
	新文件:       .gitignore
	新文件:       Dockerfile
	新文件:       README.md

li@mint /doc/lihaixin/base $ 
```
根提交
```
li@mint /doc/lihaixin/base $git commit -a -m "first commit"
master （根提交） 15934b7] first commit
 4 files changed, 11 insertions(+)
 create mode 100644 .dockerignore
 create mode 100644 .gitignore
 create mode 100644 Dockerfile
 create mode 100644 README.md
```
再次查看状态，可以看到一个干净的工作区
```
li@mint /doc/lihaixin/base $ git status
位于分支 master
无文件要提交，干净的工作区
```
配置上传到远端GitHub仓库,

```
git config --global user.email "lihaixin@15099.net"
git config --global user.name "lihaixin"
cat ~/.gitconfig 
[user]
	email = lihaixin@15099.net
	name = lihaixin
[http]
	proxy = http://192.168.3.106:8123
[https]
	proxy = https://192.168.3.106:8123
[color]
	ui = auto
```
添加origin标识符为远端仓库名称
```
li@mint /doc/lihaixin/base $git remote add origin https://github.com/lihaixin/base.git
li@mint /doc/lihaixin/base $ cat .git/config 
[core]
	repositoryformatversion = 0
	filemode = false
	bare = false
	logallrefupdates = true
[remote "origin"]
	url = https://github.com/lihaixin/base.git
	fetch = +refs/heads/*:refs/remotes/origin/*
```
推送到远端GitHub
```
git push -u origin master
Username for 'https://github.com': <--输入用户名
Password for 'https://lihaixin@github.com': <--输入密码
Counting objects: 5, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (5/5), 435 bytes | 0 bytes/s, done.
Total 5 (delta 0), reused 0 (delta 0)
To https://github.com/lihaixin/base.git
 * [new branch]      master -> master
分支 master 设置为跟踪来自 origin 的远程分支 master。
```
可以看到上面把本地的master分支上传到远端的master分支


### 四、新建一个ubuntu16.04分支

#### 4.1 查看本地分支情况

```
li@mint /doc/lihaixin/base $ git branch
* master
```
#### 4.2 新建一个本地分支ubuntu16.04

```
li@mint /doc/lihaixin/base $ git checkout -b ubuntu16.04
切换到一个新分支 'ubuntu16.04'
li@mint /doc/lihaixin/base $ git branch
  master
* ubuntu16.04
li@mint /doc/lihaixin/base $
```

#### 4.3 然后修改分支内容


```
 $ vi Dockerfile 
#
# Dockerfile for base:ubuntu16.04
#

FROM ubuntu:16.04
MAINTAINER Lee <noreply@lihaixin.name>
LABEL "vendor"="lihaixin.name" \
      "author"="Haixin Lee"
```
#### 4.4 验证dockerfile是否正确
```
li@mint /doc/lihaixin/base $ docker build -t lihaixin/base:ubuntu16.04 .
Sending build context to Docker daemon  5.632kB
Step 1/3 : FROM ubuntu:16.04
16.04: Pulling from library/ubuntu
aafe6b5e13de: Already exists 
0a2b43a72660: Already exists 
18bdd1e546d2: Already exists 
8198342c3e05: Already exists 
f56970a44fd4: Already exists 
Digest: sha256:f3a61450ae43896c4332bda5e78b453f4a93179045f20c8181043b26b5e79028
Status: Downloaded newer image for ubuntu:16.04
 ---> f7b3f317ec73
Step 2/3 : MAINTAINER Lee <noreply@lihaixin.name>
 ---> Running in 3deb0b22fd40
 ---> 552067c91f69
Removing intermediate container 3deb0b22fd40
Step 3/3 : LABEL "vendor" "lihaixin.name" "author" "Haixin Lee"
 ---> Running in d10456510147
 ---> 50451e045f77
Removing intermediate container d10456510147
Successfully built 50451e045f77
Successfully tagged lihaixin/base:ubuntu16.04
li@mint /doc/lihaixin/base $ 
```


#### 4.4 添加分支内容
```
li@mint /doc/lihaixin/base $ git status
位于分支 ubuntu16.04
要提交的变更：
  （使用 "git reset HEAD <文件>..." 以取消暂存）

	修改:         Dockerfile

li@mint /doc/lihaixin/base $ git commit -a -m "Change to ubuntu:16.04 base"
[ubuntu16.04 e81e626] Change to ubuntu:16.04 base
 1 file changed, 2 insertions(+), 2 deletions(-)
li@mint /doc/lihaixin/base $ git status
位于分支 ubuntu16.04
无文件要提交，干净的工作区
li@mint /doc/lihaixin/base $ 
```
#### 4.5 推送到远端GitHub ubuntu16.04分支下

```
li@mint /doc/lihaixin/base $ git push -u origin ubuntu16.04
Username for 'https://github.com': <--输入用户名
Password for 'https://lihaixin@github.com': <--输入密码
Counting objects: 5, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 387 bytes | 0 bytes/s, done.
Total 3 (delta 1), reused 0 (delta 0)
remote: Resolving deltas: 100% (1/1), completed with 1 local object.
To https://github.com/lihaixin/base.git
 * [new branch]      ubuntu16.04 -> ubuntu16.04
分支 ubuntu16.04 设置为跟踪来自 origin 的远程分支 ubuntu16.04。
```


#### 4.6 检验一下远端是否有分支

![github branch](https://raw.githubusercontent.com/lihaixin/base/master/images/1.png)


### 五、在另外一台电脑上克隆仓库和获取最新的远程分支

下面是操作命令，我就不把输入列出了
```
git clone https://github.com/lihaixin/base.git
git branch -a
git checkout -b ubuntu16.04 origin/ubuntu16.04
```

修改分支内容然后提交更改
```
vi somefile
git diff
git add --all
git commit -am "Add change from second computer"
git pull  
```

