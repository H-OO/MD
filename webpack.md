## webpack @4.2.0

**指令**

* `webpack` // 让 webpack 默认以根目录下的 webpack.config.js 文件来运行

* `webpack --config [config.js]` // --config 是让 webpack 使用某个配置文件来运行，忽略 webpack.config.js 文件的配置

* `webpack --mode [production|development]` // 4.0新特性 --mode 是让webpack知道当前处于生产环境还是开发环境，生产环境会自动压缩代码

-----------------------------------------------------------------

**Loader**

作用：让 webpack 具有打包除 js 以外的其他文件的能力

本质：对使用 import 和 require 命令引入的资源进行对应的 loader 加工处理再输出

用法：先安装对应的 loader，之后在配置文件中注册使用规则，使用 import 命令引入

以 CSS 文件和图片为例

```javascript
// webpack.config.js
module.exports = {
  module: {
    // 注册使用规则
    rules: [
      {
        test: /\.css$/, // 匹配条件
        use: ['style-loader', 'css-loader'] // 使用的loader 使用顺序：从右往左
      },
      {
        test: /\.(png|svg|jpg|gif)$/,
        use: ['file-loader']
      }
    ]
  }
};
// module.js
import './xxx.css'; // 引入样式表
import img from './xxx.png'; // 引入图片
```

常用文件需要安装的 loader

* `.css` 安装 style-loader、css-loader

* `加载图片` 安装 file-loader、url-loader

* `.html` 安装 html-loader

常用插件

* `clean-webpack-plugin` 清理输出文件夹中不相关文件
* `html-webpack-plugin` 生成 html 文件

-----------------------------------------------------------------

**使用模板生成HTML文件并自动载入CSS和JS**

```javascript
// 安装 html-loader html-webpack-plugin
const HtmlWebpackPlugin = require('html-webpack-plugin');
module.exports = {
  // 【入口】
  entry: {
    // 多入口文件采用对象写法
    index: './src/js/index.js'
  },
  // 【loader】
  module: {
    rules: [
      {
        test: /\.html$/,
        use: ['html-loader']
      }
    ]
  },
  // 【插件】
  plugins: [
    new HtmlWebpackPlugin({
      filename: 'index.html', // 输出文件名
      template: path.join(__dirname, 'src/index_model.html'), // 使用的模板html
      inject: 'body', // 插入body标签底部
      chunks: ['index'], // 插入scripts标签 引入打包后的js
      hash: true, // 文件名添加hash字符串
      // 压缩html
      minify: {
        removeAttributeQuotes: true, // 移除属性的引号
        removeComments: true, // 移除注释
        collapseWhitespace: true // 移除空格
      }
    })
  ],
  // 【输出】
  output: {
    filename: '[name].js', // 输出文件名
    path: path.resolve(__dirname, 'dist') // 输出文件夹路径
  }
};
```
-----------------------------------------------------------------

**使用第三方包**

所有的第三方包必须遵循ES6模块写法：  
需以`export default`或`export`进行输出  
通过`import`进行引入使用

说明一下`export default`跟`export`的用法区别  
```javascript
// test.js

// 方式1： export default 默认输出
const obj = {};
export default obj;
// 引入
import test from 'test.js';
console.log(test); // {}

// 方式2： export 对象解构赋值输出
const obj = {};
const arr = [];
export {obj, arr};
// 引入
import {obj, arr} from 'test.js'; // 全引入
console.log(obj, arr); // {} []
import {obj} from 'test.js'; // 部分引入
console.log(obj); // {}
```
-----------------------------------------------------------------

**url-loader和file-loader的关系**

url-loader中包含有file-loader，是对file-loader进行封装；使用无需依赖于file-loader

小图片转Base64减少HTTP请求
```javascript
module.exports = {
  module: {
    rules: [
      {
        test: /\.(png|svg|jpg|gif)$/,
        use: [{
          loader: 'url-loader',
          options: {
            limit: '3072', // 图片大小大于limit时不转换
            name: 'img/[name].[hash].[ext]' // 在输出文件夹中的路径（自动创建img文件夹存放所有图片）
          }
        }]
      }
    ]
  }
};
```
文件大小大于limit，url-loader会调用file-loader进行处理，参数也会直接传给file-loader

-----------------------------------------------------------------

**devtool**

作用：在使用打包后文件的某一行代码时，可以追溯打包前所在源代码的位置

注意：只在开发环境下使用

常用选项：`devtool: 'source-map'`

-----------------------------------------------------------------

