# Base of Rebase
![rebase before and after](./git rebase 1.png)


相信对于rebase肯定不会陌生，就好像上图描述的过程一样，当你使用rebase命令的时候，即好像将你需要去rebase的分支拔下来然后重新插到另一个分支上。官方对于rebase的描述为：

```
“git-rebase: Forward-port local commits to the updated upstream head”— git doc
```

翻译一下，就是讲你在某个分支上的所有提交记录移花接木到另一个分支上。这边需要强调一个概念：reapply，使用rebase并不是简单地好像你用ctrl-x/ctrl-v进行剪切复制一样，rebase会依次地将你所要操作的分支的所有提交应用到目标分支上。也就是说，实际上在执行rebase的时候，有两个隐含的注意点：

1. 在重放之前的提交的时候，Git会创建新的提交，也就是说即使你重放的提交与之前的一模一样Git也会将之当做新的独立的提交进行处理。
2. Git rebase并不会删除老的提交，也就是说你在对某个分支执行了rebase操作之后，老的提交仍然会存放在.git文件夹的objects目录下。

基于以上表述，我们可以得出以下相对更准确的Git rebase的工作流程：
![git rebase workflow](./git rebase workflow.png)


从上图可以看出，在对特征分支进行rebase之后，其等效于创建了新的提交。并且老的提交也没有被销毁，只是简单地不能再被访问或者使用。在对于分支的章节我们曾经提及，一个分支只是一个执行提交的指针。因此如果既没有分支或者Tag指向某个提交，该提交将无法再被访问使用，但是该提交会一直存在于你的文件系统中，占用着你的磁盘存储。

## Golden Rule of Rebase

> “No one shall rebase a shared branch” — Everyone about rebase

估计你也肯定看过这个原则，不过可能表述不一样罢了。本章节就是用实例的角度来探讨下，为啥不能再一个共享的分支上进行Git rebase操作。所谓共享的分支，即是指那些存在于远端并且允许团队中的其他人进行Pull操作的分支。假设现在Bob和Anna在同一个项目组中工作，项目所属的仓库和分支大概是下图这样：
![git rebase rule anylasis](./git rebase rule demo1.png)


现在Bob为了图一时方便打破了原则，正巧这时Anna在特征分支上进行了新的提交，此时的结构图大概是这样的：
![git rebase rule anylasis-destroy](./git revase rule destroy demo.png)

当Bob打算推送自己的分支到远端的时候，它收到了如下的警告：
![git rebase warning for rebased a shared branch](./git rebase rule warning.png)


Git尝试着使用fast-forward来合并你的分支，远端的Git Server被Bob搞得一头雾水，不知道应该如何去合并。此时Bob为了推送他的本地的提交，只能选择强行合并，即告诉远端：不要再尝试着合并我推送给你的和你已经有点提交，一切按照我推送过去的来。那么Git会进行如下操作：
![git rebase rule result after ](./git rebase result of Bob rebase the shared branch.png)


然后呢，当Anna也进行推送的时候，她会得到如下的提醒：
![git rebase rule warning by Anna](./git rebase rule warning 2.png)


这个消息很正常，没啥特殊的，只是Git提醒Anna她本地的版本与远程分支并不一致，在Anna提交之前，分支中的Commit序列是如下这样的：

```
A--B--C--D'   origin/feature // GitHub

A--B--D--E    feature        // Anna
```

在进行Pull操作之后，Git会进行自动地合并操作，结果大概是这样的：
![git rebase rule result after warning for Anna](./git rebase rule result after warning 2.png)


这个第M个提交即代表着合并的提交，也就是Anna本地的分支与Github上的特征分支最终合并的点，现在Anna解决了所有的合并冲突并且可以Push她的代码，在Bob进行Pull之后，每个人的Git Commit结构为：
![git rebase rule result of Bob and Anna](./git rebase result of Bob and Anna after detroy golden rule by bob.p)


到这里，看到上面这个混乱的流线图，相信你对于Rebase和所谓的黄金准则也有了更形象深入的理解。这还只是仅有两个人，一个特征分支的项目因为误用rebase产生的后果。如果你团队中的每个人都对公共分支进行rebase操作，那还不得一团乱麻。另外，相信你也注意到，在远端的仓库中存有大量的重复的Commit信息，这会大大浪费我们的存储空间。如果你还觉得这么什么，那我们来假设下还有一哥们Emma，第三个开发人员，在他进行了本地Commit并且Push到远端之后，仓库变为了：
![git rebase result of destroy golden rule by multiple people](./git rebase rule by destroy of multiple people.png)


### Resources
[Git笔记(三)——[cherry-pick, merge, rebase]](http://pinkyjie.com/2014/08/10/git-notes-part-3/)