##package.json——bin

#### bin字段

bin项用来指定各个内部命令对应的可执行文件的位置。

	"bin": {
	  "someTool": "./bin/someTool.js"
	}

上面代码指定，someTool 命令对应的可执行文件为 bin 子目录下的 someTool.js。Npm会寻找这个文件，在node_modules/.bin/目录下建立符号链接。在上面的例子中，someTool.js会建立符号链接npm_modules/.bin/someTool。由于node_modules/.bin/目录会在运行时加入系统的PATH变量，因此在运行npm时，就可以不带路径，直接通过命令来调用这些脚本。

因此，像下面这样的写法可以采用简写。


	scripts: {  
	  start: './node_modules/someTool/someTool.js build'
	}

	// 简写为
	
	scripts: {  
	  start: 'someTool build'
	}

所有node_modules/.bin/目录下的命令，都可以用npm run [命令]的格式运行。在命令行下，键入npm run，然后按tab键，就会显示所有可以使用的命令。

#### 举例

如下是webpack的初版的package.json

	{
		"name": "webpack",
		"version": "0.1.0",
		"author": "Tobias Koppers @sokra",
		"description": "Bundle CommonJS modules into single script or multiple scripts for web browser",
		"dependencies": {
			"esprima": "0.9.8",
			"optimist": "0.2.x",
			"uglify-js": "1.2.5"
		},
		"devDependencies": {
			"vows": "*"
		},
		"engines": {
			"node": ">=0.1.30"
		},
		"main": "lib/webpack.js",
		"bin": "./bin/webpack.js",
		"licence": "MIT"
	}

bin字段映射到了./bin文件夹下的webpack.js，所以安装了webpack的项目的node_modules的.bin文件夹下就会有一个叫wenpack的符号链接链接到正确的文件