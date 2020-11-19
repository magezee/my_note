## Webpack基础

### 简介

webpack 是一个现代 JavaScript 应用程序的静态模块打包器，打包是为了大幅度降低项目文件的大小，方便请求传输，每当代码更新时，需要再次打包

**webpack打包过程：module→chunk→bundle**

- **module**

项目里写的代码文件都可以称为module

- **chunk**

module 源文件传到 webpack 进行打包时，webpack 会根据文件引用关系生成 chunk文件

`````
假如有4个文件
index.js、index.css、tool.js和other.js
其中index.js中引入了index.css和tool.js
othder.js没有被引用
```

```jsx
// webpack.config.js
{
    entry: {
        index: "../src/index.js",
        other: '../src/other.js',
    },
    output: {
        filename: "[name].bundle.js", // 输出 index.js 和 other.js
    }
}
```

由于`index.css`和`tool.js`被`index.js`引入，被一起打包到了最终生成合并文件`index.bundle.js`中，因此这三个属于同一个chunk（chunk0）

而`other.js`是独立打包的，因此生成的`other.bundle.js`是另一个chunk（chunk1）

- **bundle**

webpack 处理好 chunk 文件后，最后会输出 bundle 文件，这个 bundle 文件包含了经过加载和编译的最终源文件，所以它可以直接在浏览器中运行

一般一个chunk对应生成一个bundle，但是如果使用了分割代码时或提取文件时，会将从一个bundle中抽离文件

```
如：用MiniCssExtractPlugin分离css文件
会生成index.bundle.js 和 index.bundle.css文件
```





----

### 基础使用

可以在项目package.json文件中写入项目启动命令并配置config

通过`webpack`命令直接启动打包，或者在package.json里进行配置并用npm命令来启动

```json
// 如：一个项目的package.json文件内容
{
  "name": "webpackDemo",	// 项目目录名，若要更改项目根目录名字，需要在这里也做调整，否则会出错
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {				
      // 启动项目脚本命令 如 npm(yarn) build  后面跟的是build命令的config文件路径，若不指定路径 配置文件默认会在项目根路径下查找
      "build": "webpack --config script/webpack.config.js",
      "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "devDependencies": {    // 项目依赖的npm模块
      "webpack": "^4.41.6",
      "webpack-cli": "^3.3.11",
      "webpack-dev-server": "^3.10.3"
  }
}   
```

一般在命令配置文件中需要声明以下几个属性并配置

（webpack原本就存在默认配置，这里配置的是将默认配置改成自己设置的，若部分配置不声明 则继续使用默认配置）

```js
// 如：package.json中声明的build命令的webpack.config.js文件
const path = require('path');

module.exports = {
    
    mode: 'development',
    
    entry: './src/index.js',
    
    output: {
		path: path.resolve(__dirname,"../dist"),    
		filename: 'js/main.[Hash:8].js'   
	},
    
    module: {
        rules: [
            {
                test: /\.js$/,
                exclude: /(node_modules|bower_components)/,     
                use: {
                    loader: 'babel-loader',
                    options: {
                        presets: ['@babel/preset-env']
                    }
                }
            }
        ]
    },
    
    plugins: [
        new HtmlWebpackPlugin({
            template: 'public/index.html',    
            title: 'webpack'         
        }),
    ]
         
}
```

- **mode**

  声明处理模式，自带两种模式：开发、生产（生产环境打包要求会更高，且会压缩代码，打包结果磁盘容量占用小）

```
development		// 开发模式
	打包的代码有换行符便于查看
production		// 生产模式
	全挤在一行里,等于将文件压缩大小了
```

```js
// development模式下打包结果
function a(b){
	console.log(b);
}

// production模式下打包结果(这里只是为了理解，真实的打包结果不是写什么内容代码打包什么内容)
function a(b){console.log(b)}

// 可以直接在package.json里配置处理模式（为了和后面的module）区分开
"build": "webpack --mode development --config script/webpack.config.js"
```

- **entry**

  指定默认入口文件，webpack 执行构建的第一步将从入口开始搜寻及递归解析出所有入口依赖的模块，存在多个入口文件时，会输出多个出口文件，否则会将所有js代码合并到一个js文件中

  entry配置是必填的，若不填则将导致 webpack 报错退出

```json
// 可以存在多个入口
entry: {
	home : './src/home.js',
	about: './src/about.js'
}

// 使用多入口时，出口配置不能写死，如filename: 'test.js'需要更改为filename: '[name].js'否则运行会报错
```

- **output**

  指定打包输出文件

  默认项目根路径下dist目录，文件名为入口属性名.js（entry: {Home : './src/home.js'} 则输出为Home.js）

```json
output: {
	path: path.resolve(__dirname,"../dist"),	// 指定路径
    filename: 'js/main.[Hash:8].js'				// 指定生成文件具体要求
    // 前面可加入路径，可更好管理，最终将入口指定的文件打包，在项目路径/dist/js下生成main.421384ac.js输出文件（哈希数随机生成）
}
```

