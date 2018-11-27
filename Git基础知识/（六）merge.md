##merge 合并冲突
#### 涉及到的命令：git merge/git merge --abort####

merge意思为合并，把目标分支合并到当前分支，一般在我们分支协作开发，某一分支的开发完成可以合并如主流程的时候，这样去操作。

实际的行为是，从当前分支和要合并的目标分支的分叉点开始，将目标分支路径上的所有commit内容应用到当的commit，并生成一个新的commit

我们在本地master分支上执行

	git merge feature1

将feature1分支上的两个commit记录4和5与本地分支上最新的commit3合并并生成了一个新的commit，如图中的6

![](https://i.imgur.com/KnXDi3M.png)

#### 冲突解决 ####

合并操作时，通常情况下git会帮我们自动合并两个分支上的改动，包括不同文件或者同一文件的不同位置的修改，但既然是分支开发，免不了同时修改了相同文件的同一个内容的情况，这时候就需要冲突合并。

例如我从上图2的位置新建了分支feature1，commit4和5修改了src/test.js文件，同时在master分支的commit3中也修改了src/test.js文件，这时我在master上merge远端的分支feature1

![](https://i.imgur.com/rga9eCt.png)

src/test.js出现了 "merge conflict"合并冲突，自动合并失败，要求 "fix conflicts and then commit the result"把冲突解决掉后提交。

**手动解决：**

我们可以选择手动解决一下，打开文件，把不需要的分支的内容删掉（包括===》这些符号！），然后重新提交这个冲突文件即可

![](https://i.imgur.com/jTolQjO.png)

**放弃合并，撤销merge操作：**

	git merge --abort //取消merge操作

执行之后回去看冲突文件，就变回了merge之前的状态。

如果当前分支的所有提交另一个分支都包含，就是在上图2的位置合并feature1的内容，这时其实commit记录也就还是一条线，1245，所以把本地的HEAD和分支（例子中是master）也指向feature1的最新commit记录5就可以了。

在遇到冲突时，git并不会自动为我们生成一个commit记录，而是我们修改完后自己去提交这个记录，例如上面冲突修改之后直接commit再push，在远端仓库我们看到的记录是：

![](https://i.imgur.com/eEkyV1Q.png)

而如果没有冲突，我们一次执行merge，status看下工作区，再push到远端仓库：

![](https://i.imgur.com/MoTmMwu.png)

我们看下远端仓库的提交记录：

![](https://i.imgur.com/cbLHCyV.png)

当然，我们也可以通过命令修改默认生成的提交的信息，也可以不默认生成新的commit，这里只是简单介绍常用命令的基本用法，merge详细的使用可以看下：

[https://blog.csdn.net/andyzhaojianhui/article/details/78072143](https://blog.csdn.net/andyzhaojianhui/article/details/78072143 "git merge-快进合并和非快进合并")