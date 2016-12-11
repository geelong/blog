---
title: git使用
date: 2016-11-01 15:58:34
tags:
---


```bash
初始化
git init

提交到本地库
git add $filename
git commit -m $msg $filename


查看diff
git diff $filename

查看修改
git log




```

#回滚

git reset --hard HEAD^

- HEAD^ 代表回到head前一个版本
- **只是回滚了本地库，并没有回滚工作目录**

git checkout -- $filename

- 回滚工作目录，回滚后，和index一致


### 丢弃工作区的改动
- git checkout -- <文件>...

### 撤出暂存区
- git reset HEAD <文件>...

### 工作区提交到index
- git add <文件>...

### index提交到本地库
- git commit -m $msg $file


### 查看提交到本地库的日志
- git reflog (refrence log)

### 回滚到某个版本
- git reset --hard $commentId

---
## 分支管理
### 创建dev分支，然后切换到dev分支：
- git checkout -b dev

### 查看当前分支
- git branch


### 切换回master分支
- git checkout master

### 分支合并
- git merge dev
	- 把dev的修改，合并到当前分支上
	- 没有冲突的话，是直接在本地库上合并的，不需要提交

### 删除dev分支
git branch -d dev 


---

### 当前分支和fe分支比较diff
 git diff fe

### 帮助命令
git help diff

- 最后是具体命令


### 查看远程信息
git remote -v


### 新建远程分支

1. git checkout -b dev
	2. 新建本地分支
2. git branch --set-upstream-to=origin/dev
	3. 本地分支和远程分支关联
4. git push origin dev
	5. 推送本地修改到远程分支

> 步骤2可省略，步骤3push的时候，指定远程分支名字就可以


### 删除远程分支
git branch -r -d origin/dev
	
### 查看本地分支对应的远程分支

git branch -vv	

---
	
### 如何关联到一个远程分支？

 git push --set-upstream origin test2


```bash
设置之前
 git br --v
  master 71d0d31 first commit from svn export
  test1  c82f750 test1
* test2  000bb8b test2

 git push --set-upstream origin test2
 
设置之后
git br -vv
  master 71d0d31 [origin/master] first commit from svn export
  test1  c82f750 [origin/test1] test1
* test2  000bb8b [origin/test2] test2
```

设置完后，下次提交直接 git push 就好


---

冲突解决

[merge tool设置](https://git-scm.com/book/zh/v1/%E8%87%AA%E5%AE%9A%E4%B9%89-Git-%E9%85%8D%E7%BD%AE-Git)