```
为了避免项目更新版本时不更新浏览器缓存 可以添加哈希值 
	filename: '[name].[hash：8].js' 8表示生成哈希数的位数 
	这样生成的文件就会带上哈希数 如home.421384ac.js 和 about.421384ac.js


hash：使用hash，每次修改任何一个文件，所有文件名的hash至都将改变。所以一旦修改了任何一个文件，整个项目的文件缓存都将失效
但是使用hash时 多个入口的hash是同一个值 因此修改其中任意一个文件整个hash都会变动掉 因此使用chunkHash  
    
filename: '[name].[chunkHash：8].js'
这样不同的入口会生成不同的哈希数 即使修改了home.js文件 也只有home.js的哈希值会变动 而about.js的不会 于是在修改项目时 只有home.js的缓存被清掉了
```

- **module**

  loader的包在config里的module属性里的rules里配置规则，loader处理的是一种特定文件格式（如.css和.jpg），webpack会根据不同的文件类型使用不同的loader来处理

- **Loader**

  让webpack能够去处理那些非JS文件（webpack自身只理解JS和json），一个文件只能同时被一个loader处理，如果要用到多个loader，必须写正确loader处理顺序

- **plugins**

  plugins一般都需要引入到config里才能使用

  plugin的包在config里的plugins属性里通过new一个实例对象来使用，plugin 处理的是大量的资源，插件的范围包括从打包优化和压缩，一直到重新定义环境变量等



----

### 配置webpack

**webpack、webpack-cli、webpack-dev-server**

将js脚本打包，使用webpack打包时必备包，需要在package.json里配置使用

脚本配置webpack，用于生产环境 可以查看本地实际创建的文件

脚本配置webpack-dev-server，用于开发环境，不能查看到本地打包后的文件，自动编译，自动打开浏览器，自动刷新浏览器（当脚本发生变化时）

**在运行打包好的项目时，项目实际的根目录是dist**

```json
"scripts": {
"build": "webpack --config script/webpack.config.prod.js",
"dev": "webpack-dev-server --config script/webpack.config.dev.js",
"test": "echo \"Error: no test specified\" && exit 1"
},
```

 webpack和webpack-dev-server的config配置几乎一样（因为要满足功能完全相同），webpack-dev-server可以在config文件中配置启动服务选项`devServer`属性，启动服务器时，代码更改后不需要devServer不需要重新打包，会自动打包（devServer只会在内存中编译打包，不会有任何输出）

```javascript
// webpack.config.dev.js
devServer: {
	port: 3000,   // 配置本地服务器的端口号 若不配置则默认8080
	open: true    // 运行时自动打开浏览器 默认false
}
```

生产环境不需要devServer

------

### 配置页面处理

#### html打包处理

**html-webpack-plugin**

指定打包规则（html）

功能：默认会创建一个空的HTML，自动引入打包输出的所有资源（如js/css）

```
由于需要在dist文件里创建index.html文件引入打包好的js文件 需要使用
<script type="text/javascript" src="main.2ceb2be2.js"></script>
而哈希值会变动,若写死了则会非常不方便
因此使用 html-webpack-plugin，在每次打包时 自动生成一个index.html文件 并且会自动导入打包好的js文件 这样每次修改后都能自动的修改引入js文件的哈希值 则不需要来回更改
```

```javascript
const HtmlWebpackPlugin = require('html-webpack-plugin');

plugins: [
        new HtmlWebpackPlugin({
            template: 'public/index.html',    // 指定生成的index文件的模板，复制该html文件并自动引入打包输出的所有文件           
            title: 'webpack'   
            // 这种定义属性可以写进 htmlWebpackPlugin.options 里，在html里使用ejs语法可以提取出来，如 <title><%= htmlWebpackPlugin.options.title %></title>  
        }),
]
```

#### 压缩html

```jsx
const HtmlWebpackPlugin = require('html-webpack-plugin');

plugins: [
        new HtmlWebpackPlugin({
            template: 'public/index.html',    // 指定生成的index文件的模板，复制该html文件并自动引入打包输出的所有文件           
			minify: {
                // 移除空格
                collapseWhitespace: true,
                // 移除注释
                removeComments: true
            }
        }),
]
```



------

### 配置样式处理

#### css处理

**css-loader、style-loader、mini-css-extract-plugin**

使js脚本能正常导入css文件并打包，推荐使用`mini-css-extract-plugin`而不用`style-loader`

因为`mini-css-extract-plugin`打包后会生成一个css文件并自动插入到模板html文件中，但是用`style-loader`打包时写进网页头，不好管理

css-loader：

