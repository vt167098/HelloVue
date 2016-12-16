建置配合wio執行可用的環境
===

# 依下列的步驟執行(配合VS Code)

## 由Git clone下來需修改的部份
### clone時請修改成將要命名的專案(目錄)
修改package.json
```
{
  "name": "hellovue", //修改成新的專案名稱
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "Vincent",
  ...
  ...
  }
}
```
再重新執行下列指令
``` bash
npm init
npm install
```
## Clone專案說明結束
#

## 初步建置
### 首先新增目錄, 並初始npm
``` bash
mkdir hellovue 
cd hellovue 
npm init
npm i vue vue-template-compiler --save-dev
```

### 安裝 webpack, webpack-dev-server 與相關 loaders
```
npm i webpack webpack-dev-server webpack-merge css-loader style-loader file-loader url-loader babel-loader babel-core babel-plugin-transform-runtime babel-preset-es2015 vue-loader vue-hot-reload-api -D
```
[網頁參考資料_關於vue2.0之後的改變](https://segmentfault.com/a/1190000005363030)
```
Failed to mount component: template or render function not defined. (found in root instance)
若您看到上面錯誤訊息，這是由於 Vue 2 之後分成 standalone 完整版與 runtime-only 版。差異在於完整版包含了編譯器，支援 template 以及使用了瀏覽器的 API。

而 NPM 模組預設只會匯出 runtime-only ，若要加入 compiler 和 template 支援則需增加 webpack 的設定。
```

### 新增 webpack.config.js

貼入下列代碼
``` javascript
var path = require('path')
var config = {
  entry: path.join(__dirname, 'src', 'main'),
  output: {
    path: path.join(__dirname, 'dist'),
    filename: 'vue-bundle.js',
    publicPath: '/dist/'
  },
  module: {
    loaders: [
      {
        test: /\.js$/,
        loader: 'babel',
        exclude: /node_modules/
      }
    ]
  },
  resolve: {
    extensions: ['', '.js', '.vue'],
    /**
     * Vue v2.x 之後 NPM Package 預設只會匯出 runtime-only 版本，若要使用 standalone 功能則需下列設定
     */
    alias: {
      vue: 'vue/dist/vue.js'
    }
  }
}

module.exports = config
```
或(下面是配合vue-loader的方式)
``` javascript
var path = require('path')

var config = {
  entry: [
    'webpack/hot/dev-server',
    path.join(__dirname, 'src', 'main')
  ],
  output: {
    publicPath: '/dist/',
    path: path.join(__dirname, 'dist'),
    filename: 'bundle.js'
  },
  module: {
    loaders: [
      {
        test: /\.js$/,
        loader: 'babel',
        exclude: /node_modules/
      },
      {
        test: /\.vue$/,
        loader: 'vue'
      }
    ]
  },
  resolve: {
    /**
     * Vue v2.x 之後 NPM Package 預設只會匯出 runtime-only 版本
     */
    alias: {
      vue: 'vue/dist/vue.js'
    },
    extensions: ['', '.js', '.vue']
  }
}

module.exports = config
```

### 在專案目錄下新增兩個子目錄
一個為 src, 一個為 dist

在專案目錄下, 新增檔案 index.html

``` html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Vue.js v2</title>
</head>
<body>
  <div id="app">{{ message }}</div>
  <script src="dist/bundle.js"></script>
</body>
</html>
```
或(下面是搭配vue-loader的方式)
``` html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Vue.js v2</title>
</head>
<body>
  <div id="app">
    <app></app>
  </div>

  <script src="dist/bundle.js"></script>
</body>
<!--vue-loader 的用途是提供一種更方便的組織方式讓我們把元件即一個 component 中需要的 js 行為, css 樣式, template 樣板放在一個 .vue 的檔案中。-->
</html>
```

### 在app子目錄下,新增一個index.jsx的檔案
``` jsx
import React from 'react';
import {render} from 'react-dom';

class App extends React.Component {
  render () {
    return <p> Hello React!</p>;
  }
}

render(<App/>, document.getElementById('app'));
```

### 執行 webpack 打包
```
webpack -d
```
