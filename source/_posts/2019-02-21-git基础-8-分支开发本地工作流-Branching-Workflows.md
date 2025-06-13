---
title: git基础(8)-分支开发本地工作流(Branching Workflows)
categories: 技术总结
date: 2019-02-21 16:41:02
tags: git
---

由于分支的便捷，用于开发中，渐渐衍生出了基于用分支进行开发的工作模式。最典型工作流：

- [Git Flow](https://nvie.com/posts/a-successful-git-branching-model/)
- [GitHub Flow](https://guides.github.com/introduction/flow/)
- [GitLab Flow](https://docs.gitlab.com/ee/workflow/gitlab_flow.html)

但是，此文不会介绍，因为现在小编根本不懂，对此也很迷。 如想简单了解，请查看 [Git 工作流程简介](http://www.ruanyifeng.com/blog/2015/12/git-workflow.html)  
本文，主要讲下 长期分支、特性分支、远程分支

### 长期分支

根据项目开发的不同阶段，同时有多个开发的分支，且是长期的，一般不会删除。再定期地把某些特性分支合并入其他分支。典型方式: 根据不同层次的稳定性，建立分支

- master 分支:保留完全稳定的代码，已经发布
- release 分支： 要发布的分支，发布且经过一段时间正式运行稳定后，合并入 master 分支
- test 分支: 测试的分支，经过测试后，合并入 release 分支
- develop 分支：开发分支,开发人员测试后，合并入 test 分支

**这么做使分支具有不同级别的稳定性，当分支具有一定程度的稳定性后，再把它们合并入具有更改级别稳定性的分支中。**

### 特性分支

当工作被分散到不同的流水线中时，需要快速并且完整的在不同流水线中切换。而使用特性短期分支，针对不同流水线中的工作创建与之相关的分支，在不同分支间切换 来实现单一特性的工作。在特性分支修改完成后，再合并，而不用在乎它们建立的顺序或工作进度。

![多个特性分支](/image/moreMegre.png)

### 远程分支

- 分支(branch): z 指向某个提交对象的可变指针
- 跟踪分支(tracking brnch): 本地的分支，用户可写
- 远程跟踪分支(remote tracking branch): 远程分支状态的引用（指向远程分支的指针），用户只读，本地不能移动,在网络通信操作中，会自动移动。以 `[remote]/[branch]` 形式命名

#### git clone 过程

- 克隆前【远程仓库】的状态： 远程仓库`master`分支指向`C2`的 commit

  ![远程仓库](/image/remote_repo.png)

- 将数据克隆到本地之后

  - 首先 生成一个 origin/master 的指针指向最新的提交 C2.这个 origin/master 就是远程跟踪分支，用户只读
  - 然后 生产一个和 orign 的 master 分支指向同一提交 C2 的本地 master 分支.

  ![本地仓库](/image/local_repo.png)

#### git push 过程

- 克隆后，修改本地文件并提交。此时 本地 master 分支指向 C3
- ![git push before](/image/push1.png)

- `git push` 对远程和本地都做成修改

  - 修改远程的 master 分支 指向 至 C3

    ![git push remote](/image/push2.png)

  - 修改本地的 远程跟踪分支 orign/master 指向 C3

    ![git push local](/image/push3.png)

#### git fetch + git merge 过程

`git fetch`从远端仓库抓取本地没有的数据，并更新本地数据库，移动 origin/master 指针指向新的、更新后的位置。**但不会修改本地文件， 需要显式的合并**

- fetch 之前，远程 处于 C4, 本地处于 C3

  ![fetch before](/image/fetch1.png)

- fetch 之后，本地的 origin/master 分支指向了最新的 C4

  ![fetch after](/image/fetch2.png)

- 通过 `git merge origin/master` 合并本地分支 ,merge 之后，本地 master 分支指向 C4.

  ![merge origin/master](/image/fetch_megre.png)

#### git pull 过程

`git pull = git fetch + git merge`，本地仓库更新 origin/master 分支，将其指向 C4，再将其，合并入本地 master 分支,使得本地 master 分支也指向 C4

- git pull 之前，远程在 C4， 本地在 C3

- ![git pull before](/image/fetch1.png)

- git pull 之后， 本地仓库更新了 origin/master，将其指向 C4;然后更新本地 master 分支，将其指向 C4

  ![git pull before](/image/pull2.png)
