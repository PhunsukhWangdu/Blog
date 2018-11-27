##clone远程仓库、创建本地仓库
![](https://i.imgur.com/Zuiwd3o.png)

	git clone https://github.com/****.git 

clone项目到本地之后，我们可以看到目录下有一个叫.git的隐藏文件
![](https://i.imgur.com/L3aCJHm.png)

这个.git文件夹就是我们的本地仓库（Local Repository）.git文件夹所在的根目录就是我们的工作目录（Working Directory）

clone下来的项目我们就可以正常开发了，当然我们也可以在本地直接创建一个本地仓库，之后再与远程创建的仓库进行关联，先执行

	git init
命令执行的目录下就有了一个.git文件夹，我们在创建了一个本地仓库，接着与远程仓库进行关联：

	git remote add origin https://github.com/****.git 