```javascript
const MiniCssExtractPlugin = require('mini-css-extract-plugin');

module: {
	rules: [
				{
                	test: /\.css$/,   // 正则表达式匹配文件末尾为.css的文件类型
                	use: [
                    	MiniCssExtractPlugin.loader,    // 引入
                    	'css-loader',
                	]        
            	},
    ]
}
```

style-loader：

```javascript
const MiniCssExtractPlugin = require('mini-css-extract-plugin');

module: {
        rules: [
            {
                test: /\.css$/,   
                use: [
                    'style-loader',
                    'css-loader'
                ]
                 
                    /*
                        处理步骤：
                         	css-loader先将css文件进行处理
                         	style-loader将处理后的结果动态插入到js
                         	运行网页时再插入到网页头
                    */
                
            }
        ]
},
```

-----

#### less处理

**less、less-loader**

-----使js脚本能识别less文件并成功导入（用less替代css更有效率）

```javascript
module: {
	rules: [
        // 处理css
		{
			test: /\.css$/,   
			use: [
				MiniCssExtractPlugin.loader,   
				'css-loader',
			]        
		},
		
        // 处理less
		{
			test: /\.less$/,
			use:[
				MiniCssExtractPlugin.loader,
				'css-loader',
				'less-loader',	// 将less文件解析成css再用css方式处理
			]
}
```

----

#### css兼容

 **postcss-loader、autoprefixer、postcss-preset-env**

用于解决css兼容问题

使用`postcss-loader + autoprefixer ` 或者`postcss-loader + postcss-preset-env ` 

编写css时，会自动加上前缀以兼容各式浏览器 使其代码兼容性更好

```css
/* 伪元素placeholder在不同浏览器中需要不同兼容 */
::placeholder {
    color: #999;
}
```

```css
/* 打包后生成的css文件，已经自动添加各种浏览器需要添加的兼容前缀 */ 
::-webkit-input-placeholder {
    color: #999;
}
:-moz-placeholder {
    color: #999;
}
::-moz-placeholder {
    color: #999;
}
:-ms-input-placeholder {
    color: #999;
}
::-ms-input-placeholder {
    color: #999;
}
::placeholder {
    color: #999;
}
```

需要在项目根目录下创建一个postcss.config.js才能运行

```javascript
// postcss.config.js
module.exports = {
    plugins: [
        require('autoprefixer')	//或 require('autoprefixer')
    ]
}
```

```javascript
module: {
	rules: [
        // 处理css
		{
			test: /\.css$/,   
			use: [
				MiniCssExtractPlugin.loader,   
				'css-loader',
                'postcss-loader'    // 要最先执行postcss-loader
			]        
		},
		
        // 处理less
		{
			test: /\.less$/,
			use:[
				MiniCssExtractPlugin.loader,
				'css-loader',
				'less-loader',	
                'postcss-loader'    // 要最先执行postcss-loader
			]
		}
 	]      
}
```

如果不想新建`postcss.config.js`文件，可直接在`package.json`里写入browserslist字段配置兼容的要求，并在`webpack.config`里配置postcss的插件，功能一样

```json
// package.json
{
  // 具体使用的关键字需要去查  
  "browserslist": {
  	  // 不同mode的配置，一般生产环境要比开发环境要严格，默认使用生产环境（即使config已经声明module为development）
      // 若要使用开发环境，需要设置nodejs环境变量：process.env.NODE_ENV = 'development'（webpack.config的module.exports前直接添加该语句就行）
      "development":[
          “last 1 chrom version”,		// 兼容最近一个chrom浏览器的版本，下面同理
          “last 1 firefox version”,
          “last 1 safari version”,
      ],
      // 
      "production":[
          “>0.2%”,				// 兼容98%以上的主流浏览器
          "not dead",			// 不去兼容已经停用的浏览器
          "not op_mini all"		// 不去兼容任何opera mini浏览器（主要原因已经停止更新很久并不再使用）
      ]
  }
}
```

```javascript
// webpack.config   
//  处理less同理
module: {
	rules: [
		{
			test: /\.css$/,   
			use: [
				MiniCssExtractPlugin.loader,   
				'css-loader',
                {
                    loader:'postcss-loader',
                    options: {
                        ident: 'procss',
                        plugins: () => [
                            // postcss的插件
                            require('postcss-preset-env')
                        ]
                    }
                }
			]        
		}
	}

```

---

#### 压缩CSS

**optimize-css-assets-webpack-plugin**

```jsx
const  optimizeCssAssetsWebpackPlugin = require('optimize-css-assets-webpack-plugin')

plugins: [
	new optimizeCssAssetsWebpackPlugin()	// 直接调用即可，默认配置即可满足要求
]
```



-----

### 配置资源处理

#### 动态资源处理

**file-loader**

打包时将使用的资源直接拷贝到打包结果的文件目录里

