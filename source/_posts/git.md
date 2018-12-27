---
title: git
date: 2018-12-27 16:21:15
tags:
categories:
    - 技术(未分类)
---

1. 签名&邮箱
```
git config --global user.email "email.com"  # 邮箱
git config --global user.name "morninglu"  # 签名
```

2. 常用
```
git pull  # 拉取代码
git pull --rebase  # 再推代码远程代码发生改变时要先rebase下 log好看一点
--
git status  # 查看修改的文件
git diff  # 查看修改内容
git diff commit_old commit_new  # commit_new和commit_old的差异
git show commit  # 查看某次提交的内容
git add .  # 代码从工作区上交到缓冲区
git commit -am "log"  # 代码从缓冲区提交的本地仓库
git push  # 代码从本地仓库推到远程仓库
git push -f  # 以本地代码为准强制推送到远程仓库
--
git log  # 查看commit log
--
git checkout branch_name  # 切到新分支
git checkout -b new_branch_name  # 当前分支切到新分支
git push --set-upstream origin branch  # 本地分支和远程分支关联
--
git branch  # 查看本地分支
git branch -a  # 查看远程分支
--
git merge branch_name --no-ff  # 把branch_name合入当前分支
git branch -D branch_name  # 删除本地分支
git push origin --delete branch_name  # 删除远程分支
--
git remote -v  # 查看远程仓库信息
git remote set-url origin remote_git_address  # 设置远程仓库的url对应github中的ssh
--
git reset --hard commit  # 回推到
```
