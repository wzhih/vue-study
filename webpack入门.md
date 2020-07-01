# Webpack 入门

## 概念

`webpack`的使用方法，其实就是写配置，依据规则构造一个**配置对象**，放到`webpack.config.js`导出，然后调用`webpack`命令传入此配置文件，进行打包代码

打包代码的流程就是：
1. 从入口脚本开始，递归查找依赖的模块
2. 构建依赖视图
3. 启用加载器和插件处理对应类型文件
4. 输出到定义好的出口目录

### 入口

`entry`属性，默认值`./src`(默认使用目录下的`index.js`)，可以配置为：字符串、数组、对象

应用程序代码和引用的扩展、库，其实都是`模块`，需要有一个入口文件(应用程序可以有多个)

`webpack`配置了这个属性之后，就会从这个入口文件开始，递归构建依赖视图，用于打包


### 出口

`output`属性，值是一个对象，对象两个属性一个是`path`路径、一个是`filename`文件名

这个属性告诉`webpack`，要把打包好的代码输出到哪里，如何命名。

多个入口配置会被输出到同一出口，可以使用占位符来确保：多个入口配置会生成多个入口文件


### 加载器

`loader`加载器，让`webpack`可以去处理那些非`javascript`的文件，从而将这些文件加载进依赖视图中

比如，有个模块引用了一张图片，`webpack`通过使用`file-loader`加载器去将这张图片加载进依赖视图中

不同类型的文件有不同的加载器可以使用，还可以自定义加载器(像`vue`一样，就是自定义了`.vue`文件的加载器)


### 插件

如果加载器是用于加载不同类型文件，那么插件就是用于处理这些不同类型文件，甚至还能提供打包优化、压缩、重新定义环境变量等强大功能


### 模式

`mode`属性，只支持以下两个值：

| 值 | 描述 |
|---|---|
| development | 会将`process.env.NODE_ENV`的值设为`development`。启用`NamedChunksPlugin`和`NamedModulesPlugin`|
| production | 会将`process.env.NODE_ENV`的值设为`production`。启用`FlagDependencyUsagePlugin`,`FlagIncludedChunksPlugin`,`ModuleConcatenationPlugin`,`NoEmitOnErrorsPlugin`,`OccurrenceOrderPlugin`,`SideEffectsFlagPlugin`和`UglifyJsPlugin`|

选择其中一个值，`webpack`在打包的时候，会启动相应模式下的内置优化


## 配置

基础的配置例子

```js
const HtmlWebpackPlugin = require('html-webpack-plugin'); //通过 npm 安装
const webpack = require('webpack'); //访问内置的插件
const path = require('path');

module.exports = {
  // 模式
  mode: 'development',
  //入口
  entry: './src/index.js',
  //出口
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'dist')
  },
  module: {
    rules: [
      // 引入加载器列表
      { test: /\.txt$/, use: 'raw-loader' },
      { test: /\.css$/, use: 'css-loader' },
      { test: /\.ts$/, use: 'ts-loader' }
    ]
  },
  plugins: [
    // 引入插件列表
    new webpack.optimize.UglifyJsPlugin(),
    new HtmlWebpackPlugin({template: './src/index.html'})
  ]
};

```








