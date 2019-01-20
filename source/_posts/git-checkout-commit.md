---
title: git 切换 commit 的正确姿势
date: 2019-01-14 14:37:48
tags: git
---

## git checkout commit-hash

最近使用 git 进行版本切换，理所当然地使用了 `git reset`，然后发现自己非常艰难地切换回来……哪里出了问题，仔细一看 stack overflow，发现原来 `git checkout xxx` 才是切换 commit 的正确方法！

```bash
$ git log --oneline -3
c18f295 (HEAD) third commit
74aeb85 second commit
6a491ce first commit
```

如上，如果需要切换到倒数第二次提交，所应该使用的命令应该是 `checkout` 加提交的哈希值。

```bash
git checkout 74aeb85
```

输出

```
Previous HEAD position was c18f295... third commit
HEAD is now at 74aeb85... second commit
```

代表已经切换到了倒数第二次提交状态。**注意：此时，你的 HEAD 指针处于游离状态。不建议进行代码提交修改。**

<!--more-->

~~如果需要切换回来，需要最后一次提交的哈希值。此时，使用 `git log`  + `分支名` 来显示：~~

```bash
$ git log --oneline master
c18f295 (origin/master, master) third commit
74aeb85 (HEAD) second commit
6a491ce modify first commit
```

~~然后 `git checkout c18f295  ` 就可以将  HEAD 指针移动回最后的提交了。~~

**修正：git checkout 最后一次 commit hash 的做法是错误的，正确的做法是 git checkout 分支名。**

```bash
$ git checkout master
```

之前我错误地以为 git checkout commit-3 后再 git checkout checkout-1 就能切换回来，其实不然，**当前 HEAD 指针仍然处于游离状态。**

## HEAD 指针游离处理

如果不小心在指针游离的时候进行了提交，这次提交会留在匿名分支上，直接 push 某分支会发现 “already up to date” 的提示。

### 回到匿名分支

首先，如果你使用了 `git checkout master` 回到了正常分支，先使用 `git checkout 匿名分支commidId` 回到匿名分支（因为是匿名分支，所以当然没办法通过分支名 checkout，这里的 commit id 就是刚刚新提交时的 id）。

### 新建临时分支

使用命令 `git checkout -b tempbranch` 新建一个临时分支，用以固定匿名分支。

### 回到原分支并合并

`git checkout master` 回到主分支，然后 `git merge tempbranch` 就可以合并回来了。



---

## 参考

[Git HEAD detached from XXX (git HEAD 游离) 解决办法](https://blog.csdn.net/u011240877/article/details/76273335)

[How can I switch my git repository to a particular commit](https://stackoverflow.com/questions/4940054/how-can-i-switch-my-git-repository-to-a-particular-commit)

[Fix a Git detached head?](https://stackoverflow.com/questions/10228760/fix-a-git-detached-head)



PS: 原来 `git log` 命令的正确退出方法是按一下 “q” 键，我一直都用 `ctrl + c` 暴力退出的…


