# webpack 打包优化

## loader配置

声明`exclude`或`include`字段

+ exclude 排除不满足条件的文件夹

+ include 需要被loader 处理的文件或文件夹

```js

{
	test: /\.js$/,
	loader: 'babel-loader',
	// exclude: /node_modules/
	include: [resolve('src'), resolve('test'), resolve('static')]
}

```

## 分而治之 

简单来说就是，将业务代码和第三方的代码分开打包，使用`CommonsChunkPlugin`

```js

new webpack.optimize.CommonsChunkPlugin({
  name: 'vendor',
  minChunks: function (module, count) {
    // any required modules inside node_modules are extracted to vendor
    return (
      module.resource &&
      /\.js$/.test(module.resource) &&
      module.resource.indexOf(
        path.join(__dirname, '../node_modules')
      ) === 0
    )
  }
}),
	
new webpack.optimize.CommonsChunkPlugin({
  name: 'manifest',
  chunks: ['vendor']
}),

```

这样做的好处就是我们只需要关系自己的业务代码，第三方代码谁也不会去改动，但是每次打包的时候，vendors的hash也会更改,所以其实是重新打包的


## dllPlugin

作用就是 将静态资源文件（运行依赖包）与源文件分开打包，先使用DllPlugin给静态资源打包，再使用DllReferencePlugin让源文件引用资源文件。


第一步，创建webpack.dll.config.js

```js
var path = require("path");
var webpack = require("webpack");

module.exports = {
  // 第三方包
  entry: {
    vendor: ['react', 'lodash', 'jquery']
  },
  output: {
    path: path.join(__dirname, './static/js'), // 打包后文件输出的位置
    filename: '[name].dll.js',
    library: '[name]_library' 
    // vendor.dll.js中暴露出的全局变量名。
    // 主要是给DllPlugin中的name使用，
    // 故这里需要和webpack.DllPlugin中的`name: '[name]_library',`保持一致。
  },
  plugins: [
    new webpack.DllPlugin({
      path: path.join(__dirname, '.', '[name]-manifest.json'),  // 输出json 
      name: '[name]_library', 
      context: __dirname
    }),
    // 压缩打包的文件
    new webpack.optimize.UglifyJsPlugin({ 
      compress: {
        warnings: false
      }
    })
  ]
};

```

第二步， 使用dllreferencePlugin

在webpack.config.js添加

```js
const HtmlWebpackPlugin = require('html-webpack-plugin')
var CopyWebpackPlugin = require('copy-webpack-plugin')
const path = require('path')

const webpack = require('webpack')
module.exports = {
  entry: {
    app: './src/index.js',
  },
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: '[name][hash].js',
  },
  plugins: [
    new webpack.optimize.UglifyJsPlugin({
      compress: {
        warnings: false
      },
      sourceMap: true
    }),
    //  拆分第三方文件
    // new webpack.optimize.CommonsChunkPlugin({
    //   name: 'vendor',
    //   minChunks: function (module, count) {
    //     return (
    //       module.resource &&
    //       /\.js$/.test(module.resource) &&
    //       module.resource.indexOf(
    //         path.join(__dirname, '../node_modules')
    //       ) === 0
    //     )
    //   }
    // }),
    // //  拆分第三方文件
    // new webpack.optimize.CommonsChunkPlugin({
    //   name: 'manifest',
    //   chunks: ['vendor']
    // }),
    new webpack.DllReferencePlugin({
      context: __dirname,
      manifest: require('./main-manifest.json')
    }),
    new HtmlWebpackPlugin({
      filename: path.resolve(__dirname, 'dist/index.html'),
      template: 'index.html',
      inject: true,
      minify: {
        collapseWhitespace: true,
        removeAttributeQuotes: true
      },
    }),
    new CopyWebpackPlugin([  // 将static copy到指定路径
      {
        from: path.resolve(__dirname, './static'),
        to: path.resolve(__dirname, './dist'),
        ignore: ['.*']
      }
    ])
    
  ]
}

```
## 耗时对比

项目均采用`lease-h5`

传统方式耗时

![](https://ws1.sinaimg.cn/large/006tNbRwly1fuctxnuxz6j30ow0c841u.jpg)



使用dll方式，耗时

![](https://ws1.sinaimg.cn/large/006tNbRwly1fuctwpomxwj30lo0ciq5u.jpg)

## QA

使用Dllplugin进行打包操作，简直不要太溜，至于坑嘛，就是你需要在index.html中手动引入这个js，可以考虑结合`HtmlWebpackPlugin`进行自动添加，正在探索中....




