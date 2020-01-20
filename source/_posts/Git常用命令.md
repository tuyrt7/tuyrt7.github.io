---
title: Git常用命令
date: 2020-01-14 14:24:51
tags:
	- blog
	- Git
---

## 安装

- 配置用户名：git config --global user.name "Your Name"
- 配置邮箱：git config --global user.email "email@example.com"

## 创建

- Git仓库创建：git init

## 基础操作

- 添加：git add <file>
- 提交：git commit -m <message>
- 查看工作区：git status
- 查看修改内容：git diff
- 查看修改内容：git diff HEAD -- <file> （查看当前版本库与共工作区的file不同）

## 版本回退

- 回退上一个版本：git reset --hard HEAD^（`HEAD^^`就是上上版本，`HEAD~100` 回退100次）
- 回退指定版本：git reset --hard commit_id (commit_id只要前6位就可以)
- 查看提交历史：git log
- 查看提交历史：git log --pretty=oneline（添加--pretty=oneline，一行显示一次提交信息）
- 查看命令历史：git reflog （可以用来查看提交commit id，在回退后反悔）

<!--more-->
## git工作区、暂存区、版本库

- 第一步：git add（把文件添加进去，实际上就是把文件修改添加到暂存区）

- 第二步：git commit（提交更改，实际上就是把暂存区的所有内容提交到当前分支）

> 例如1：第一次修改 -> git add -> 第二次修改 -> git commit  
> 结果2： 第一次的修改被提交了，第二次的修改不会被提交  

> 例如1：第一次修改 -> git add -> 第二次修改 -> git add -> git commit  
> 结果2：第一、二次修改提交

## 撤销修改

- 丢弃工作区的修改：git checkout -- file （回到最近一次git commit或git add时的状态）

> 例如：命令git checkout -- readme.txt意思就是，把readme.txt文件在工作区的修改全部撤销，这里有两种情况：  
> 一种是readme.txt自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；  
> 一种是readme.txt已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。  

- 丢弃暂存区修改：第一步：git reset HEAD <file>，第二步：git checkout -- file。

## 删除

- 删除（提交暂存区）: git rm <file> （先手动删除文件，然后使用git rm <file>和git add<file>效果是一样的）
- git checkout -- <file> （可以撤销工作区文件手动删除）

## 关联远程、推送

- 关联一个远程库：git remote add origin git@server-name:path/repo-name.git

- 关联后第一次推送：git push -u origin master （第一次推送master分支的所有内容）

- 之后推送修改：git push origin master

## 克隆

- 克隆项目： git clone <url> （url是远程地址 SSH:git@server-name:path/repo-name.git）

## Git分支操作

- 查看分支：git branch

- 创建分支：git branch <name>

- 切换分支：git checkout <name>或者git switch <name>

- 创建+切换分支：git checkout -b <name>或者git switch -c <name>

- 合并分支（分支没有历史信息）：git merge <name>

- 删除分支：git branch -d <name>

> 当Git无法自动合并分支时，就必须首先解决冲突。解决冲突后，再提交，合并完成。  
> 解决冲突就是把Git合并失败的文件手动编辑为我们希望的内容，再提交。 

- 显示分支合并图: git log --graph --pretty=oneline --abbrev-commit

- 合并分支（分支有历史信息）：git merge --no-ff -m "merge with no-ff" <name> （-m 提交合并信息；--no-ff 禁用Fast forward）

> 结果：如果可能，Git会用Fast forward模式，但这种模式下，删除分支后，会丢掉分支信息。  
> 如果要强制禁用Fast forward模式，Git就会在merge时生成一个新的commit，这样，从分支历史上就可以看出分支信息。 

## bug分支操作

- 保存当前工作现场：git stash

- 恢复现场：git stash pop

> 流程：  
> git stash（保存工作分支） 	..-> 	git stash list（查看保存）	-> 		git checkout master（切换发布分支）		-> 		git checkout -b issue-101 （创建并切换bug分支）		-> 		..add .. commit（修复bug）		-> 		git switch master（切换发布分支）		-> 		git merge --no-ff -m "merged bug fix 101" issue-101（合并bug分支）		-> 		git switch dev（切换工作分支）		..-> 		git stash list（查看保存）		-> 		git stash pop（恢复保存）