**观察模式**

作用：观察文件改动，自动执行编译

命令`webpack --watch`

缺点：浏览器需要手动重新加载

-----------------------------------------------------------------

**webpack-dev-server**

作用：提供web服务，自动执行编译，并实现浏览器自动重新加载

使用：先安装`webpack-dev-server`，再修改配置文件

```javascript
// webpack.config.js
module.exports = {
  // web服务
  devServer: {
    host: '192.168.1.xxx', // 域名/IP，默认localhost
    port: '5000', // 端口，默认8080
    contentBase: './dist' // devServer访问该目录的文件
  }
};
```
-----------------------------------------------------------------

**模块热替换**

HMR(Hot Module Replacement) 是webpack的内置插件

作用：当项目体积较大时，只更新变更内容，无需全部编译，以节省宝贵的开发时间

使用场景：只用于开发环境

可能存在的疑问：HTML上的修改不能自动触发浏览器的刷新

目前对HMR的理解：  
HMR仅限于入口文件import的CSS和JS文件进行热替换  
修改模板HTML文件的任何内容会触发重新编译，但浏览器不会热替换，也不自动刷新  
修改HTML文件无法实时看到修改后的效果，需要手动刷新浏览器  

那么问题来了，HMR不就变得鸡肋了吗！  
我认为不是的，当项目体积较大时，修改某个模块会导致全部模块进行重新编译，这是个耗时的操作  
但有了HMR，就能监听某个模块发生改动，只替换有改动的模块，而不会进行重新编译  
HMR解决了改动某个模块就要全部模块进行重新编译的耗时等待问题，节省出宝贵的开发时间

对于修改HTML文件不进行热替换的问题，...若有修改到就手动刷新浏览器即可，毕竟HMR解决了关键的耗时问题

开启HMR
```javascript
// webpack.config.js
const webpack = require('webpack');
module.exports = {
  devServer: {
    contentBase: './dist', // devServer访问该目录的文件
    hot: true // 【HMR】
  },
  plugins: [
    new webpack.NamedModulesPlugin(), // 容易查看要修补(patch)的依赖，辅助HMR
    new webpack.HotModuleReplacementPlugin() // 【HMR】
  ]
};

// main.js
import module1 from './module1.js';
module1(); // 执行模块1返回的函数
if (module.hot) {
  // 监听 module1.js 文件的改动 改动则执行回调函数
  module.hot.accept('./module1.js', function() {
    module1(); // 模块1返回的函数重新执行
  });
}

// module1.js
export default function module1() {
  const content = 'webpack';
  console.log(content);
}
```
-----------------------------------------------------------------

**添加一个公共模块**

目的：方法共享，同时在使用该模块时，未引用的代码会被webpack标记

可以通过 `--mode production` 参数，删除标记的未引用代码

格式如下：
```javascript
// math.js
export function first() {
  // todosomething...
}
export function second() {
  // todosomething...
}

// use
import {first} from './math.js'; // 引入first
```
-----------------------------------------------------------------

**生产环境构建-配置**

开发环境和生产环境的构建目标差异很大，所以需要独立去写每个环境的webpack配置

但仍要遵循不重复原则，保留一个通用配置，然后将配置不同点与相同点进行合并

安装 `npm install --save-dev webpack-merge` 

-----------------------------------------------------------------

**生产环境构建-指定环境**

可以通过参数指定 `--config [production|development]`

访问`process.env.NODE_ENV`变量可获取当前环境

-----------------------------------------------------------------

**抽离CSS**

安装 `extract-text-webpack-plugin` 插件

使用
```javascript
const ExtractTextPlugin = require('extract-text-webpack-plugin');
// 单实例
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        // use: ['style-loader', 'css-loader']
        use: ExtractTextPlugin.extract({
          fallback: 'style-loader',
          use: 'css-loader' // sass ['css-loader', 'sass-loader']
        })
      }
    ]
  },
  plugins: [
    new ExtractTextPlugin({
      filename: 'style.css' // 输出文件路径+文件名
    })
  ]
};

// 多实例
const extractCSS = new ExtractTextPlugin('stylesheets/[name]-one.css');
const extractSCSS = new ExtractTextPlugin('stylesheets/[name]-two.css');
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        use: extractCSS.extract(['css-loader', 'postcss-loader'])
      },
      {
        test: /\.scss$/,
        use: extractSCSS.extract(['css-loader', 'sass-loader'])
      }
    ]
  }
};
```
-----------------------------------------------------------------