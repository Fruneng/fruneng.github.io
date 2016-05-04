---
layout: post
title: "git工作流"
description: "pull request 使用"
category: git
tags: [git]
---

git作为开源多人协作的工具,已被广泛认可.我也学习了一下git开发的pull request的工作流模式.

我们要给一个开源项目提交代码, 首先要fork一个版本到自己的git仓库,
这样我们就在自己的git仓库中有了一个fork版本的项目.

![2016-01-20-gitfork.png](/assets/pic/2016-01-20-gitfork.png)

接着我们在自己项目中做一些修改， 并且提交到这个仓库中.

![2016-01-20-gitpullrequest.png](/assets/pic/2016-01-20-gitpullrequest.png)

点击new pull request, github上会生成一份两个版本之间差异的patch, 我们要做的是尽量
详细的把我们修改的意图,以及大概的思路写给项目的管理者, 他可能会给你一些建议, 可能会合并你的代码
到主分支, 或者拒绝你的代码.

![2016-01-20-pullrequest.png](/assets/pic/2016-01-20-pullrequest.png)