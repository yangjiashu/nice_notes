# 本地

## 版本回退

查看版本输入命令：`git log`或者简短版`git log --pretty=oneline`

查看所有命令的历史：`git reflog`

版本回退：HEAD指针指向当前版本，命令行最上面的是当前的版本HEAD,上一个版本表示为HEAD^,以此类推。要回退到某个特定的版本命令是：

`git reset --hard [版本id或者HEAD^^^]`

## 工作区和暂存区

![git组成](./images/git.PNG)

工作区里有一个.git文件夹，这个文件夹对于工作区来说是隐藏的，成为git的版本库。版本库里存了很多东西，其中最重要的就是stage或者叫index的暂存区，还有git创建的第一个分支master，以及指向master的指针HEAD。

git add就是把文件**修改**添加到暂存区；

`git commit`就是把暂存区的所有内容提交到当前分支;

`git diff`不加参数是比较的是stage暂存区和工作空间的区别

`git diff master`查看master分支和working area的文件区别

`git diff refs/remotes/origin/master`用远程分支和当前工作区比较

`git diff HEAD -- xxxx.xxx`可以查看某个文件和某个版本的差异,同样，HEAD可以替换为commit id;

`git diff` 命令的结果：

```
---代表源文件，+++代表目标文件，通常，working area中的文件都被当作目标文件对待。
-开头的行，是只出现在源文件中的行，+开头的行，是只出现在目标文件中的行。
空格开头的行，是源文件和目标文件中都出现的行。
差异按照差异小结进行组织，每个差异小节的第一行都是定位语句，由@@开头，@@结尾。
@中包括的语句意思是：（@@ -4,6 +4,7 @@)为例，为从源文件第4行开始的6行和目标文件的第4行开始的7行构成一个差异小节。
```

## 撤销修改

`git checkout -- file`可以撤销工作区的更改。

`git checkout -- readme.txt`的意思是，把文件在工作区的修改全部撤销，如果readme.txt没有放到暂存区stage，那么撤销修改就撤回到和版本库一样。

如果readme.txt已经添加到暂存区，又作了修改，那么修改就回到暂存区的状态。相当于checkout会先尝试回到stage状态，如果stage没有存储修改，那么回到branch中的状态。

`git reset HEAD <file>`可以把暂存区stage的修改撤销掉（unstage），重新放回工作区。注意它跟版本回退的区别，版本回退是`git reset --hard HEAD^^`是直接将工作区变成某个历史版本的状态

`git reset`既可以回退版本，也可以把暂存区的修改回退到工作区。加`--hard`表示工作区也回退，不加`--hard`表示只清空stage

## 删除文件

在git中，删除也是一个修改操作。

# 远程仓库

第一步：创建ssh key，在用户home目录下，查看.ssh目录，如果有，那么查看有无`id_rsa`和`id_rsa.pub`文件，如果没有，则创建：`ssh-keygen -t rsa -C "1098325805@qq.com"`

第二步：在github账号管理里添加ssh公钥

# 分支管理

## 创建和合并分支

HEAD严格来说不是指向提交，而是指向master，master才是指向提交的。

创建了一个新的分支dev，实际上就是创建了一个新的指针。

命令：

首先，创建一个dev分支，然后切换到dev分支
`git checkout -b dev` or `git switch -c dev`

用`git branch`查看当前分支。

用`git checkout [分支名]`来切换分支。or `git switch [分支名]`

用`git merge [分支名]`来合并分支，在master分支下执行，分支名为dev，这样master就跟进到了dev的版本去了。

删除分支：注意，这样做只是删除了指针，并不会对版本产生影响。

`git branch -d dev`

**一个最佳实践是**，写代码时先用`git checkout -b xxx`创建一个分支，在完成工作之后再用`git checkout master`命令切换到主分支，最后用命令`git merge dev`合并分支，最后用`git branch -d dev`删除临时工作的分支。

另外，切换分支的命令推荐用新的switch

创建：`git switch -c dev`

切换：`git switch master`

## 解决冲突

在两个分支不是一前一后时，如果合并分支会产生冲突，必须手动解决冲突之后再提交。git status也可以告诉我们冲突的文件。再合并分支可以看到合并的情况，合并时情况可以用命令：

相当于已经合并成一个文件，但是需要先自行修改里面的冲突之后在commit，亦或者放弃本次merge，可以使用命令`git merge -abort`

出现冲突之后文件里会出现提示。注意，出现冲突，git只会有提醒，然后把修改放在工作区，至于之后要怎么处理，由用户自行制定。

`git log --graph --pretty=oneline --abbrev-commit`

合并分支时 加上--no-ff参数可以采用普通模式合并，合并后的历史分支可以看出来。

## 远程仓库

`git remote`和`git remote -v`可以查看远程仓库信息。

抓取分支： `git pull`

## Fast Forward模式

相当于直接移动master分支到新的分支

禁用Fast Forward模式时，merge的时候相当于提交了一次新的master版本，会留下提交的痕迹
同时在merge时也推荐添加-m的提交参数。

`git merge --no-ff -m "merge with no-ff" dev`

## 远程协作

推送分支时，就是把该分支上的所有本地提交推送到远程仓库中去。推送时要**指定本地分支**，这样，Git就会将该分支推送到远程仓库的**对应分支**上去。

`git push origin [要推送的分支]`

并不一定要把本地分支都推送到远程去，master和dev分支要随时同步，而bug和feature不需要。

抓取分支时，如果要在dev分支上开发，就需要创建远程origin的dev分支到本地，命令是：

`git checkout -b dev origin/dev` or `git switch -c dev origin/dev`

这时候可以在本地开发，然后不断的推送到dev上面去。但是，如果和其他的开发者出现冲突时，应该先拉取最新的dev：

`git pull`，但是这时候pull会失败，是因为还没有和远程的dev建立链接，要么建立链接，要么输入命令`git pull origin dev`。

建立链接的命令是：`git branch --set-upstream-to=origin/dev dev`，这时候在dev分支下输入`git pull`命令即可快速拉取分支。这时候手动处理冲突便可以推送了。

## 远程协作总结

多人协作的工作模式通常是这样：

1. 首先，可以试图用git push origin <branch-name>推送自己的修改；
2. 如果推送失败，则因为远程分支比你的本地更新，需要先用git pull试图合并；
3. 如果合并有冲突，则解决冲突，并在本地提交；
4. 没有冲突或者解决掉冲突后，再用git push origin <branch-name>推送就能成功！

如果git pull提示no tracking information，则说明本地分支和远程分支的链接关系没有创建，用命令git branch --set-upstream-to <branch-name> origin/<branch-name>。