---
title: typeScript --- 入坑整配置
date: 2021-01-18 14:15:23
tags: typeScript
---

## typeScript

首先，准备 node 环境 + Vscode

```
mkdir <dirName>
npm init -y
npm i typescript -g
tsc -h
tsc --init
```

现在，你的 tsconfig.json 已经创建完成

```
tsc <文件路径> // 查看ts编译的文件 或者可以去官网的tsplayground查看编译结果

npm i webpack webpack-cli webpack-dev-server -D // 配置webpack
npm i ts-loader typescript -D // 引入ts-loader
npm i html-webpack-plugin -D // html的加载模块
npm i clean-webpack-plugin -D // 清除dist打包文件
npm i webpack-merge -D // 合并配置项
```

项目目录如下：

```
project
│   packagejson
│   README.md
│   tsconfig.js
│
└───src
│   │   index.ts
│   │   ...
│   │
│   └───tpl
│       │   index.html
│       │   ...
│       │   ...
│
└───build
    │   webpack.base.config.js
    │   webpack.config.js
    │   webpack.dev.config.js
    │   webpack.pro.config.js
```

================================================

### webpack.base.config.js

```
const HtmlWebpackPlugin = require("html-webpack-plugin");

module.exports = {
  entry: "./src/index.ts", // 入口文件
  output: {
    filename: "app.js", // 输出文件
  },
  resolve: {
    extensions: [".js", ".ts", ".tsx"],
  },
  module: {
    rules: [
      {
        test: /\.tsx?$/i,
        use: [
          {
            loader: "ts-loader",
          },
        ],
        exclude: /node_modules/,
      },
    ],
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: "./src/tpl/index.html",
    }),
  ],
};

```

### webpack.config.js

```
const merge = require("webpack-merge");
const baseConfig = require("./webpack.base.config");
const devConfig = require("./webpack.dev.config");
const proConfig = require("./webpack.pro.config");

module.exports = (env, argv) => {
  let config = argv.mode === "development" ? devConfig : proConfig;
  // 合并配置文件
  return merge(baseConfig, config);
};


```

### webpack.dev.config.js

```
module.exports = {
    devtool: 'cheap-module-eval-source-map'
}
```

### webpack.pro.config.js

```

const { CleanWebpackPlugin } = require("clean-webpack-plugin");

module.exports = {
  plugins: [
    // 自动清空dist目录
    new CleanWebpackPlugin(),
  ],
};

```

### package.json 注意修改 package.json 的入口配置

```
{
  "name": "type-script",
  "version": "1.0.0",
  "description": "",
  "main": "./src/index.ts",
  "scripts": {
    "start": "webpack-dev-server --mode=development --config ./build/webpack.config.js",
    "build": "webpack --mode=production --config ./build/webpack.config.js",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "clean-webpack-plugin": "^3.0.0",
    "html-webpack-plugin": "^3.2.0",
    "ts-loader": "^8.0.11",
    "typescript": "^4.1.2",
    "webpack": "^4.35.2",
    "webpack-cli": "^3.3.5",
    "webpack-dev-server": "^3.7.2",
    "webpack-merge": "^4.2.1"
  }
}

```