这里处理的是js里import或者css里直接引用的动态资源，在html里直接导入的资源则不会处理，为了处理这种静态资源文件则需要`copy-webpack-plugin`

```javascript
{
	test: /\.(png|jpg|gif)$/,   
	use: [
		{
			loader: 'file-loader',
			options: {
				// name: '[path][name].[ext]'  配置自动生成的图片在dist的存放路径，ext是文件自己的后缀名
				name: 'images/[name].[ext]',
				// 指定公共路径（加在在指定路径前） 即实际的路径为 /images/[name].[ext]  这里的项目根路径为dist 若不指定/则会找不到对应图片 一般是绝对路径而非相对路径
				publicPath: '/'                                 
				// 这样处理的图片需要基于http协议 因此需要构建服务器才能正常显示效果 因此应使用npm run dev
				// 这里的路径是指npm run build 后生成的dist的目录结构的路径
			}
		}
	]

}
```

**url-loader**

功能和file-loader类似且包含了file-loader的功能因为要依赖file-loader 因此使用要同时下载file-loader包）

```javascript
{
	test: /\.(png|jpg|gif)$/,   
	use: [
		{
			loader: 'url-loader',
			options: {
			limit: 80,      // 当资源小于超过80k时 将使用url-loader 此时不会在dist生成对应资源 但是会启用base64加密并导入html 大于80k时则使用file-loader
			name: 'images/[name].[ext]',
			publicPath: '/'     

		}
	]
}
```

-------

#### 静态资源处理

**copy-webpack-plugin**

处理那些不需要动态导入的静态资源文件

```javascript
const CopyPlugin = require('copy-webpack-plugin')

plugins: [
	new CopyPlugin([
		{
            // C:\Users\Marisue\Desktop\webpackDemo\src\static
			from: path.resolve(process.cwd(), 'src/static'),    
            // 将静态文件的资源直接拷贝到 C:\Users\Marisue\Desktop\webpackDemo\dist 而不经过webpack处理
			to: path.resolve(process.cwd(), 'dist/static')        
		}   
	])
]
```

----

### 配置JS处理

#### 兼容ES6

 **babel-loader、@babel/core、@babel/preset-env**

将ES6的代码自动处理成ES5以便让旧版本的浏览器能兼容

```javascript
{
	test: /\.js$/,
    // 优化处理性能项目中node_modules和bower_components目录下的东西都不需要经过webpack处理，反之include属性表示只对哪些目录下的东西进行处理
	exclude: /(node_modules|bower_components)/,     
	use: {
		loader: 'babel-loader',
		options: {
			presets: ['@babel/preset-env']
		}
	}
}
```

-----

#### 语法检查

**eslint-loader、eslint**

（这里没写完，需要配置一个语法检查库，如airbnb）

```jsx
module:{
	reles:[
        {
            test: /\.js$/,
            exclude: /node_modules/,	// 只检查源代码，不会检查第三方的库
            loader: 'eslint-loader',
            options: {}
            
        }
    ]
}	
```

----

#### 压缩JS

生产环境会自动压缩js

```jsx
mode:'production'
```



----

## Webpack优化

```
开发环境：
	优化打包构建速度
	优化代码调试
生产环境：
	优化打包构建速度
	优化代码运行性能
```

### 代码调试

**source- map：**一种提供源代码到构建后代码的映射技术，如果构建后的代码出错了，通过映射可以追踪到源代码错误（在浏览器中调试能看到具体错误原因）`devServer`模式用浏览器进行调试（生产环境不需要调试）

- `source- map`：外部

  提示错误代码准确信息、源代码的错误位置（具体源文件和行数）

- `inline-source-map`：内联

  提示错误代码准确信息、源代码错误位置

- `hidden-source-map`：外部

  错误代码错误原因，只能提示到构建后代码的错误位置（无法看出源文件和具体位置）

- `eval-source-map`：内联

  每一个文件都生成对应的source-map，都在eval

  提示错误代码准确信息、源代码错误位置

- `nosources-source-map`：外部

  提示错误代码准确信息，但无任何源代码信息

- `cheap-source-map`：外部

  提示错误代码准确信息，源代码错误位置

  但是只能精确到行（如果同一行中有多个代码块，一个错则会整行都提示报错，如source-map则会精确到具体代码块）

- `cheap-module-source-map`：外部

  提示错误代码准确信息、源代码错误位置

  会将loader的source-map加入

**隐藏源代码是可以防止代码泄漏，内联和外联的区别是外部会生成文件，而内联没有，因此内联构建速度更快**

```jsx
// 在webpack.config文件下最外层对象中直接添加
{
    devServer: {
	port: 3000,   
	open: true   
	},
    
    devtool: 'source-map'	// 或者 inline-source-map、hidden-source-map......
}

```

**开发环境要求**

- 速度快

  `cheap-source-map`优于`eval-source-map`

- 调试友好

  `souce-map`优于`cheap-module-source-map`优于`cheap-source-map`

