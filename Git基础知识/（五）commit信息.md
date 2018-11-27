##commit信息历史
#### 涉及到的命令：git log####

![](https://i.imgur.com/ORGPyna.png)

#### commit的id ####

每一个commit对应一个唯一id，是40为的数字和字符组成的字符串，是属于每一个commit的一个id，一个SHA-1校验和

![](https://i.imgur.com/M86R3KZ.png)

第一行，tree和对应的hash值，根据这个hash值我们可以得到本次提交的整个目录树和对应的hash值

	git cat-file -p hash值 //-p 以更优雅的方式展现对象内容

![](https://i.imgur.com/7u4xnU4.png)

里面的每一个文件都可以根据hash继续展开 直到叶子结点。

回到head信息组成这里，第二行parent，是当前查看的commit的上一条commit的id；第三行作者信息以及提交时的时间戳；第四行提交者的信息以及提交时的时间戳。

根据head对应的提交信息生成其sha-1值的命令如下：

	(printf "commit %s\0" $(git cat-file commit HEAD | wc -c); git cat-file commit HEAD) | shasum

![](https://i.imgur.com/poghrAD.png)

#### HEAD ####

commit记录后面括号对应着指向这个commit的引用，注意到commit提交信息第一条后面的括号里的HEAD，它永远指向当前的commit，就是当前工作目录对应的提交的commit。

HEAD同时也指向一个分支，图中的HEAD——>master，表示当前工作目录对应的是本地master分支


通常每次有一条新的commit记录时，工作目录会与这条最新的commit对应，HEAD指针也会指向它（在使用checkout reset等操作切换当前工作目录对应的commit时，HEAD也会跟过去，后面会说）

![](https://i.imgur.com/DyTrf89.png)

如图，我们commit了最新的提交信息还没push到远端时，本地的HEAD指向我们最新的提交，而远端仓库的还停留在之前的那条commit记录，origin/master指向它。

在我们push操作的时候，HEAD并不会推送到远端，远端的HEAD永远指向默认分支master

#### master分支 ####

一个没有提交记录的新项目，在创建第一条commit时，会默认提交到master分支，同时HEAD也指向它。

在我们clone远端项目时，默认也会在本地checkout出一个master分支，并将本地工作目录的文件内容保持与clone下来的项目的master分支的最新commit一致，HEAD也会指向它。

绝大多数团队会选择master作为核心的分支，其余分支都是围绕master来开发，但本质上各个分支都是一样的，都是一串commit信息的记录。

commit信息这里就粗略的说到这吧。接下来接着说说branch


##branch分支创建和切换
#### 涉及到的命令：git branch/git checkout/git checkout -b/git branch -d####

	git branch 新的分支名//创建分支

这样创建完新的分支，并不会自动切换到新的分支上

	git checkout 分支名 //切换分支

以上两部可以合并
	
	git checkout -b 新的分支名 //创建并切换到新的分支

![](https://i.imgur.com/626TlIc.png)

切换到新的分之后，HEAD也跟着指了过去，当当前新分支有新的commit时，HEAD会指向这个分支最新的commit，master会停留在它之前对应的commit记录那里，因为那是属于master分支最新的commit记录。

	git branch -d 分支名 //删除分支

需要注意，HEAD指向的分支无法删除，也就是我们所在的分支，需要先checkout切换到别的分支，再去删除之前的分支。

我们删除了一个分支后，并不会删除这个分支上的提交记录，其实branch这个分支的概念，更确切的说是一个引用，是一个指向，指向一串的提交记录，我们删除了分之后只是删除了对这个分支包含的提交记录的一个引用，虽然说我们没有删除它们，但是Git的回收机制会定期清理那些不在任何分支上的commit记录。