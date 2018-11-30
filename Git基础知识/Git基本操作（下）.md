## 修正commit信息

#### 涉及到的命令： git commit --amend####

git commit --amend是对上一条commit命令进行修正。当我们执行这条命令时时，Git会把当前暂存区的内容和这次commit的新内容合并创建一个新的commit，把我们当前HEAD指向的最新的commit替换掉。例如我们当前最新一条commit记录中，我们输入错了提交信息，想要修改，又或者我们提交错了一点东西，又不想生成一个新的commit记录，我们都可以使用这个命令。

这里假设我们需要修复一个上次提交错的文件，同时想修改上一个commit的信息


在我们修复了错误文件test.js后,执行：

	git add src/test.js

然后我们commit这条修改：

	git commit --amend

接着我们又一次进入commit的提交信息编辑页面

![](https://i.imgur.com/UnJxTc3.jpg)

记得上篇文章讲的commit编辑输入信息那里的内容吗？来顺一下

#### vim的操作 ####

1.输入i进入插入模式，对上一条commit信息的内容进行修改

2.按下ESC键，退出编辑模式，切换到命令模式。 

3.在命令模式下键入"ZZ"或者":wq"保存修改并且退出 vim。 

4.只想保存文件：则键入":w"，回车后底行会提示写入操作结果，并保持停留在命令模式。

5.放弃所有文件修改：按下ESC键进入命令模式，键入":q!"回车后放弃修改并退出vim

6.放弃所有文件修改，但不退出 vi：按下ESC键进入命令模式，键入":e!"

好，我们继续上面amend的部分，执行命令：

![](https://i.imgur.com/SwCAqn7.png)

看下我们的commit记录

![](https://i.imgur.com/Jv0eXGt.png)

amend用于修改上一条commit信息时，实际上并不是对上一个commit修改，而是生成新的对它进行替换，例如我们对已经push到远端的commit记录在本地仓库进行--amend操作之后，直接push到远端仓库是不会成功的，因为本地丢失了远端仓库那个我们替换的commit

## 丢弃之前的提交reset --hard

最新一次的commit的内容有问题，想要丢弃这次提交，先log看下提交记录：

![](./images/wrongcommit-log.png)

如果想要恢复到最新一次前面的那次commit记录，也就是the third，执行：

	git reset --hard HEAD^

head指向最新的一个commit，也就是途中的wrong commit,^标识从当前位置向前数，有几个就是向前数几个，所以上面的命令的意思就是恢复到head对应的的commit前面的commit，也就是the third。

执行结果：

![](./images/resethardhead1.png)

这时我们git log看下commit的记录：

![](./images/1afterresethard.png)

同理如果要恢复到head前面的第二个commit，也就是the second,在head后两个^:

	git reset --hard HEAD^^
	
被撤销的那条提交并没有消失，只是log不再展现出来，因为已经被丢弃。如果你在撤销它之前记下了它的 SHA-1 码，那么还可以通过这个编码找到它，执行如下：

![](./images/1.png)

再log看下commit记录，wrongcommit已经重新出现在记录里，并且head指向它：

![](./images/2.png)

	git reset --hard 目标commit


参考文章：
https://www.jianshu.com/p/4f8b56d0fd5b
http://gitbook.liuhui998.com/4_2.html
https://blog.csdn.net/andyzhaojianhui/article/details/78072143