# untitled

> A Vue.js project

## Build Setup

``` bash
# install dependencies
npm install

# serve with hot reload at localhost:8080
npm run dev

# build for production with minification
npm run build

# build for production and view the bundle analyzer report
npm run build --report

# run unit tests
npm run unit

# run e2e tests  
npm run e2e  

# run all tests  
npm test
```

# 目录构建

进入src文件夹中，新建一个文件夹，取名view，用于存放页面文件，然后在pages里面新建一个文件夹叫index用于存放首页，然后将下图红框内的文件(文件夹)都拉进index文件夹里面，还有最外层的index.html也拉进去来。最后进入index文件夹，将里面的main.js改成index.js，保证页面的入口js文件和模板文件的名称一致  

# 配置utils.js 文件

// glob是webpack安装时依赖的一个第三方模块，还模块允许你使用 *等符号, 例如lib/*  .js就是获取lib文件夹下的所有js后缀名的文件  
var glob = require('glob')  
// 页面模板  
var HtmlWebpackPlugin = require('html-webpack-plugin')  
// 取得相应的页面路径，因为之前的配置，所以是src文件夹下的view文件夹  
// 你的文件是什么目录就是什么目录  
var PAGE_PATH = path.resolve(__dirname, '../src/view')  
// 用于做相应的merge处理  
var merge = require('webpack-merge')  


//多入口配置  
// 通过glob模块读取pages文件夹下的所有对应文件夹下的js后缀文件，如果该文件存在  
// 那么就作为入口处理  
exports.entries = function() {  
  var entryFiles = glob.sync(PAGE_PATH + '/*/*.js')  
  console.log(entryFiles)  
  var map = {}  
  entryFiles.forEach((filePath) => {  
    var filename = filePath.substring(filePath.lastIndexOf('\/') + 1,   filePath.lastIndexOf('.'))  
    map[filename] = filePath  
  
  })  
  console.log(map)  
  return map  
}  
  
//多页面输出配置  
// 与上面的多页面入口配置相同，读取pages文件夹下的对应的html后缀文件，然后放入数组中  
exports.htmlPlugin = function() {  
  let entryHtml = glob.sync(PAGE_PATH + '/*/*.html')  
  let arr = []  
  entryHtml.forEach((filePath) => {  
    let filename = filePath.substring(filePath.lastIndexOf('\/') + 1,   filePath.lastIndexOf('.'))  
    let conf = {  
      // 模板来源  
      template: filePath,  
      // 文件名称  
      filename: filename + '.html',  
      // 页面模板需要加对应的js脚本，如果不加这行则每个页面都会引入所有的js脚本  
      chunks: ['manifest', 'vendor', filename],  
      inject: true  
    }  
    if (process.env.NODE_ENV === 'production') {  
      conf = merge(conf, {  
        minify: {  
          removeComments: true,  
          collapseWhitespace: true,  
          removeAttributeQuotes: true  
        },  
        chunksSortMode: 'dependency'  
      })  
    }  
    arr.push(new HtmlWebpackPlugin(conf))  
  })  
  return arr  
}  

# 修改build/webpack.base.conf.js

注释掉  
  /*entry: {  
    app: './src/main.js'  
  },*/  
添加   
  entry: utils.entries(),  

# 修改build/webpack.dev.conf.js  
注释掉  

 // https://github.com/ampedandwired/html-webpack-plugin  
/*    new HtmlWebpackPlugin({  
      filename: 'index.html',  
      template: 'index.html',  
      inject: true  
    }),*/  
    // copy custom static assets  

在plugins:[]后面添加.concat(utils.htmlPlugin())  

#修改webpack.prod.conf.js
注释掉  
 /*  new HtmlWebpackPlugin({  
      filename: process.env.NODE_ENV === 'testing'  
        ? 'index.html'  
        : config.build.index,  
      template: 'index.html',  
      inject: true,  
      minify: {  
        removeComments: true,  
        collapseWhitespace: true,  
        removeAttributeQuotes: true  
        // more options:   
        // https://github.com/kangax/html-minifier#options-quick-reference  
      },  
      // necessary to consistently work with multiple chunks via CommonsChunkPlugin  
      chunksSortMode: 'dependency'  
    }),*/     

在plugins:[]后面添加.concat(utils.htmlPlugin())  

#添加测试文件
test文件夹  

* test.html文件  

<!DOCTYPE html>  
<html>  
<head>  
  <meta charset="utf-8">  
  <title>multi_page</title>  
</head>  
<body>  
<div id="app"></div>  
<!-- built files will be auto injected -->  
</body>  
</html>  

* test.js文件
  
import Vue from 'vue'  
import cell from './test.vue' 
  
/* eslint-disable no-new */  
new Vue({  
  el: '#app',  
  render: h => h(cell)  
})  

* test.vue文件  
  
<template>  
  <div id="app">  
    <cell></cell>  
  </div>  
</template>  
  
<script>  
import cell from '../../components/cell.vue'  
export default {  
  name: 'app',  
  components: { cell }  
}  
</script>  
  
<style>  
  #app {  
    font-family: 'Avenir', Helvetica, Arial, sans-serif;  
    -webkit-font-smoothing: antialiased;  
    -moz-osx-font-smoothing: grayscale;  
    text-align: center;  
    color: #2c3e50;  
    margin-top: 60px;  
  }  
</style>  

#通过超链接跳转  
<li><a href="test.html">Core Docs</a></li>  
