# Git submodule

Git对于Submodule有特殊的处理方式，在一个主项目中引入了Submodule其实Git做了3件事情：

- 记录引用的仓库
- 记录主项目中Submodules的目录位置
- 记录引用Submodule的commit id

在主项目中push之后其实就是更新了引用的commit id，然后新的项目在clone主项目的时候获取到了submodule的commit id，然后当执行git submodule update的时候git就根据gitlink获取submodule的commit id，最后获取submodule的文件，所以clone之后不在任何分支上；但是master分支的commit id和HEAD保持一致。

![](https://i.imgur.com/tTq7Zxl.png)
![](https://i.imgur.com/4I5RXxX.png)
![](https://i.imgur.com/POqjgAa.png)

切换回master发现最新的提交并不是这个号码，回到远程仓库上看到，这个57b63其实是这个子模块某一个分支的最新提交，所以可以看出，子项目更新后在主项目push一下，主项目就会标记当前子项目指向哪一个版本，而不是非得要合并进master的才可以

### 添加子模块

$ git submodule add https://github.com/chaconinc/DbConnector

git submodule init 用来初始化本地配置文件，这里对应的是主项目.git中的modules，里面存的是每个子模块的git仓库

git submodule update 则从该项目中抓取所有数据并检出父项目中列出的合适的提交

$ git clone --recursive https://github.com/chaconinc/MainProject 会自动初始化并更新仓库中的每一个子模块。

### 删除子模块

先删除.gitignore中的配置
再删除.git中modules里init的仓库

再执行git clean

https://www.cnblogs.com/familymrfan/p/3866811.html