- 综合

  `eval-source-map`（react脚手架默认选择）

**生产环境要求**

- 是否隐藏源代码
- 是否需要调试更友好
- 内联会让代码体积变大，因此生产环境不用内联

`nosources-source-map`：全部隐藏

`hidden-source-map`：隐藏源代码，但是会提示错误信息

综合：`source- map`

----

### loader匹配

**oneOf**

正常情况下，每使用一个loader，loader就会在rulese全部匹配一遍指定test格式文件，如下面即使只有`.less`匹配项会用到less-loader，但是实际上less-loader还是会匹配到`.css`

```jsx
module:{
	rules:[
		{
			test: /\.css$/,   
			use: [
				MiniCssExtractPlugin.loader,   
				'css-loader',
                'postcss-loader'   
			]        
		},
		
		{
			test: /\.less$/,
			use:[
				MiniCssExtractPlugin.loader,
				'css-loader',
				'less-loader',	
                'postcss-loader'    
			]
		}
    ]
}
```

oneOf指定了loader寻到第一次匹配项时便不再继续匹配，加快了loader的速度

下面匹配到`.less`时，less-loader便不会再匹配其他规则对象

```jsx
module:{
	rules:[
        {
        	oneOf: [
        		{
                    test: /\.css$/,   
                    use: [
                        MiniCssExtractPlugin.loader,   
                        'css-loader',
                        'postcss-loader'    
                    ]        
                },
                {
                    test: /\.less$/,
                    use:[
                        MiniCssExtractPlugin.loader,
                        'css-loader',
                        'less-loader',	
                        'postcss-loader'    
                    ]
                }
        	]
        }
    ]
}
```

如果有多个同匹配项的规则对象，则应把该对象放在oneOf外面，这样就可以都匹配到了

```jsx
module:{
	rules:[
        {            
			test: /\.css$/,   
			use: [
			MiniCssExtractPlugin.loader,   
			'css-loader',
			... 
			],
            enfore:'pre'	// 注明优先执行该lodaer
            
		}, 
        {
        	oneOf: [
        		{
                    test: /\.css$/,   
                    use: [
                        MiniCssExtractPlugin.loader,   
                        'css-loader',
                        .... 
                    ]        
                }
        	]
        }
    ]
}
```

-----

### 缓存

**babel缓存**

babel将ES6代码编译成JS，但是每次JS改动时都会将所有JS代码重新编译一次，如果只想重新编译改动过的JS代码，需要开启babel缓存，二次构建时，会读取之前的

```jsx
// options里配置
cacheDiretory: true
```

```jsx
{
	test: /\.js$/,
	exclude: /(node_modules|bower_components)/,     
	use: {
		loader: 'babel-loader',
		options: {
			presets: ['@babel/preset-env'],
            cacheDiretory: true
		}
	}
}
```

**文件资源缓存**

为了避免项目更新版本时不更新浏览器缓存，可以为打包出口文件添加哈希值 （因为更改了文件名，对于浏览器来说是新的文件，需要重新缓存）

webpack每一次打包都会生成一个hash值，可以取该哈希的前x位用于重新命名

- hash：

  每次修改任何一个文件，所有文件名的hash至都将改变。所以一旦修改了任何一个文件，整个项目的文件缓存都将失效

````jsx
output : {
    filename: '[name].[hash：8].js' 8表示生成哈希数的位数 
	// 这样生成的文件就会带上哈希数 如home.421384ac.js 和 about.421384ac.js
}
````

但是使用hash时，多个入口的hash是同一个值，因此修改其中任意一个文件整个hash都会变动掉，因此使用chunkHash 

- chunkHash

  根据chunk生成的hash值，如果打包来源于同一个chunk，那么hash值就一样

```jsx
filename: '[name].[chunkHash：8].js'
	// 这样不同的chunk会生成不同的哈希数 即使修改了home.js文件 也只有home.js的哈希值会变动 而about.js的不会 于是在修改项目时 只有home.js的缓存被清掉了
```

但是chunkHash不适用于同一chunk的文件，如一个js文件导入了一个css文件，他们属于同一个chunk，因此若只修改了js，最终打包出来的文件cs和js都会变成一个新的hash

- contenthahs：根据文件内容生成hash值，不同文件的hash值一定不一样（只要文件内容不做修改，一定是同一个hash，有变动则会替换成另外的），这样就令浏览器只清楚掉变动文件的缓存（只有改动的文件重命名了）

```jsx
filename: '[name].[contenthahs：8].js'
```

-----

### 去除无用代码

**tree shaking**

去除文件中写了但是实际上没有被使用的代码（摇掉代码树中的枯叶）

减少代码提及，请求速度更快

满足前提自动开启：

- 必须使用ES6模块化（即export和import文件）
- 开启production环境

