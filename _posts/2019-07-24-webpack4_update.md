---
layout: post
title: webpack3 升级 webpack4 踩坑记录
date: 2019-07-24
author: "zhoupeng"
header-img: "img/posts/webpack/1.jpg"
tags: webpack
---

>本篇不包含所有坑，暂时只记录自己踩到的部分坑

### 一.安装

**安装webpack4最新版本**
```
npm install --save-dev webpack@4 
```
**安装新增依赖 webpack-cli** 

*这个在webpack3中，webpack本身和它的CLI是在同一个包中，webpack4中将两个分开管理。*

```
npm install --save-dev webpack-cli
```

### 二.运行

执行本地打包以及运行操作
```
npm run build:dll
npm run start
```

**记得添加mode**

用来告知 webpack 使用相应环境的内置优化

```javascript
module.exports = {
  mode: 'production' //或者 'development'
};
```

**其中遇到的报错：**

>1.Error:webpack.optimize.UglifyJsPlugin has been removed,pleaseuseconfig.optimization.minimizeinstead.

UglifyJsPlugin是用来对js文件进行压缩

webpack4中`UglifyJsPlugin`被废除，需要安装新的插件`uglifyjs-webpack-plugin`进行替换，见[官方文档](https://webpack.docschina.org/migrate/4/#update-plugins)

安装`uglifyjs-webpack-plugin`

```
npm install uglifyjs-webpack-plugin --save-dev 
```

更改 webpack.dll.config.js || webpack.prod.config.js

去除
```javascript
-  new webpack.optimize.UglifyJsPlugin({
-    compress: {
-      warnings: false
-    },
-    mangle: {
-      safari10: true,
-    },
-    output: {
-      comments: false,
-      ascii_only: true,
-    },
-    sourceMap: false,
-    comments: false
-  }),
```
添加
```javascript
const UglifyJsPlugin = require('uglifyjs-webpack-plugin');
...
optimization: { //与entry同级
    minimizer: [
      new UglifyJsPlugin({
        uglifyOptions: {
          compress: false,
          mangle: true,
          output: {
            comments: false,
          },
        },
        sourceMap: false,
      })
    ]   
},
  
```

注意：`uglifyjs-webpack-plugin`更多的配置请参考[详细配置](https://www.npmjs.com/package/uglifyjs-webpack-plugin2)


>2.Error: webpack.optimize.CommonsChunkPlugin has been removed, please use config.optimization.splitChunks instead.

CommonsChunkPlugin 主要是用来提取第三方库和公共模块

CommonsChunkPlugin 已被移除，用splitChunks替代，见[官方文档](https://webpack.docschina.org/migrate/4/#commonschunkplugin)

**更改webpack.base.config.js**

去除
```
// new webpack.optimize.CommonsChunkPlugin({
//   children: true,
//   async: true,
//   minChunks: 2,
// }),
```
添加
```
optimization: {
    splitChunks: {
      chunks: 'async',
      minChunks: 2,
    },
  },
```
注意：`splitChunks`更多的配置请参考[详细配置](https://webpack.docschina.org/plugins/split-chunks-plugin/)


>3.compilation.mainTemplate.applyPluginsWaterfall is not a function

更新html-webpack-plugin到最新版本
```
npm install html-webpack-plugin@latest --save-dev
```

>4.Chunk.entrypoints: Use Chunks.groupsIterable and filter by instanceof Entrypoint instead

这个最后解决方式是用`mini-css-extract-plugin`替代。

我记录下自己更新这个的过程，以下前半部分可以直接跳过。

1. 更新extract-webpack-plugin到最新版本

```
npm install --save-dev extract-text-webpack-plugin@4.0.0-beta.0
```
继续报错

> Path variable [contenthash] not implemented in this context: static/css/style.[contenthash].css

在之前版本中我们使用extract-text-webpack-plugin来提取CSS文件，不过在webpack 4.x中则应该使用mini-css-extract-plugin来提取CSS到单独文件中

更改如下

这是基于webpack 3.0

```javascript
const utils = require('./utils')
const ExtractTextPlugin = require('extract-text-webpack-plugin')
module.exports = {
    //...
    new ExtractTextPlugin({
        filename: utils.assetsPath('css/[name].[contenthash:7].css')
    })
}
```

基于webpack 4.0

```javascript
const utils = require('./utils')
const MiniCssExtractPlugin = require('mini-css-extract-plugin')
module.exports = {
    //...
    new MiniCssExtractPlugin({
        filename: utils.assetsPath('css/[name].[contenthash:7].css')
    })
}
```

css 以及 mini-css-extract-plugin 的 rule配置

```javascript
module: {
    rules: [
      {
        test: /\.(css|less)$/,
        use: [
          {
            loader: MiniCssExtractPlugin.loader,
          },
          {
            loader: 'css-loader',
            options: {
              modules: true,
              importLoaders: 1,
              localIdentName: '[local]'
            }
          },
          {
            loader: 'postcss-loader',
            options: {
              ident: 'postcss',
              plugins: () => [
                require('postcss-flexbugs-fixes'),
                autoprefixer({
                  browsers: [
                    '>1%',
                    'last 4 versions',
                    'Firefox ESR',
                    'not ie < 9', // React doesn't support IE8 anyway
                  ],
                  flexbox: 'no-2009',
                }),
              ],
            }
          },
          {
            loader: 'less-loader',
            options: {
              modifyVars: theme
            }
          }
        ]

      },
    ],
  },
```

> 5.TypeError: webpack.optimize.DedupePlugin is not a constructor

 `DedupePlugin`是用来查找相等或近似的模块，避免在最终生成的文件中出现重复的模块
  
  已经被废除，删除即可，[见官网](https://webpack.docschina.org/migrate/3/#%E7%A7%BB%E9%99%A4-dedupeplugin)
  
> 6.FATAL ERROR: Ineffective mark-compacts near heap limit Allocation failed - JavaScript heap out of...

这个是内存溢出了，需要在启动命令中加一个空间 `--max_old_space_size=4096`

```
"scripts": {
    "start": "better-npm-run start",
},
"betterScripts": {
    "start": {
      "command": "node --max_old_space_size=4096 build/server.js",
      "env": {
        "NODE_ENV": "development",
        "DEPLOY_ENV": "",
        "PUBLIC_URL": "",
        "PORT": "8082"
      }
    },
}
```

7.**最后还有一个大坑**

`offline-plugin`插件，可以用来对多页面应用进行缓存。这个插件的报错同以上`UglifyJsPlugin`的报错。

只需要更新到最新版本即可。

以下记录踩坑过程。

> Error:webpack.optimize.UglifyJsPlugin has been removed,pleaseuseconfig.optimization.minimizeinstead.

因此导致我，删了好几次`UglifyJsPlugin`以及`uglifyjs-webpack-plugin`相关的所有代码，然后依然报错。

又以为是缓存问题，我重启了vscode，重启了终端，电脑也尝试重启一遍，依然报错。

最后逐行注释代码，运行打包操作，发现是`offline-plugin`插件的问题。

问题所在：`offline-plugin`5.0以前的版本会带有`webpack.optimize.UglifyJsPlugin`操作，最新的应该做了些处理。

![图片](http://note.youdao.com/yws/res/1909/557183166E0647FEA73021329080920E)

[详情参考](https://github.com/NekR/offline-plugin/search?q=UglifyJsPlugin&unscoped_q=UglifyJsPlugin)

最后处理方式，更新`offline-plugin`到最新的版本

```
npm install offline-plugin --save-dev
```

### 三、新增TypeScript的打包

**安装**
```
npm install --save-dev typescript ts-loader
```

**添加`tsconfig.json`文件**

可以利用ts初始化命令自动添加
```
tsc --init
```
也可以手动新增文件。

其中配置详情如下，具体查阅[tsconfig.json配置详情](https://www.typescriptlang.org/docs/handbook/tsconfig-json.html)

```json
{
  "compilerOptions": {
    "outDir": "./dist/",
    "noImplicitAny": true,
    "module": "commonjs",
    "target": "es5",
    "jsx": "react",
    "allowJs": true,
    "moduleResolution": "node",
    "esModuleInterop": true,
    "experimentalDecorators": true,
    "noUnusedParameters": true,
    "noUnusedLocals": true,
  },
  "module": "ESNext",
  "exclude": ["node_modules"]
}
```

**配置 webpack 处理 TypeScript, 主要更改点：**
1. 添加rule
2. 添加需要处理的文件后缀，'.ts'、'.tsx'等
```javascript
rules: [
  {
    test: /\.tsx?$/,
    use: 'ts-loader',
    exclude: /node_modules/
  }
]
resolve: {
    extensions: [ '.tsx', '.ts', '.js' ]
},
```

**测试文件TestTsLoader.tsx**

用来检测是否配置成功，导入相应页面即可测试。实测ok

```javascript
import * as React from "react"

interface TsProps {
  name: string
  company: string
}

export default class TestTsLoader extends React.Component<TsProps, {}> {
  render() {
    return (
      <h1>
        Hello, I am {this.props.name}, I in {this.props.company} now!
      </h1>
    )
  }
}
```

参考资料
>1.https://blog.csdn.net/harsima/article/details/80819747
>2.https://www.typescriptlang.org/docs/handbook/tsconfig-json.html
>3.https://webpack.docschina.org/migrate/


#### 四.再推荐一个npm script命令优化插件 `better-npm-run`
参考: 
>1.https://www.jianshu.com/p/710d0a693357
>2.https://www.npmjs.com/package/better-npm-run


