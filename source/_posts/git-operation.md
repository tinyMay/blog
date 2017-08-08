---
title: git的一些操作
date: 2017-08-07 12:14:03
description: git常用操作, 包括如何初始化git仓库，添加公钥，设置远端地址，回退版本，关联子git项目等
categories: 工具
tags: [git]
---

## 一，先来说些常用的非入门级的git操作
#### 1. git stash
- 你正在进行项目中某部分工作，此时又要转向另一个分支进行一些工作，但这个部分未完成不想提交
```
git stash
git checkout another_branch

```
- 需要在当前分支拉最新代码，但又想先隐藏本地修改，拉完再将本地代码拉出融合
```
git stash
git pull
git stash pop
```

#### 2. git提交
先添加要提交的文件，然后commit
- 添加所有改动的文件 ：git add .
- 添加某几个文件：git add file1 file2 file3
``` 
git add . && git commit -m "some note this commit"
等同于 git commit -am 'some note this commit'
```

#### 3. git pull --rebase的作用， 其与 git pull的区别
1. 先来说说它的使用
```
1，git pull --rebase
2，若有冲突，解决后git status看下改了哪些有冲突的文件
3，git add 相应冲突文件
4，git rebase —continue （若无冲突则2/3/4步可省略，这里意思就是拉线上代码并把冲突解决了，可以继续做本地修改了，不要忘记这句，如果真的不小心忘了，而且本地又做了修改并commit，后面有解决方法）
5，本地又做些修改
6，git add . && git commit --amend （这样可以和上一个commit合为一条）
7，git push

注意：
如果有执行2/3步却不小心忘了第4步，继续做了5/6步，当你想git push的时候，由于之前的rebase操作不完整，会无法push, 怎么办呢？低头看：

git reset --soft HEAD^ 
上面这句会把刚才你做的commit撤销掉，但又不会把commit的文件改动撤回，完美~你可以继续git status ，看需要加哪些改动文件接着commit去吧~
```
2. git pull --rebase 与 git pull到底有神马区别呢？
先上一张git pull的图
![git_merge_example](/uploads/git_merge_example.jpg)

##### 出现上图交叉线原因

	团队协作中，同伴在本地各自有新的提交，如果你在本地先commit后再git pull了同伴做的新的commit,按照 Git 的默认策略，如果远程分支和本地分支之间的提交线图有分叉的话（即不是 fast-forwarded），git 会执行一次 merge 操作，因此产生一次没意义的提交记录，从而造成了像上图那样的混乱。
##### 解决

假设执行pull前是这样的：
```
                 A---B---C  remotes/origin/master
                /
           D---E---F---G  master
```
如果执行`git pull`后，提交线图变成这样
```
                 A---B---C remotes/origin/master
                /         \
           D---E---F---G---H master
```
可以看到，多了`H`这个没必要的记录，但如果执行`git pull --rebase`，情况就不同了、
```
                       remotes/origin/master
                           |
           D---E---A---B---C---F'---G'  master
```
`F` `G`两个提交通过 `rebase` 方式重新拼接在 C 之后，多余的分叉去掉了，目的达到。
GIT常用命令及解释
- 需注意的是，使用 git pull --rebase 比直接 pull 容易导致冲突的产生，如果预期冲突比较多的话，建议还是直接 pull。

## 二，初始建仓

### 1. 在git项目内添加自己的公钥，这样不用每次提交都让填密码，先在电脑上找出
1. cat ~/.ssh/id_rsa.pub
2. 若上面步骤没有找到文件内容，说明还没有，进入3
3. ssh-keygen -t rsa -C "your_email@youremail.com"（即github上注册的邮箱地址）
4. 在github上把本电脑的公钥加入，此不赘述

### 2. 初始化仓库/和远程仓库链接/首次提交可能出现的问题
#### 1. 在文件夹内初始化本地git仓库
git init

#### 2. 和远程git仓连接
git remote add origin your_remote_git_project_address
注意，这里origin指的是所加远程地址的别名，也可以视为简写

#### 3. 设置本地和远程的分支对应
可能不同git版本不太一样，根据提示敲下面的某一种
方法一 ：git branch --set-upstream-to=origin/master
方法二： git push --set-upstream origin master
本地git init并关联远程新建的仓库时，初次上传有两个途径：
```
1. git push -u origin gaga 
2. git branch --set-upstream-to=origin/gaga master
```
这两种方式都可以达到目的。但是1方法更通用，因为你的远程库有可能并没有gaga分支，这种情况下你用2方法就不可行，连目标分支都不存在，怎么进行关联呢

