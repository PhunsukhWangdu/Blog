##GIT GUI CMD BASH

GIT GUI可视化图形界面

GIT CMD是CMD

Git中的Bash是基于CMD的，在CMD的基础上增添一些新的命令与功能。所以建议在使用的时候，用Bash更加方便。

git在安装的时候也安装了一个轻量级的bash环境，然后启动这个”git bash”，出来的命令窗口就带有这个bash环境的环境变量。bash是基于shell的命令库，本身是unix下的命令脚本。你甚至可以在”git bash”中使用一个轻量的vi编辑器

本质上来说：**git bash是一个封装过的cmd命令行**，并在其中加入了指向bash环境的环境变量。因此cmd命令行本身的环境变量它也有，自然就能使用dos命令了。但是在封装的过程中，windows可能对调用自己cmd命令行控件的第三方应用设了限制，所以说是大部分可以用，当然还有Git自己的命令

某些情况下，权限有区别，例如设置了ssh key，gitbash下权限正常，但是CMD下没有读写权限