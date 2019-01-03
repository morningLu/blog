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

2. 远程仓库
```
git remote -v  # 查看远程仓库信息
git remote set-url origin remote_git_address  # 设置远程仓库的url对应github中的ssh
```

3. 拉取代码
```
git pull  # 拉取代码
git pull --rebase  # 再推代码远程代码发生改变时要先rebase下 log好看一点
```

4. 提交代码
```
git status  # 查看修改的文件
git diff  # 查看修改内容
git add .  # 代码从工作区上交到缓冲区
git commit -am "log"  # 代码从缓冲区提交的本地仓库
git commit --amend  # 修改最近一次commit
git push  # 代码从本地仓库推到远程仓库
git push -f  # 以本地代码为准强制推送到远程仓库
```

5. 查看代码修改日志
```
git log  # 查看commit log
git diff commit_old commit_new  # commit_new和commit_old的差异
git show commit  # 查看某次提交的内容
```
6. 分支
```
git branch  # 查看本地分支
git branch -r  # 查看远程分支
git branch -a  # 查看全部分支
git checkout branch_name  # 切换分支
git checkout -b new_branch_name  # 从当前分支切出新分支
git push --set-upstream origin branch  # 本地分支和远程分支关联
git branch -D branch_name  # 删除本地分支
git push origin --delete branch_name  # 删除远程分支
```

7. 合代码
```
git merge branch_name --no-ff  # 把branch_name合入当前分支（log好看点）
git rebase branch  # rebase想要的分支
git rebase --continue  # 继续rebase
git rebase --abort  # 回退到rebase、merge之前
git commit  # 把有冲突的注释打开
git push  # merge和rebase同一分支的时候(远程rebase到本地)时用这个
git push -f  # rebase别的分支的时候用这个
```

8. 回退代码
```
git checkout .  # 去掉本地修改（慎用另外也不要开多个gitbush血的教训）
git reset --hard  # 回退到最近的一次commit
git reset --hard commit  # 回退到某个commit
```

9. 本地保存
```
git stash save 1  # 把修改保存到本地
git stash list  # 查看保存在本地的修改
git stash pop  # 弹出最近的一次保存
```

10. 背锅侠（好像不用rebase&--no-ff的时候查不出来 未验证）
```
git blame ./path/file_name | grep 'code' -a5  # 查看code的修改人和修改时间
```

11. init
```
git init  # 在你想要纳入git管理的文件夹下执行 然后关联远程仓库就可以使用git了
```

12. git 忽略某个文件(要在代码的根目录，不要是软连接)
```
vim .git/info/exclude  # windows
vim .gitignore         # linux
```