#### 4. 最开始前git pull，把远程初始建仓的文件拉下来
可能会出现`fatal: refusing to merge unrelated histories`
这是由于历史不对应无法拉，可以强制执行
git pull  --allow-unrelated-histories

#### 5. 把本地修改后的文件放到暂存区
git add .
#### 6. git commit –m “说点啥”
#### 7. git push

## 三，常用操作
#### 1. 创建新的分支后，和远程的链接起来
git branch --set-upstream-to=origin/KMY(远程) KMY(本地)

#### 2. 强制推送
git push –f

#### 3. 回滚代码commit
- 硬回退，即commit和代码都被回滚
git reset –-hard 35as345(这是commitid哦，输前几个，能和其他的区别开就行了)
git reset –-hard HEAD^/ HEAD^^/ HEAD~2 / HEAD~4(回退到上1/2/2/4个版本
- 软回退，即commit回退，但改变的代码仍旧保留
git reset --soft 35as345 / HEAD^/ HEAD~2

#### 4. 忽略某些文件
git add --ignore-removal .DS_Store
注意：如果原先git中饭没有ignore且上传过，还要把它原来的删掉，因为之前有提交过，尚在git里
1. 移除本地被track的单文件
git rm --cached *.DS_Store
2. 移除本地被track的文件夹
git rm -r --cached vendor/*

#### 5. 切换分支
git checkout certain_branch

#### 6. 转移项目（从gitlab到oschina）

- 把gitlab上原来的项目公有化，去setting里面设置哦
- 在oschina上新建项目，导入已有（粘贴gitlab上代码地址）
- 在本地的该项目目录下设置远端url
git remote set-url origin remote_new_project_address

#### 7. 可能刚拉下来一个项目却推不上去，那就
git config receive.denyCurrentBranch ignore

#### 8. 本地有未stash的，又想忽略本地修改，用线上的
git fetch —all   (怎么感觉不用加all)
git reset —hard origin/develop  (origin指向本地git设的远端推拉地址，develop指向远端的某个分支

#### 9. git项目内建子git项目submodule

1. 为当前工程添加submodule，命令如下：
`git submodule add 仓库地址 路径`
其中，仓库地址是指子模块仓库地址，路径指将子模块放置在当前工程下的路径。
注意：路径不能以 / 结尾（会造成修改不生效）、不能是现有工程已有的目录（不能順利 Clone）

2. 当使用git clone下来的工程中带有submodule时，初始的时候，submodule的内容并不会自动下载下来的，此时，只需执行如下命令：
git submodule update --init --recursive

#### 10. 设置与查看remote url
1. 设置：git remote set-url short_name remote_project_address
2. 查看： git remote -v

#### 11. 自动化部署，在github上设置webhook
webhook用于自动化部署，比如某个git项目文件用于线上部署，想在push后自动感应接收，线上生效。
1. 设置位置: certain_repository => settings => Webhooks
Payload为需要接收的远端地址
2. 在服务器上，要去做相应的接收钩子，没做过相应设置，自行google吧

#### 12. Fork 后的仓库如何同步本仓库
1. 添加 upstream 源，只需执行一次
git remote add upstream primary_project_address
2. 拉取远程代码
git pull upstream master
3. 提交修改
git add . && git commit -m 'xxx'
4. 更新 fork 仓库
git push origin master
- 注意，这里origin指的是fork到自己的仓库地址，upstream指原项目地址即fork源

#### 13. 在本地删除远端分支
git push origin --delete release   删除远端release分支

#### 14. 设置大小写敏感
1. 在某个git项目文件夹内 vi .git/config  =>  ignorecase = false
2. 全局设置 vi .gitconfig => git config --global core.ignorecase false

#### 15. git reset 与 git revert 区别
  git revert 撤销 某次操作，此次操作之前和之后的commit和history都会保留，并且把这次撤销作为一次最新的提交
1. git revert是用一次新的commit来回滚之前的commit，git reset是直接删除指定的commit。
2. 在回滚这一操作上看，效果差不多。但是在日后继续merge以前的老版本时有区别。因为git revert是用一次逆向的commit“中和”之前的提交，因此日后合并老的branch时，导致这部分改变不会再次出现，但是git reset是之间把某些commit在某个branch上删除，因而和老的branch再次merge时，这些被回滚的commit应该还会被引入。
3. git reset 是把HEAD向后移动了一下，而git revert是HEAD继续前进，只是新的commit的内容和要revert的内容正好相反，能够抵消要被revert的内容。