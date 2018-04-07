---
title: git操作备忘
date: 2016-05-16 16:47:16
tags:
---

[Git远程操作详解](http://www.ruanyifeng.com/blog/2014/06/git_remote.html)



# Git 协作流程

## master 分支

master 永远处于稳定状态，这个分支代码可以随时用来，部署。不允许在该分支直接提交代码。

## develop 分支

开发分支，包含了项目最新的功能和代码，所有开发都在 develop 上进行。一般情况下小的修改直接在这个分支上提交代码。

## feature 分支

如果要改的一个东西会有比较多的修改，或者改的东西影响会比较大，请从 develop 分支开出一个 feature 分支，分支名约定为`feature/xxx`，开发完成后合并回 develop 分支并且删除这个 feature 分支，相应的操作如下：

```
$ git checkout -b feature/xxx develop
# 写代码，提交，写代码，提交。。。
# feature 开发完成，合并回 develop
$ git checkout develop
# 务必加上 --no-ff，以保持分支的合并历史
$ git merge --no-ff feature/xxx
$ git branch -d feature/xxx
```

如果想要当前分支能保持与 develop 的更新，请用 rebase，操作如下：

```
# 假设当前在 feature/xxx 分支
$ git rebase develop
```

rebase 会修改历史，如果你的 feature 分支是跟人合作开发的，请互相做好协调。

## release 分支

当 develop 上的功能和 bug 修得差不多的时候，我们就要发布新版本了，这个时候从 develop 分支上开出一个 release 分支，来做发布前的准备，分支名约定为`release/20121221`，主要是测试有没有什么 bug，如果有 bug 就直接在这个分支上修复，确定没有问题后就会合并到 master 分支。相应操作如下：

```
$ git checkout -b release/20121221 develop
# 修复 bug、检查没问题后合并到 master 分支并删除
$ git checkout master
$ git merge --no-ff release/20121221
```

为了让 release 分支上 bug 修改作用到 develop 分支，我们还需要把这个 release 分支合并回 develop 分支：

```
$ git checkout develop
$ git merge --no-ff release/20121221
# 到此，这个 release 分支完成了它的使命，可以被删除了
$ git branch -d release/20121221
```

## hotfix 分支

如果我们发现线上的代码（也就是 master）有 bug，但是这个时候我们的 develop 上的有些功能还没完成，还不能发布，这个时候我们可以从 master 分支上开出一个 hotfix 分支（记住：直接在 master 上提交代码是不允许的！），分支名约定为`hotfix/xxx`，在这个分支上修改完 bug 后需要把这个分支同时合并到 master 和 develop 分支。相应操作如下：

```
$ git checkout -b hotfix/xxx master
# 修完 bug 后
$ git checkout master
$ git merge --no-ff hotfix/xxx
$ git checkout develop
$ git merge --no-ff hotfix/xxx
# hotfix 分支完成使命
$ git branch -d hotfix/xxx
```

例外：当 hotfix 分支完成，这个时候如果有 release 分支存在，那么这个 hotfix 就应该合并到 release，而不是 develop 分支。

## proj 分支

proj 分支为项目分支，所有的项目分支都从 master 上开出来，约定的分支名为`proj/xxx`。所有的项目定制内容都直接在项目分支上提交。为了保证项目的更新，每当项目有新版本发布时都需要把 master 分支合并到 proj 分支上。相应操作如下：

```
$ git checkout -b proj/xxx master
# 定制。。。
# 如果 master 分支有更新
$ git checkout proj/xxx master
$ git merge --no-ff master
```



## 四、git pull

`git pull`命令的作用是，取回远程主机某个分支的更新，再与本地的指定分支合并。它的完整格式稍稍有点复杂。

> ```
> $ git pull <远程主机名> <远程分支名>:<本地分支名>
>
> ```

比如，取回`origin`主机的`next`分支，与本地的`master`分支合并，需要写成下面这样。

> ```
> $ git pull origin next:master
>
> ```

如果远程分支是与当前分支合并，则冒号后面的部分可以省略。

> ```
> $ git pull origin next
>
> ```

上面命令表示，取回`origin/next`分支，再与当前分支合并。实质上，这等同于先做`git fetch`，再做`git merge`。

> ```
> $ git fetch origin
> $ git merge origin/next
>
> ```

在某些场合，Git会自动在本地分支与远程分支之间，建立一种追踪关系（tracking）。比如，在`git clone`的时候，所有本地分支默认与远程主机的同名分支，建立追踪关系，也就是说，本地的`master`分支自动"追踪"`origin/master`分支。

Git也允许手动建立追踪关系。

> ```
> git branch --set-upstream master origin/next
>
> ```

上面命令指定`master`分支追踪`origin/next`分支。

如果当前分支与远程分支存在追踪关系，`git pull`就可以省略远程分支名。

> ```
> $ git pull origin
>
> ```

上面命令表示，本地的当前分支自动与对应的`origin`主机"追踪分支"（remote-tracking branch）进行合并。

如果当前分支只有一个追踪分支，连远程主机名都可以省略。

> ```
> $ git pull
>
> ```

上面命令表示，当前分支自动与唯一一个追踪分支进行合并。

如果合并需要采用rebase模式，可以使用`--rebase`选项。

> ```
> $ git pull --rebase <远程主机名> <远程分支名>:<本地分支名>
>
> ```

如果远程主机删除了某个分支，默认情况下，`git pull` 不会在拉取远程分支的时候，删除对应的本地分支。这是为了防止，由于其他人操作了远程主机，导致`git pull`不知不觉删除了本地分支。

但是，你可以改变这个行为，加上参数 `-p` 就会在本地删除远程已经删除的分支。

> ```
> $ git pull -p
> # 等同于下面的命令
> $ git fetch --prune origin
> $ git fetch -p
> ```

## 五、git push

`git push`命令用于将本地分支的更新，推送到远程主机。它的格式与`git pull`命令相仿。

> ```
> $ git push <远程主机名> <本地分支名>:<远程分支名>
>
> ```

注意，分支推送顺序的写法是<来源地>:<目的地>，所以`git pull`是<远程分支>:<本地分支>，而`git push`是<本地分支>:<远程分支>。

如果省略远程分支名，则表示将本地分支推送与之存在"追踪关系"的远程分支（通常两者同名），如果该远程分支不存在，则会被新建。

> ```
> $ git push origin master
>
> ```

上面命令表示，将本地的`master`分支推送到`origin`主机的`master`分支。如果后者不存在，则会被新建。

如果省略本地分支名，则表示删除指定的远程分支，因为这等同于推送一个空的本地分支到远程分支。

> ```
> $ git push origin :master
> # 等同于
> $ git push origin --delete master
>
> ```

上面命令表示删除`origin`主机的`master`分支。

如果当前分支与远程分支之间存在追踪关系，则本地分支和远程分支都可以省略。

> ```
> $ git push origin
>
> ```

上面命令表示，将当前分支推送到`origin`主机的对应分支。

如果当前分支只有一个追踪分支，那么主机名都可以省略。

> ```
> $ git push
>
> ```

如果当前分支与多个主机存在追踪关系，则可以使用`-u`选项指定一个默认主机，这样后面就可以不加任何参数使用`git push`。

> ```
> $ git push -u origin master
>
> ```

上面命令将本地的`master`分支推送到`origin`主机，同时指定`origin`为默认主机，后面就可以不加任何参数使用`git push`了。

不带任何参数的`git push`，默认只推送当前分支，这叫做simple方式。此外，还有一种matching方式，会推送所有有对应的远程分支的本地分支。Git 2.0版本之前，默认采用matching方法，现在改为默认采用simple方式。如果要修改这个设置，可以采用`git config`命令。

> ```
> $ git config --global push.default matching
> # 或者
> $ git config --global push.default simple
>
> ```

还有一种情况，就是不管是否存在对应的远程分支，将本地的所有分支都推送到远程主机，这时需要使用`--all`选项。

> ```
> $ git push --all origin
>
> ```

上面命令表示，将所有本地分支都推送到`origin`主机。

如果远程主机的版本比本地版本更新，推送时Git会报错，要求先在本地做`git pull`合并差异，然后再推送到远程主机。这时，如果你一定要推送，可以使用`--force`选项。

> ```
> $ git push --force origin
>
> ```

上面命令使用`--force`选项，结果导致远程主机上更新的版本被覆盖。除非你很确定要这样做，否则应该尽量避免使用`--force`选项。

最后，`git push`不会推送标签（tag），除非使用`--tags`选项。

> ```
> $ git push origin --tags
> ```

[](https://gist.github.com/df007df)

[深入理解学习Git工作流（git-workflow-tutorial）](https://segmentfault.com/a/1190000002918123)

[iOS开发中的Git流程](http://www.jianshu.com/p/87e34894a9f9?utm_campaign=maleskine&utm_content=note&utm_medium=writer_share&utm_source=weibo)



#### Git操作备忘录

推送远程分支

```objective-c
git push origin local_branch:remote_branch
```



##### Git 小命令

## 使用 git stash

### 保存

使用`git stash`保存当前的操作，如果不这么做，你在切换到别的分支之前就一定要提交已经有的改动。但你当前的操作尚未完成，所以要暂时保存起来。

### 查看

直接使用`git stash list`就可以了。

```
MyPC:project limi$ git stash list
stash@{0}: WIP on master: 3d72f0b clear file
stash@{1}: WIP on start-test: fabaa87 fix bug
```

### 恢复

用`git stash pop stash@{num}`，`num` 是你要恢复的操作的序号，所以你最好在回复前用`git stash list`查看一下。

`git stash pop`命令是恢复stash队列中的`stash@{0}`，然后从记录就删除，就是常规的`pop`操作。

### 删除

stash存的不要过多，不然你也不知道哪个是哪个，最好随时清一清。
把所有的记录都清空掉用`git stash clear`。



#### 冲突解决