```jsx
// 如：
export A = {...}
export B = {...}
// 若整个项目里只有A被使用 import {A} from ...，则打包的时候不会把B的内容也打包进去（和按需加载概念不一样）
```

----

### 代码分割

```
如果没有使用代码分隔，则整个项目的所有js文件只会打包合并成一个总js文件
```

**使用多入口：**

有一个入口，最终输出就有一个bundle

打包后生成home.js和adbout.js文件，如果使用单入口，只会生成一个js文件

```jsx
entry: {
	home : './src/home.js',
	about: './src/about.js'
}

output: {
	path: path.resolve(__dirname,"../dist"),	
    filename: 'js/[name].[contenthash:8].js'				
}
```

但是多入口不灵活，需要多次配置入口

-----

**提取公共代码 -- splitChunks**

一般配合多入口使用：

将公共代码抽离出来，在用户打开一个页面的时候，顺便加载了公共的文件，在打开其他页面的时候，如果其他页面也引用了这个公共文件，就不用重新加载，直接从浏览器缓存中获取，可以解决①相同的资源配重复加载，造成了资源的浪费，（最后的静态资源包会很大）②每个页面打开的时间会变长（影响用户体验）

在入口chunk和异步chunk中发现被重复使用的模块，将重叠的模块以vendor-chunk的形式分离出来







<img src="https://img-blog.csdnimg.cn/20200510093802541.png" style='margin:"0 "'> 

假设有ABC三个入口文件：

```jsx
{
	entry: {
        A: './src/A.js',
        B: './src/B.js',
        C:'./src/C.js',  
	},
	output: {
        path: path.resolve(__dirname, 'dist'),
        filename: '[name].bundle.js',
	}
}
```

```jsx
// A.js
import B,C
// 没有用到B、C模块的代码A
// A和B搭配的代码A-B
// A和C搭配的代码A-C
// A和B、C共同搭配的代码A-B-C

// B.js
import A,C
// C.js
import A,B
// 不使用代码分割打包，即打包A时会打包一遍BC，打包B时会打包一遍AC，打包C会打包一遍AB，重复打包
// 最终生成的A、B、C文件中，A.js包含BC内容，B.js包含AC内容，C.js包含AB内容
```

使用splitChunks打包时，会将公共的代码抽离成单独的一个文件，最终生成的文件为`A.bundle.js`、`B.bundle.js`、`C.bundle.js`、`A-B.bundle.js`、`A-C.bundle.js`、`B-C.bundle.js`、`A-B-C.bundle.js`，因为抽离成了单独的文件，因此可以在打包后的文件中多次重复使用，只需导入单独文件即可

```jsx
// 打包处理后的文件，非源文件，如果不经过处理A文件中会打包进BC内容合并成A.bundle.js
// 但是使用代码分割分离后的A.bundle.js只包含A.js的内容，需要用到依赖时，再导入部分公共文件即可
// A.bundle.js 
import A-B,A-C,A-B-C	// B.bundle.js和C.bundle.js同理
```

```jsx
// 假设a、b文件都引入了一个共同的npm包
const path = require('path')
const webpack = require('webpack')

module.exports = {
  entry: {
    a: './src/a.js',
    b: './src/b.js'
  },
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: '[name].bundle.js',
  },
  optimization: {
    // async 异步(import()语法) initial(同步import xxx from 'xxx') all(所有)
    splitChunks: {
      chunks: 'initial'
    }
  }
}

// 打包后生成的js文件：
// a.bundle.js
// b,bundle.js
// vendors~a~b.bundle.js	

// 假如不使用splitChunks生成的文件：a.js、b.js，公共的npm包会被打包合并到ab文件中，即ab文件有重复打包的东西
```

```jsx
// 想react和react-dom打一个包，vue和vuex打一个包（a和b都使用到了这些模块）
optimization: {
	splitChunks: {
        chunks: 'initial',
        cacheGroups: {
            vue: {
                test: /[\\/]node_modules[\\/](react|react-dom)/,	//[\\/]表示匹配\node_modules...或\node_modules\react...（linux和window的文件路径表示方法不一样）
                priority: -1	// 多个打包规则时，一般要声明优先级，默认-10，数字大的优先处理
            },
            react: {
                test: /[\\/]node_modules[\\/](vue|vuex)/,
                priority: -2
           }
		}
	}
}
// 打包后生成的文件：
// a.bundle.js
// b,bundle.js
// react~a~b.bundle.js
// vendors~a~b.bundle.ks
// vue~a~b.bundle.js
```

配置项详细：

