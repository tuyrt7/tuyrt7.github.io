---
title: Git新建仓库-命令
date: 2019-10-11 09:24:51
tags:
	- blog
	- Git
---
# Git

### Command line instructions 

You can also upload existing files from your computer using the instructions below. 

## Git global setup

git config --global user.name "符涛"
git config --global user.email "jasson@bayair.com"

## Create a new repository
git clone git@c73905819740:xiaoke/newdevice.git
cd newdevice
touch README.md
git add README.md
git commit -m "add README"
git push -u origin master

<!--more-->

## Push an existing folder
cd existing_folder
git init
git remote add origin git@c73905819740:xiaoke/newdevice.git
git add .
git commit -m "Initial commit"
git push -u origin master


## Push an existing Git repository
cd existing_repo
git remote rename origin old-origin
git remote add origin git@c73905819740:xiaoke/newdevice.git
git push -u origin --all
git push -u origin --tags


