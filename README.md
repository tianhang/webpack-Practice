# Step1
## 初始化项目
```
npm init
```
## 安装webpack
 ### 全局安装
```
npm install -g webpack
```
### 安装到你的项目目录
```
npm install --save-dev webpack
```
## 建立app文件夹和public文件夹创建三个文件，index.html 文件放在public文件夹中，两个js文件（Greeter.js和main.js）放在app文件夹中
### index.html
```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>Webpack Sample Project</title>
  </head>
  <body>
    <div id='root'>
    </div>
    <script src="bundle.js"></script>
  </body>
</html>
```
### main.js 
```
var greeter = require('./Greeter.js');
document.getElementById('root').appendChild(greeter());
```
### Greeter.js
```
module.exports = function() {
  var greet = document.createElement('div');
  greet.textContent = "Hi there and greetings!";
  return greet;
};
```
## 使用webpack
```
webpack {entry file/入口文件} {destination for bundled file/存放bundle.js的地方}
```
### 非全局安装
```
node_modules/.bin/webpack app/main.js public/bundle.js
```
### 全局安装
 **进入项目目录**
```
webpack app/main.js  public/bundle.js
```
# Step2
## 使用配置文件
 ### 在当前练习文件夹的根目录下新建一个名为webpack.config.js的文件
 ```
 module.exports = {
  entry:  __dirname + "/app/main.js",//已多次提及的唯一入口文件
  output: {
    path: __dirname + "/public",//打包后的文件存放的地方
    filename: "bundle.js"//打包后输出文件的文件名
  }
}
```
**注：“__dirname”是node.js中的一个全局变量，它指向当前执行脚本所在的目录。**
### 在根目录（有webpack.config.js的）下运行
```
webpack
```
## 修改package.json对npm进行设置引导webpack
```
"scripts": {
    "start":"webpack" 
  }
```
**注：package.json中的脚本部分已经默认在命令前添加了node_modules/.bin路径，所以无论是全局还是局部安装的Webpack，你都不需要写前面那指明详细的路径了。**
### npm的start是一个特殊的脚本名称，它的特殊性表现在，在命令行中使用npm start就可以执行相关命令，如果对应的此脚本名称不是start，想要在命令行中运行时，需要这样用npm run {script name}如npm run build

```
  "scripts": {
    "webpack":"webpack" 
  },
```
### 这样就需要在终端运行 
```
npm run webpack
```
# Step3
## 生成Source Maps

devtool选项  |	配置结果
---------------------------------|-----
source-map |	在一个单独的文件中产生一个完整且功能完全的文件。这个文件具有最好的source map，但是它会减慢打包文件的构建速度；
cheap-module-source-map	|在一个单独的文件中生成一个不带列映射的map，不带列映射提高项目构建速度，但是也使得浏览器开发者工具只能对应到具体的行，不能对应到具体的列（符号），会对调试造成不便；
eval-source-map	|使用eval打包源文件模块，在同一个文件中生成干净的完整的source map。这个选项可以在不影响构建速度的前提下生成完整的sourcemap，但是对打包后输出的JS文件的执行具有性能和安全的隐患。不过在开发阶段这是一个非常好的选项，但是在生产阶段一定不要用这个选项；
cheap-module-eval-source-map|	这是在打包文件时最快的生成source map的方法，生成的Source Map 会和打包后的JavaScript文件同行显示，没有列映射，和eval-source-map选项具有相似的缺点；

*在学习阶段以及在小到中性的项目上，eval-source-map是一个很好的选项，不过记得只在开发阶段使用它，继续上面的例子，进行如下配置*
```
devtool: 'eval-source-map',//配置生成Source Maps，选择合适的选项
```
## 使用webpack构建本地服务器
### 先安装
```
npm install --save-dev webpack-dev-server
```
### 然后就可以直接在终端输入
```
webpack-dev-server --open
```
启动了，默认8080端口
或者我们可以用配置文件来启动

devserver配置选项 |	功能描述
----------|---
contentBase	|默认webpack-dev-server会为根文件夹提供本地服务器，如果想为另外一个目录下的文件提供本地服务器，应该在这里设置其所在目录（本例设置到“public"目录）
port	|设置默认监听端口，如果省略，默认为”8080“
compress| 是否使用Gzip
inline	|设置为true，当源文件改变时会自动刷新页面
historyApiFallback	|在开发单页应用时非常有用，它依赖于HTML5 history API，如果设置为true，所有的跳转将指向index.html
#### 更多配置文件[开发中 Server(DevServer)](https://doc.webpack-china.org/configuration/dev-server/#devserver)
### 在配置文件中加入配置
```
	devServer: {
		contentBase: __dirname+"/public", //本地服务器所加载的页面所在的目录
		compress: true,  // 是否使用Gzip
		port: 9000, // 指定端口 不指定端口默认为1080
		historyApiFallback: true, //不跳转
		inline: true //实时刷新
	}
```
### 再去加个快捷启动命令
```
  "scripts": {
    "server": "webpack-dev-server"
  },
```
## Loaders （个人感觉webpack最屌的功能）
  *使用不同的loader可以处理各种不同的文件*