```jsx
 optimization{
        splitChunks: {
            /**
                1. 三个值 
                    async 仅提取按需载入的module
                    initial 仅提取同步载入的module
                    all 按需、同步都有提取
            */
            chunks: "async",
                
            // 只有导入的模块 大于 该值 才会 做代码分割 （单位字节）
            minSize: 30000,
                
            // 提取出的新chunk在两次压缩之前要小于多少kb，默认为0，即不做限制
            maxSize: 0,
                
            // 被提取的chunk最少需要被多少chunks共同引入
            minChunks: 1,
                
            // 按需加载的代码块（vendor-chunk）并行请求的数量小于或等于5个
            maxAsyncRequests: 5, 
                
            // 初始加载的代码块，并行请求的数量小于或者等于3个
            maxInitialRequests: 3,
                
            // 默认命名 连接符
            automaticNameDelimiter: '~',
                
            /**
                name 为true时，分割文件名为 [缓存组名][连接符][入口文件名].js
                name 为false时，分割文件名为 [模块id][连接符][入口文件名].js
                如果 缓存组存在 name 属性时 以缓存组的name属性为准
            */
            name: true,
                
            // 缓存组 当符合 代码分割的 条件时 就会进入 缓存组 把各个模块进行分组，最后一块打包
            cacheGroups: {
                // 如果 引入文件 在node_modules 会被打包 这个缓存组(vendors)
                vendors: {
                    // 只分割 node_modules文件夹下的模块
                    test: /[\\/]node_modules[\\/]/,
                    // 优先级 因为如果 同时满足 vendors、和default 哪个优先级高 就会打包到哪个缓存组
                    priority: -10
                },
                default: {
                    // 表示这个库 至少被多少个chunks引入，
                    minChunks: 2,
                    priority: -20,
                    // 如果 这个模块已经 被分到 vendors组，这里无需在分割 直接引用 分割后的
                    reuseExistingChunk: true
                }
            }
	}
}    
```

SplitChunksPlugin在`production`模式下是默认开启的，但是它默认只作用于异步chunk，如果要作用全部chunk的话，需要配置

```jsx
module.exports = {
	splitChunks: {
        chunks: "all",
    }
}
```

webpack自动split chunks是有几个限制条件的：

- 新产出的vendor-chunk是要被共享的，或者模块来自npm包（node_modules目录下文件，默认引入的node_modules提取的的公共文件叫做vendors命名）

- 新产出的vendor-chunk的大小得大于30kb

- 并行请求vendor-chunk的数量不能超出5个

- 对于entry-chunk而言，并行加载的vendor-chunk不能超出3个

-----

**import动态导入**

使用js指定额外生成bundle文件

如果使用单入口并且不使用splitChunks，懒加载原型

```jsx
// index.js
import(./test)
	.then(() => {
    	console.log('文件加载失败')
	})	
	.catch(() => {
    	console.log('文件加载成功')
	})
// 最终生成index.bundle.js和test.bundle.js文件

// 如果想重命名ChunkNames
import(/* webpackChunkName:'test' */ './test')
```

----

### 懒加载

前提：进行了代码分割（使用懒加载会进行代码分割）

当文件需要使用时才加载指定文件（在浏览器的Network中则是一开始没有加载该文件，使用的时候读取）

下面代码在渲染页面结束还没点击按钮的时候，打印`test.js`，表明了`test.js`文件即使在没有使用的时候，也会进行加载

```jsx
// index.html
<button id='btn'>按钮</button>

// index.js
import {add} form 'test.js'
document.getElementById().onclick() = function() {
	console.log(add(1,2))
}

// test.js
console.log('加载了test.js')
export function add (x, y) {
    return x + y
} 

```

使用懒加载（使用的时候再导入，且导入一次会写入缓存，之后不会再重复导入，因此无需担心每调用一次函数都导入一次）

```jsx
// import {add} form 'test.js'
document.getElementById().onclick() = function() {
    // 懒加载
	import('./test')
    	.then(() => {
			console.log(add(1,2))        
    	})
}
// 浏览器不会自动加载test.js，点击按钮后才进行加载
```

**预加载**

（好用但兼容性较差，特别是非PC端，因此一般用懒加载）

等其他资源加载完毕，浏览器空闲了，再尝试加载预加载资源

```jsx
// import {add} form 'test.js'
document.getElementById().onclick() = function() {
    // 懒加载
	import(/* webpackPrefetch: true*/ './test')
    	.then(() => {
			console.log(add(1,2))        
    	})
}
// 浏览器在点击按钮前会加载test文件，但是不会执行里面的程序，因此无打印信息
```

----

### PWA

渐进式网络开发应用程序（当网络离线时也可访问到数据）

差不多的原理：每一次访问到的部分信息会缓存在一个地方，下次没有网的时候打开，会读取最新的这部分缓存，显示部分页面（但是没有网的时候还是无法请求数据打开其他页面获取新的数据的）

**workbox-webpack-plugin**

```jsx
const workboxWebpackPlugin = require('workbox-webpack-plugin')

plugins: [
   // 打包后生成一个serviceworker配置文件，然后需要在入口文件中注册servicewoker
	new workboxWebpackPlugin.GenerateSW({
        // 帮助serviceWorker快速启动
        // 删除旧的serviceWoker缓存
        clientsClaim: true,
        skipWaiting: true
    })
	
]
```

