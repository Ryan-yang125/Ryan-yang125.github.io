---
layout: post
title: "How use github in vscode"
subtitle: "如何在vscode中使用git和github"
date: 2020-11-25
author: "RyanYang"
header-img: "img/post-bg-default.jpg"
tags: [工具,github,vscode,git]
---

#### Init locally and push to GitHub

- from scrach

```bash
#config your name and email
git config --global --user.name abc
git config --global --user.email 123@abc.com
#list if you had done it
git config --global --list #show your username and email
```

- init locally

```bash
git init my-project#init empty git repo in current folder
#or just in the vscode gui git init
```

- add and commit

```bash
#stage changed means workplace to stagin area
git add .
#or
git add your-filename
#commit means staging area to your local repo
git commmit -m 'commit once'
```

- push to a remote repo

```bash
#you had to init a repo in github first
git remote add origin(foryour remote repos) https://your-pos.git
git push -u origin master（your-branch-name）
```

- pull to update

```bash
git pull
```

Then you can push/commit

#### Clone from others' repo

- clone

```bash
git clone xxx.git
```

- Branches

```bash
#show all branches and where you are
git branch
# create
git branch your-branchname
# switch
git checkout another-branch
# merge
git merge branch-name# now this branch is merged to master
git branch -d newtest
# merge conflict
# after fixed
git status -s
git add fixed-file
git status -s
git commit
```

假设 ABC 都拥有一个仓库的权限，该仓库目前有 master branch

如果两人都直接使用 master branch，那么很有可能造成冲突，比如 A 删掉 B 需要的文件，C 添加 A 已经实现的功能。

所以需要各自创建并切换到自己的 branch，在 push 以前先 pull 当前 master branch 的内容，查看其他人的修改，再据此修改、提交、推送自己的内容。

如果没有权限，create pull request。

- 不过几个人的小项目就不用这样了，只要会 add/commit/push/pull 就可以了，或者是自己本地写好上传到 github。