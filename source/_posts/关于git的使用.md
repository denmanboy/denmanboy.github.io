---
title: 关于git的使用
date: 2015-04-20 15:06:48
toc: true
tags: iOS
categories: technology
description: 介绍Git和SVN的区别，Git简单使用
---
## Git和SVN
 一：Git是什么？
 
   Git是目前世界上最先进的分布式版本控制系统。
   
二：SVN与Git的最主要的区别？

   SVN是集中式版本控制系统，版本库是集中放在中央服务器的，而干活的时候，用的都是自己的电脑，所以首先要从中央服务器哪里得到最新的版本，然后干活，干完后，需要把自己做完的活推送到中央服务器。集中式版本控制系统是必须联网才能工作，如果在局域网还可以，带宽够大，速度够快，如果在互联网下，如果网速慢的话，就纳闷了。

   Git是分布式版本控制系统，那么它就没有中央服务器的，每个人的电脑就是一个完整的版本库，这样，工作的时候就不需要联网了，因为版本都是在自己的电脑上。既然每个人的电脑都有一个完整的版本库，那多个人如何协作呢？比如说自己在电脑上改了文件A，其他人也在电脑上改了文件A，这时，你们两之间只需把各自的修改推送给对方，就可以互相看到对方的修改了

三：理解工作区与暂存区的区别？

工作区：就是你在电脑上看到的目录，比如目录下testgit里的文件(.git隐藏目录版本库除外)。或者以后需要再新建的目录文件等等都属于工作区范畴。

   版本库(Repository)：工作区有一个隐藏目录.git,这个不属于工作区，这是版本库。其中版本库里面存了很多东西，其中最重要的就是stage(暂存区)，还有Git为我们自动创建了第一个分支master,以及指向master的一个指针HEAD。

我们前面说过使用Git提交文件到版本库有两步：

  第一步：是使用 git add 把文件添加进去，实际上就是把文件添加到暂存区。

  第二步：使用git commit提交更改，实际上就是把暂存区的所有内容提交到当前分支上。
  
  
      
## 简单使用

### 1、github创建个人仓库

      *首先打开github的网站，注册账号并登陆，在网页里找到New repository开始创建个人仓库

### 2、配置SSH Keys
      *ssh-Keygen -t rsa -C “yourEmail”  【输入完之后一直回车】

      *pbcopy < \~/.ssh/id\_rsa.pub  【拷贝配置的SSH Key】

      *在github的设置中添加一个SSH Keys

      *回到终端：ssh -T git@github.com

### 3、将本地项目上传至仓库
      *git init  【初始化并生成本地git管理】

      *git add xxx  【添加需要上传的文件】

      *git add.  【添加目录下的所有文件】

      *git commit -m “first commit”  【提交到本地仓库，“”内填的是提交注释】

      *git remote add origin https://github.com/denmanboy/YZValue.git  【为版本库添加名为origin的远程版本库】

      *git remote -v  【查看远程仓库】

      *git remote remove origin  【删除该项目的远程仓库】

      *git pull --rebase origin master  【将github仓库的文件和本地项目文件合并】

      *git push -u origin master  【执行推送命令，完成github版本库的初始化，注意命令行中的-u参数，在推送成功后自动建立本地分支与远程版本库分支的追踪】

### 4、项目管理

      *git clone https://github.com/denmanboy/YZValue.git  【克隆远程库到本地】

### 5、分支管理

      *git branch -a  【查看所有的分支】

      *git branch develop  【创建分支】

      *git checkout -b develop  【创建并切换到develop分支】

      *git checkout branchName  【切换到某分支】

      *git merge --no-ff develop  【把develop合并到master分支，no--ff选项的作用是保留原来分支的记录】

      *git branch -d develop  【删除develop分支】

      *git checkout -b dev origin/dev  【创建远程origin的dev分支到本地，并且命名为dev（）如果项目中没有origin/dev远程分支则创建失败】

      *git pull origin dev：dev  【取回origin主机的dev分支，与本地的dev分支合并】

      *git branch --set-upstream branch-name origin/branch-name  【可以建立起本地分支和远程分支的关联，之后可以直接git pull从远程抓取分支】

      *git push --delete origin branchName  【删除远程分支】

      *git show  【查看历史修改】

      *git rm --cached filename  【删除文件在暂缓区的状态】

### 6、版本回退

      *git reflog  【查看提交历史】

      *git reset --hard HEAD^  【回到上一个版本】

### 7、管理修改

      *git status  【查看状态】

      *git checkout -- filename  【放弃对某个文件的修改】

      *git reset HEAD --filename  【丢弃暂存区上某个文件的修改，重新放回到工作区】

      *git diff  【查看在工作区未提交到暂缓区的文件更新】

      *git diff --cached  【查看在暂存区文件的更新】

### 8、git的设置

      *git config user.name “xxx”  【设置commit的用户名】

      *git config user.email.com “xxx@xx.com”  【设置commit的邮箱】

