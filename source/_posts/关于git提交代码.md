---
title: 关于git提交代码
date: 2019-05-22 11:28:25
tags: git
---

>**以下步骤是基于我公司的环境，你可以参考使用。**

1.首先要从git服务器下载digioceanfs_manager管理系统源代码必须现在git服务器上添加自己的ssh密钥，配置好密钥再去下载代码
```
git clone ssh://zhoutongle@10.10.1.13:29418/MGMT
```
<!-- more -->
2.这里提交的时候需要拷贝glusterfs的一个文件到当前项目.git/hooks/目录下
```
cp ~/glusterfs/.git/hooks/commit-msg   /root/MGMT/.git/hooks/ 
chmod +x commit-msg
```
3.配置好身份认证后，去创建一个自己bug的分支
```
git checkout -b bug_branch_name
```
4.配置好ssh密钥后，现在去配置身份认证
```
git config user.name "zhoutongle"
git config user.email "zhoutongle@estor.com.cn"
```
5.然后去修改自己要修改的代码文件
6.add添加文件
```
git add . 
or
git add *
```
7.commit已经add的文件
```
git commit -s
```
8.如果继续修改这个文件再次 commit
```
git commit -s --amend
```
9.然后将commit的代码push到远端。
```
git push origin HEAD:refs/for/CentOS-7-X86/rfc
```
```
[root@node-1 MGMT]# git branch -a    显示所有分支
  CentOS-7-X86
  master
* test
  remotes/origin/2.0
  remotes/origin/C7-devel
  remotes/origin/CentOS-7-ARM
  remotes/origin/CentOS-7-X86
  remotes/origin/HEAD -> origin/master
  remotes/origin/master
```
```
git checkout  CentOS-7-X86  切换到分支CentOS-7-X86
git checkout -b test  创建分支test在 CentOS-7-X86
git reset --hard ff648831a9547afb2d6e96213760d445aeb9e514
```

1.如果出现：
```
ERROR: missing Change-Id in commit message footer
```
直接在命令行加入报错的红色部分，再按照一下步骤来就ok了：
```
gitdir=$(git rev-parse --git-dir); scp -p -P 29418 zhoutongle@10.10.1.13:hooks/commit-msg ${gitdir}/hooks/
```
2.下载一份代码，修改之后提交代码，不小心把代码删除了，而无法进行追加提交的问题。
重新下载代码，下载补丁:
```
git reset --hard 90792628c1bd03c0245813057cfee361cae80094    （commit）
```
翻滚到之前的版本。
```
git pull origin CentOS-7-X86
```
3.合并的分支产生冲突问题。
 当你提交的代码后，管理员发现，您的代码不能提交到服务器上，主要原因在于，你的commit 中和服务器中的有些commit不再同一时间轴上，即：你的有些commit要插入到服务器中的某些commit之间，这样就会造成代码的冲突。所以这个 时候就要使用git rebase。
 假如，你平时使用的分支叫 new ，然后在这个分支上你刚提交过几个commit。
 做法：
 ```
    1.新建一个分支，并且代码和服务器中代码同步
    git checkout origin/v2.0 -b temp
    2.为了保证新建的temp分支代码是最新的，可以多执行下面一步
    git pull
    3.当你新建分支后，系统会自动checkout到temp分支上，此时
    git checkout  new
    4.合并代码，并整理
    git rebase  temp  //会将temp分支的代码合并过来，并按照提交的顺序排序
    5.因为顺序是重新整理的，所以肯定会出现冲突
    6.解决冲突，最后 
    git add *
    ，但不需要
    git commit
    7.解决后，执行
    git rebase --continue
    8.重新提交代码： 
    git push for-
```