入口注册servicewoker

sw代码必须运行在服务器上，可通过浏览器开发者模式的`Application`下查看service workers



```jsx
// 入口文件
// 全局变量Navigator 对象包含有关浏览器的信息
// 尝试注册serviceWork
if('serviceWoker' in navigator) {
    window.addEventListener('load', () => {
        navigator.serviceWorker
        	.register('./service-worker.js')
        	.then(() => {
            	console.log('sw注册成功')
        	})
        	.catch(() => {
            	console.log('sw注册失败')
        	})
    })
}
```

---

### 多进程打包

**thread-loader**

将该loader放在一个loader后面执行（即写在前面），就会令指定loader开启多进程打包，一般配合babel-loader用

由于进程启动会有耗时，因此工作消耗时间比较长才需要多进程打包，否则多进程打包反而更加耗时（js代码很多的时候就会带来益处）

```jsx
{
	test: /\.js$/,
	exclude: /(node_modules|bower_components)/,     
	use: [
        // 开启多进程打包
        {
            'thread-loader',
            options: {
                workers: 2	// 进程2个
            }
        }
    	{
			loader: 'babel-loader',
			options: {
				presets: ['@babel/preset-env']
			}
		}
    ]
}
```

----

### externals

当有些包需要从外部引入（cdn）而非从下载的npm包中引入打包时，可以使用externals忽略掉打包该npm包，但是需要在html中引进该包

通过cdn引入包速度会更快

```jsx
module.export = {
	...
	externals: {
        // 库名:包名 拒绝该包被打包
    	jquery: 'jQuery'
	}
}
```

```jsx
// index.js
import $ from 'jquery'
console.log($)
```

html通过cdn引入，不然就无法正常打印了

```html
// index.js
<head>
    <script src="https://apps.bdimg.com/libs/jquery/2.1.4/jquery.min.js"></script>
</head>
```

----

### dll

使用dll技术，对某些库（第三方库：jquery、react、vue……）进行单独打包，这样就不需要在每次代码变动的时候都需要重新打包这些库进来，只需对这些库打包一次即可（如果要更新第三方包，那就需要再重新打包一次这些库）

需要声明另外一个配置文件（webpack.dll.js --其实起名任意），这个配置文件主要是对某些库进行单独打包

```jsx
// webpack.dll.js
const {resolev} = require('path')	// 拼接绝对路径
const webpack require('webpack')	// webpack自带插件

module.exports = {
    entry: {
        jquery: ['jquery']		// 如果有和jquery相关的依赖包可以写进去一并打包 jquery:['jquery','xx','xxx']
    },
    outout: {
        filename: '[name].bundle.js',
        path: resolve(_dirname,'dll'),
        // 打包的库里向外暴漏出去的内容名字 比如打包生成的jquery.bundle.js文件里面 var jquery12345678 = .....
        library: '[name][hash:8]' 
    }，
    plugins: [
    	// 打包生成一个manifest.json文件，提供jquery映射，令webpack知道jquery无需再次打包
    	new webpack.Dllplugin({
    		name: '[name][hash:8]',	// 映射库的暴露名字
    		path: resolve(_dirname,'dll/manifest.json')		// 输出文件路径
            // 最终manifest.json文件里的内容长这样：
            // {name: 'jquery12345678', content: {'..node_modules/jquery/dist/jquery.js'}}
		})
    ],
    mode: 'production'
}
```

webpack默认查找webpack.config.js配置文件，如果需要使用其他配置文件进行打包需要更改配置

```
webpack --config webpack.dll.js
```

使用该配置文件打包一次后，为`webpack.config.js`增添配置

需要下载插件：**add-asset-html-webpack-plugin**

```jsx
// webpack.dll.js
const {resolev} = require('path')	
const webpack require('webpack')	
const addAssetHtmlWebpackPlugin = requir('add-asset-html-webpack-plugin')

module.exports = {
	...
    plugins: [    	
        // 告诉webpack那些库不参与打包
    	new webpack.DllReferencePlugin({
			manifest:resolve(_dirname,'dll/manifest.json')	// 从指定路径中查找不参与打包库的映射json文件
		}),
        // 告诉webpack无需打包之后，需要将某个文件打包输出去，并在html中自动引入该资源
        new  addAssetHtmlWebpackPlugin({
            filepath: resolve(_dirname, 'dll/jquery.bundle.js')
        })
    ],
    mode: 'production'    
    
}
```

再运行一遍webpack打包即可（若使用的第三方库的代码没有变动，则只需运行一次webpack.dll.js打包生成一次使用到的文件），即jquery这个三方库无需再重复打包，能让今后的打包速度变快

-----





