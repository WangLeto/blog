---
title: git 切换 commit 的正确姿势
date: 2019-01-14 14:37:48
tags: git
---

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

代表已经切换到了倒数第二次提交状态。

<!--more-->

如果需要切换回来，需要最后一次提交的哈希值。此时，使用 `git log`  + `分支名` 来显示：

```bash
$ git log --oneline master
c18f295 (origin/master, master) third commit
74aeb85 (HEAD) second commit
6a491ce modify first commit
```

然后 `git checkout c18f295  ` 就可以将  HEAD 指针移动回最后的提交了。

PS: 原来 `git log` 命令的正确退出方法是按一下 “q” 键，我一直都用 `ctrl + c` 暴力退出的…

参考：

[How can I switch my git repository to a particular commit](https://stackoverflow.com/questions/4940054/how-can-i-switch-my-git-repository-to-a-particular-commit)

[Fix a Git detached head?](https://stackoverflow.com/questions/10228760/fix-a-git-detached-head)