> 注意：  
> 一是用git stash apply恢复，但是恢复后，stash内容并不删除，你需要用git stash drop来删除；  
> 另一种方式是用git stash pop，恢复的同时把stash内容也删了：  

- 恢复指定（stash@{0}）保存：git stash apply stash@{0}

master分支上修复的bug，想要合并到当前dev分支，把bug提交的修改“复制”到当前分支，避免重复劳动。
- 合并到当前dev分支：git cherry-pick <commit> （commit_id是bug分支issue-101的commit_id）

## feature分支

开发一个新feature，最好新建一个分支；

- 强行删除：git branch -D <name> （丢弃一个没有被合并过的分支，D要大写）

## 多人协作

- 查看远程：git remote

- 查看远程（详细信息）：git remote -v 

> 结果：  
> origin  git@github.com:michaelliao/learngit.git (fetch) （表示抓取地址）  
> origin  git@github.com:michaelliao/learngit.git (push)	（表示推送地址）  

- 推送本地master分支： git push origin master

- 推送本地dev分支： git push origin dev


应用：

> master分支是主分支，因此要时刻与远程同步；    
> dev分支是开发分支，团队所有成员都需要在上面工作，所以也需要与远程同步；   
> bug分支只用于在本地修复bug，就没必要推到远程了，除非老板要看看你每周到底修复了几个bug；   
> feature分支是否推到远程，取决于你是否和你的小伙伴合作在上面开发。  


_多人协作的工作模式_：

1、试图用git push origin <branch-name>推送自己的修改；

2、如果推送失败，则因为远程分支比你的本地更新，需要先用git pull试图合并；

3、如果合并有冲突，则解决冲突，并在本地提交；

4、没有冲突或者解决掉冲突后，再用git push origin <branch-name>推送就能成功！


- 本地分支和远程分支的建立链接关系：git branch --set-upstream-to <branch-name> origin/<branch-name>（如果git pull提示no tracking information，则说明无链接关系）

- 本地创建和远程分支对应的分支：git checkout -b branch-name origin/branch-nam（本地和远程分支的名称最好一致）

- 建立本地分支和远程分支的关联：git branch --set-upstream branch-name origin/branch-name；

从远程抓取分支，使用git pull，如果有冲突，要先处理冲突。

## Rebase

- 提交历史分叉转直线：git rebase

rebase操作可以把本地未push的分叉提交历史整理成直线；
rebase的目的是使得我们在查看历史提交的变化时更容易，因为分叉的提交需要三方对比。


## 标签

标签用来管理发布版本，发布时版本tag v1.2，按照tag v1.2查找commit就能导出那时发布 v1.2时源码

> 注意：标签总是和某个commit挂钩。如果这个commit既出现在master分支，又出现在dev分支，那么在这两个分支上都可以看到这个标签。

- 新建标签：git tag <tagname> （默认为HEAD，也可以指定一个commit id）

- 新建标签：git tag -a <tagname> -m "blablabla..." （-a指定标签名，-m指定标签信息）

- 查看所有标签：git tag

- 推送本地标签：git push origin <tagname>

- 推送全部未推送过的本地标签：git push origin --tags

- 删除本地标签：git tag -d <tagname>

- 删除一个远程标签：git push origin :refs/tags/<tagname>


## 简写命令

> 配置方法：  
> $ git config --global alias.st status （st就表示statu，）  
> $ git config --global alias.co checkout （co表示checkout）  
> $ git config --global alias.ci commit （ci表示commit）  
> $ git config --global alias.br branch （br表示branch）  
> $ git config --global alias.unstage 'reset HEAD'   
> $ git config --global alias.last 'log -1'  
> $ git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"  

> 配置后：   
> git st ====  git status  
> git co master ====  git checkout master  
> git ci -m "add" ====  git commit -m "add"  
> git br ====  git branch  
> git unstage test.py  ====  git reset HEAD test.py  
> git last ====  git log -1  
> git lg ====   