### 先去webpack.config.js下的modules关键字下进行配置
test|一个匹配loaders所处理的文件的拓展名的正则表达式（必须）
----|----
loader|loader的名称（必须）
include/exclude|手动添加必须处理的文件（文件夹）或屏蔽不需要处理的文件（文件夹）（可选）；
query|为loaders提供额外的设置选项（可选）
#### 以json为例
#### 先安装
```
npm install --save-dev json-loader
```
#### 再去改一下配置
```

	module: {//在配置文件里添加JSON loader
		loaders: [
			{
				test: /\.json$/,
				loader: "json"
			}
		]
	},
```
#### 写个JSON文件  我就乱起名了。。jsontext.json
```
{
  "testText": "啦啦啦，我是JSON"
}
```
#### Greeter.js去引用一下
```
var testText = require('./jsontext.json'); //引入json

module.exports = function () {
  var greet = document.createElement('div');
  greet.textContent = testText.testText; // 使用
  return greet;
};
```
###  最常用的Loaders  Babel
 不用介绍了吧  编译JavaScript的平台
#### react的JSX为例
先安装 嗯 一次性安装多个
```
npm install --save-dev babel-core babel-loader babel-preset-es2015 babel-preset-react
```
嗯 还得装react
```
npm install --save react react-dom
```
#### 再去把Greeter改成react组件
```
import React, {Component} from 'react'
import testText from './jsontext.json';

class Greeter extends Component{
  render() {
    return (
      <div>
        {testText.testText}
      </div>
    );
  }
}

export default Greeter
```
#### 主入口文件 main
```
import React from 'react';
import {render} from 'react-dom';
import Greeter from './Greeter';

render(<Greeter />, document.getElementById('root'));
```
 ####  建议用.babelrc对Babel进行配置 不过演示么  无所谓了
 虽然能在webpack.config.js配置，但是  那么多 真的好么。
```
{
  "presets": ["react", "es2015"]
}
```
#### 最后改一下设置
```
	module: {//在配置文件里添加JSON loader
		loaders: [
			{
				test: /\.json$/,
				loader: "json-loader" 
			},
			{
				test: /\.js$/,
				exclude: /node_modules/,
				loader: 'babel-loader', //在webpack的module部分的loaders里进行配置即可
				query: {
					presets: ['es2015', 'react']
				}
			}
		]
	},

```
### css
*webpack提供两个工具处理样式表，css-loader 和 style-loader，二者处理的任务不同，css-loader使你能够使用类似@import 和 url(...)的方法实现 require()的功能,style-loader将所有的计算后的样式加入页面中，二者组合在一起使你能够把样式表嵌入webpack打包后的JS文件中。*
#### 先安装
```
npm install --save-dev style-loader css-loader
```
#### 再去改设置
```
{
				test: /\.css$/,
				loader: [ 'style-loader', 'css-loader' ]//添加对样式表的处理
}
```

####  写个css测试下 就叫main.css吧
```
html{
	background-color: red;
}
body{
	font-size: 100px;
}
```
再去 主入口引入。。main.js 文件  前面说过
```
import './main.css';//使用require导入css文件
```
### CSS module
**通常情况下，css会和js打包到同一个文件中，并不会打包为一个单独的css文件，不过通过合适的配置webpack也可以把css打包为单独的文件的 好处就是CSS模块，所有的类名，动画名默认都只作用于当前模块（不用费尽心机去起名了。。）😄**

#### 改配置文件
```
{
				test: /\.css$/,
				use: [
					'style-loader',
					{
						loader: 'css-loader',
						options: { modules: true }
					}
				]
}
```
#### 写个新模块的css  Greeter.css
```
.mod{
		background-color: blue;
		font-size: 20px;
}
```
####  在模块中引入css 并使用
```

import React, {Component} from 'react'
import testText from './jsontext.json';
import styles from './Greeter.css' // 引入css

class Greeter extends Component{
  render() {
    return (
      <div className={styles.mod}>   //使用
        {testText.testText}
      </div>
    );
  }
}

export default Greeter

```


#### 可以去看效果了
类名变得只有他妈（webpack）才认识了
有兴趣可以去瞅瞅文档[我是文档](https://github.com/css-modules/css-modules)


