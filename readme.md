# The most concise development environment for front-end developers by  typescript + react + webpack + eslint

## 前言


作为一枚前端开发者来说，与时俱进是少不了的。


近些年来的各种前端开发工具层出不穷，让人眼花缭乱。


虽然单纯的写一个页面，用 html、css、JavaScript 三者就够了，甚至有的简单的页面，连 JavaScript 也不需要，单凭 html + css 就足以胜任。


但是对于大型的项目来说，不用前端框架开发，项目简直就是灾难现场。


一般而言，大型项目都不会是由一个人单独开发完成的，而是需要多人协作开发。每个人的代码风格都不尽相同，而且每个人的水平都参差不齐，这就导致会碰到各种问题，最后往往搞得大家苦不堪言。


得益于 typescript 的兴起以及近些年的蓬勃发展，前端项目的开发也越来越规范化了。


本文的目的就是记录下，怎么配置最简单的 typescript + react + webpack + eslint 前端的开发环境。
## 1. 初始化项目
这一步是初始化项目必不可少的，也没什么高深的地方。打开控制台，执行以下命令就好了。

```bash
mkdir demo && cd demo && git init && npm init -y

echo node_modules/ >> .gitignore

mkdir src && touch src/app.js src/index.html
```
依次执行完以上命令以后，我们会得以下目录结构：
```bash
$ tree
.
├── package.json
└── src
    ├── app.js
    └── index.html
```


下面我们来先补充下 `index.html`  和 `app.js` 里面的内容。


index.html
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>demo</title>
</head>
<body>
  <div id="root"></div>
  <script src="./app.js" type="module"></script>
</body>
</html>
```
app.js
```javascript
const root = document.querySelector('#root');

const h1 = document.createElement('h1');
h1.innerText = "Hello, typescript + react + webpack + eslint.";

root.appendChild(h1);
```


在浏览器打开 `index.html` 页面，出现以下结果：
![image.png](https://cdn.nlark.com/yuque/0/2020/png/204954/1592718537131-dfc480a8-295b-4d4d-af80-ba9df3a7daae.png#align=left&display=inline&height=130&margin=%5Bobject%20Object%5D&name=image.png&originHeight=260&originWidth=1398&size=22627&status=done&style=none&width=699)
## 2. 添加 webpack
添加 webpack，然后创建配置文件
```bash
npm install --save-dev webpack webpack-cli webpack-dev-server webpack-merge clean-webpack-plugin

mkdir build && touch webpack.dev.js build/webpack.prod.js build/webpack.common.js
```
执行完以上命令以后，目录结构会变成如下所示
![image.png](https://cdn.nlark.com/yuque/0/2020/png/204954/1592718945382-b58309e0-2847-4206-9ef1-1c8d4dd70349.png#align=left&display=inline&height=396&margin=%5Bobject%20Object%5D&name=image.png&originHeight=792&originWidth=618&size=70696&status=done&style=none&width=309)
### webpack.common.js
基本的配置
```javascript
const path = require('path');
const HtmlWebpackPlugin = require("html-webpack-plugin");

module.exports = {
  entry: './src/app.js',
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, '../dist'),
    publicPath: './',
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: "./src/index.html",
    }),
  ],
};

```


### webpack.dev.js
开发环境的配置
```javascript
const path = require('path');
const webpackMerge = require('webpack-merge');
const common = require('./webpack.common.js');

module.exports = webpackMerge(common, {
  mode: 'development',
  devtool: 'inline-source-map',
  devServer: {
    contentBase: path.join(__dirname, '../dist'),
    publicPath: '/',
    compress: true,
    port: 9000,
  },
});
```


### webpack.prod.js
生产环境的配置
```javascript
const merge = require('webpack-merge');
const common = require('./webpack.common.js');
const {CleanWebpackPlugin} = require('clean-webpack-plugin');

module.exports = merge(common, {
  mode: 'production',
  devtool: 'source-map',
  plugins: [
    new CleanWebpackPlugin(),
  ],
  watchOptions: {
    poll: 1000, // 轮询间隔时间
    aggregateTimeout: 500, // 防抖（在输入时间停止刷新计时）
    ignored: /node_modules/,
  },
});
```


### 修改 index.html
```diff
 </head>
 <body>
   <div id="root"></div>
-  <script src="./app.js" type="module"></script>
 </body>
 </html>
```
### 朝 package.json 添加脚本
```diff
  "description": "",
   "main": "index.js",
   "scripts": {
+    "start": "webpack-dev-server --open --config ./build/webpack.dev.js",
+    "watch": "webpack --watch --config ./build/webpack.prod.js",
+    "build": "webpack --config ./build/webpack.prod.js",
     "test": "echo \"Error: no test specified\" && exit 1"
   },
   "keywords": [],
```
### 运行脚本
`npm start` 
```bash
$ npm start

> demo@1.0.0 start /Users/root1/Desktop/demo
> webpack-dev-server --open --config ./build/webpack.dev.js

ℹ ｢wds｣: Project is running at http://localhost:9000/
ℹ ｢wds｣: webpack output is served from /
ℹ ｢wds｣: Content not from webpack is served from /Users/root1/Desktop/demo/dist
ℹ ｢wdm｣: wait until bundle finished: /
ℹ ｢wdm｣: Hash: e989ba9f2efc6598e907
Version: webpack 4.43.0
Time: 540ms
Built at: 2020/06/21 下午2:26:14
     Asset       Size  Chunks             Chunk Names
 bundle.js    876 KiB    main  [emitted]  main
index.html  249 bytes          [emitted]  
Entrypoint main = bundle.js
[0] multi (webpack)-dev-server/client?http://localhost:9000 ./src/app.js 40 bytes {main} [built]
[./node_modules/ansi-html/index.js] 4.16 KiB {main} [built]
[./node_modules/html-entities/lib/index.js] 449 bytes {main} [built]
[./node_modules/loglevel/lib/loglevel.js] 8.41 KiB {main} [built]
[./node_modules/url/url.js] 22.8 KiB {main} [built]
[./node_modules/webpack-dev-server/client/index.js?http://localhost:9000] (webpack)-dev-server/client?http://localhost:9000 4.29 KiB {main} [built]
[./node_modules/webpack-dev-server/client/overlay.js] (webpack)-dev-server/client/overlay.js 3.51 KiB {main} [built]
[./node_modules/webpack-dev-server/client/socket.js] (webpack)-dev-server/client/socket.js 1.53 KiB {main} [built]
[./node_modules/webpack-dev-server/client/utils/createSocketUrl.js] (webpack)-dev-server/client/utils/createSocketUrl.js 2.91 KiB {main} [built]
[./node_modules/webpack-dev-server/client/utils/log.js] (webpack)-dev-server/client/utils/log.js 964 bytes {main} [built]
[./node_modules/webpack-dev-server/client/utils/reloadApp.js] (webpack)-dev-server/client/utils/reloadApp.js 1.59 KiB {main} [built]
[./node_modules/webpack-dev-server/client/utils/sendMessage.js] (webpack)-dev-server/client/utils/sendMessage.js 402 bytes {main} [built]
[./node_modules/webpack-dev-server/node_modules/strip-ansi/index.js] (webpack)-dev-server/node_modules/strip-ansi/index.js 161 bytes {main} [built]
[./node_modules/webpack/hot sync ^\.\/log$] (webpack)/hot sync nonrecursive ^\.\/log$ 170 bytes {main} [built]
[./src/app.js] 174 bytes {main} [built]
    + 18 hidden modules
Child HtmlWebpackCompiler:
     1 asset
    Entrypoint HtmlWebpackPlugin_0 = __child-HtmlWebpackPlugin_0
    [./node_modules/html-webpack-plugin/lib/loader.js!./src/index.html] 455 bytes {HtmlWebpackPlugin_0} [built]
ℹ ｢wdm｣: Compiled successfully.
```


`npm run build` 
```bash
$ npm run build

> demo@1.0.0 build /Users/root1/Desktop/demo
> webpack --config ./build/webpack.prod.js

Hash: 82d149378677fd82d8b9
Version: webpack 4.43.0
Time: 337ms
Built at: 2020/06/21 下午2:27:55
        Asset       Size  Chunks                   Chunk Names
    bundle.js   1.09 KiB       0  [emitted]        main
bundle.js.map   4.89 KiB       0  [emitted] [dev]  main
   index.html  228 bytes          [emitted]        
Entrypoint main = bundle.js bundle.js.map
[0] ./src/app.js 174 bytes {0} [built]
Child HtmlWebpackCompiler:
     1 asset
    Entrypoint HtmlWebpackPlugin_0 = __child-HtmlWebpackPlugin_0
    [0] ./node_modules/html-webpack-plugin/lib/loader.js!./src/index.html 455 bytes {0} [built]
```


`npm run watch` 


```bash
$ npm run watch

> demo@1.0.0 watch /Users/root1/Desktop/demo
> webpack --watch --config ./build/webpack.prod.js


webpack is watching the files…

Hash: 82d149378677fd82d8b9
Version: webpack 4.43.0
Time: 130ms
Built at: 2020/06/21 下午2:31:00
        Asset       Size  Chunks                   Chunk Names
    bundle.js   1.09 KiB       0  [emitted]        main
bundle.js.map   4.89 KiB       0  [emitted] [dev]  main
   index.html  228 bytes          [emitted]        
Entrypoint main = bundle.js bundle.js.map
[0] ./src/app.js 174 bytes {0} [built]
Child HtmlWebpackCompiler:
     1 asset
    Entrypoint HtmlWebpackPlugin_0 = __child-HtmlWebpackPlugin_0
    [0] ./node_modules/html-webpack-plugin/lib/loader.js!./src/index.html 455 bytes {0} [built]
```


## 2. 添加 react
添加 react 开发依赖以及类型文件。
```bash
npm install --save react react-dom
npm install --save-dev @types/react @types/react-dom
```
单纯只是添加了这些的话，我们暂时还无法编译运行。


因为 jsx 语法，需要我们配置 babel 才能正确的编译 js 代码。


我们知道的是，babel 配置一直是 webpack 配置里面的“玄学”，特别是对于初学者而言，着实不友好。

但是如果我们往下看，当我们朝项目中配置了 typescript 以后，就不需要配置 babel 就能直接编译运行了。


因为 typescript 不能直接运行在浏览器上，需要编译为 js 才能运行，如果我们写的是 tsx 文件，那么在由 typescript 编译为 js 的过程中，jsx 语法直接就被编译好了，不需要我们自己操心了。

## 3. 添加 typescript
添加 typescript 依赖以及配置文件。
```bash
npm install --save-dev typescript ts-loader source-map-loader

touch tsconfig.json
```
下面是 `tsconfig.json` 配置文件里面的内容：
```json
{
  "compilerOptions": {
      "outDir": "./dist/",
      "sourceMap": true,
      "noImplicitAny": true,
      "module": "commonjs",
      "target": "es5",
      "jsx": "react",
      "allowJs": true,
      "baseUrl": ".",
      "esModuleInterop": true,
  },
  "include": [
      "./src/**/*"
  ],
  "exclude": [
    "dist",
    "node_modules"
  ]
}
```


接下来，我们就修改 `webpack.common.js`  文件，让其支持 typescript 的编译
```diff
       template: "./src/index.html",
     }),
   ],
+
+  resolve: {
+    extensions: ['.ts', '.tsx', '.js', '.json'],
+  },
+
+  module: {
+    rules: [
+      {
+        test: /\.ts(x?)$/,
+        exclude: /node_modules/,
+        use: [
+          {
+            loader: 'ts-loader',
+          },
+        ],
+      },
+      {enforce: 'pre', test: /\.js$/, loader: 'source-map-loader'},
+    ],
+  },
 };
```
接下来，我们讲 `app.js` 文件重命名为 `app.tsx` ，然后改一下里面的代码，改成 react 的写法
```typescript
import React, { FC } from "react";
import ReactDom from "react-dom";

const Hello: FC<{title: string}> = ({ title }) => {
  return <h1>{title}</h1>;
};

ReactDom.render(
  <Hello title="Hello, typescript + react + webpack + eslint." />,
  document.querySelector("#root"),
);
```
然后改一下 webpack.common.js 里面的入口文件


```diff
 const HtmlWebpackPlugin = require("html-webpack-plugin");
 
 module.exports = {
-  entry: './src/app.js',
+  entry: './src/app.tsx',
   output: {
     filename: 'bundle.js',
     path: path.resolve(__dirname, '../dist'),
```
然后运行 npm start ，发现项目能正常运行
![image.png](https://cdn.nlark.com/yuque/0/2020/png/204954/1592722388259-62d49ccb-b1e2-4f48-9461-89b5926d1ecc.png#align=left&display=inline&height=257&margin=%5Bobject%20Object%5D&name=image.png&originHeight=514&originWidth=1528&size=42194&status=done&style=none&width=764)
## 4. 添加 eslint
输入下列命令，为项目添加 eslint 规则约束，这一项是可选的，但是为了项目代码的统一，建议还是用上。


唯一不好的地方是，用了以后，在写代码的过程中，经常会发现，自己写的不符合规范，还得花时间研究下，为啥自己写的不符合规范。


运行以下命令
```bash
npm install eslint -g && eslint --init
```


之后会出现让你进行选择的选项，类似下面的情况：
```bash
$ eslint --init
? How would you like to use ESLint? (Use arrow keys)
  To check syntax only 
❯ To check syntax and find problems 
  To check syntax, find problems, and enforce code style 
```
不要着急，一路选择下去


```bash
$ eslint --init
? How would you like to use ESLint? To check syntax, find problems, and enforce 
code style
? What type of modules does your project use? JavaScript modules (import/export)


? Which framework does your project use? React
? Does your project use TypeScript? Yes
? Where does your code run? (Press <space> to select, <a> to toggle all, <i> to 
invert selection)Browser
? How would you like to define a style for your project? Use a popular style gui
de
? Which style guide do you want to follow? Google (https://github.com/google/esl
int-config-google)
? What format do you want your config file to be in? JavaScript
Checking peerDependencies of eslint-config-google@latest
Local ESLint installation not found.
The config that you've selected requires the following dependencies:

eslint-plugin-react@latest @typescript-eslint/eslint-plugin@latest eslint-config-google@latest eslint@>=5.16.0 @typescript-eslint/parser@latest
? Would you like to install them now with npm? (Y/n) y
```
## 

最后输入 y 回车，eslint 相关配置就会自动安装到项目中去了


最后朝 package.json 中新增一个 fix 脚本
```diff
     "start": "webpack-dev-server --open --config ./build/webpack.dev.js",
     "watch": "webpack --watch --config ./build/webpack.prod.js",
     "build": "webpack --config ./build/webpack.prod.js",
+    "lint": "eslint --fix --ext .js --ext .jsx --ext .ts --ext .tsx src/ ",
     "test": "echo \"Error: no test specified\" && exit 1"
   },
   "keywords": [],
```


运行 `npm run lint` 开始自动修复我们的 src 文件里的代码格式
```bash
$ npm run lint

> demo@1.0.0 lint /Users/root1/Desktop/demo
> eslint --fix --ext .js --ext .jsx --ext .ts --ext .tsx src/ 


/Users/root1/Desktop/demo/src/app.tsx
  1:8   error  'React' is defined but never used           no-unused-vars
  1:16  error  'FC' is defined but never used              no-unused-vars
  4:7   error  'Hello' is assigned a value but never used  no-unused-vars

✖ 3 problems (3 errors, 0 warnings)

npm ERR! code ELIFECYCLE
npm ERR! errno 1
npm ERR! demo@1.0.0 lint: `eslint --fix --ext .js --ext .jsx --ext .ts --ext .tsx src/ `
npm ERR! Exit status 1
npm ERR! 
npm ERR! Failed at the demo@1.0.0 lint script.
npm ERR! This is probably not a problem with npm. There is likely additional logging output above.

npm ERR! A complete log of this run can be found in:
npm ERR!     /Users/root1/.npm/_logs/2020-06-21T07_23_07_803Z-debug.log
```


可知道，fix 失败，这说明，有些错误，是不能通过此命令为我们自动修复的，我们必须手动修复或者忽略掉这个约束规则。


从报错，我们可知，是因为我们的代码不符合 `no-unused-vars` 这个规则，那么我们可以去掉这个约束或者修改我们的代码。


分析代码可以，这几个变量 我们都不能去掉，否则代码就不能正常运行了，那么我们只需要在 `.eslintrc.js` 文件里面的 rules 里加一行规则就行了，忽略掉这个规则
![image.png](https://cdn.nlark.com/yuque/0/2020/png/204954/1592724487373-7f3f49e1-376e-43f4-8d5b-465a2cd8e5f3.png#align=left&display=inline&height=537&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1074&originWidth=1484&size=213478&status=done&style=none&width=742)
再次运行 `npm run lint` 


```bash
$ npm run lint

> demo@1.0.0 lint /Users/root1/Desktop/demo
> eslint --fix --ext .js --ext .jsx --ext .ts --ext .tsx src/ 
```
发现，没有报错，根据 Unix 的 no news is good news 的设计哲学，我们的代码全部符合 eslint 的规则。
![image.png](https://cdn.nlark.com/yuque/0/2020/png/204954/1592724774330-43e7b913-6537-441e-90f8-03e685fcbc11.png#align=left&display=inline&height=362&margin=%5Bobject%20Object%5D&name=image.png&originHeight=362&originWidth=474&size=357960&status=done&style=shadow&width=474)


到此，我们的目标基本实现，最简洁的 typescript + react + webpack + eslint 开发环境已然搭建完成了。


最后来看下我们的项目目录：
![image.png](https://cdn.nlark.com/yuque/0/2020/png/204954/1592724876937-21255cdc-b4f0-4e26-ab7b-815761989a6b.png#align=left&display=inline&height=596&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1192&originWidth=886&size=112086&status=done&style=none&width=443)
## 5. 可选 webpack 插件


在实际项目中，只是添加以上一些模块是不够的，因为我们暂时还无法处理图片、css 等资源，为了让我们的开发环境更完善点，我们接下来需要朝项目中添加一些 webpack loader，让我们的开发环境更加的强大一些。


### 处理图片的 loader
添加 file loader
```bash
npm install --save-dev file-loader
```
修改 `webpack.common.js` 
```diff
         ],
       },
       {enforce: 'pre', test: /\.js$/, loader: 'source-map-loader'},
+      {
+        test: /\.(png|jpg|gif)$/,
+        use: [
+          {
+            loader: 'file-loader',
+            options: {},
+          },
+        ],
+      },
     ],
   },
 };
```


然后我们尝试着，朝项目中引入一个 logo 图片。
![image.png](https://cdn.nlark.com/yuque/0/2020/png/204954/1592725629915-a5afd8fa-a2df-4ac5-8500-b6e4ee644e2d.png#align=left&display=inline&height=422&margin=%5Bobject%20Object%5D&name=image.png&originHeight=844&originWidth=610&size=69298&status=done&style=none&width=305)


然后改一下 `app.tsx` 里面的代码：
```diff
 import React, {FC} from 'react';
 import ReactDom from 'react-dom';
+import logo from './logo.jpg';
 
 const Hello: FC<{title: string}> = ({title}) => {
-  return <h1>{title}</h1>;
+  return <h1><img src={logo} />{title}</h1>;
 };
 
 ReactDom.render(
```


然后准备，运行的时候，却发现运行不了
![image.png](https://cdn.nlark.com/yuque/0/2020/png/204954/1592725725042-b80c2882-738c-43d6-bc21-c9011027bc77.png#align=left&display=inline&height=116&margin=%5Bobject%20Object%5D&name=image.png&originHeight=232&originWidth=1208&size=33451&status=done&style=none&width=604)


别慌，原来是我们在 typescript 中引入图片的时候，需要自己定义以下其类型声明。


我们创建一个 typings 文件夹，然后新建一个 `custom.d.ts`  文件，自定义自己的类型声明。
```bash
mkdir typings && touch typings/custom.d.ts
```


`custom.d.ts`  内容
```typescript
declare module '*.png' {
  const content: any;
  export = content;
}

declare module '*.jpg' {
  const content: any;
  export = content;
}
```


写到这里，还没完，还需要改下 `tsconfig.json` ，让我们这个文件可以正常加载出来。
```diff
       "esModuleInterop": true,
   },
   "include": [
-      "./src/**/*"
+      "./src/**/*",
+      "typings/*"
   ],
   "exclude": [
     "dist",
```
改完以后，重新 `npm start` 一下：


![image.png](https://cdn.nlark.com/yuque/0/2020/png/204954/1592726095913-10998524-a14c-41f7-8d79-f42a25e22a13.png#align=left&display=inline&height=452&margin=%5Bobject%20Object%5D&name=image.png&originHeight=904&originWidth=2194&size=543858&status=done&style=none&width=1097)


再次打开页面，可以看到，我们的 logo 也加载出来了。


### 处理 css 的 loader


添加 style-loader 和 css-loader


```bash
npm install --save-dev style-loader css-loader
```


修改 `webpack.common.js`
```diff
         ],
       },
       {enforce: 'pre', test: /\.js$/, loader: 'source-map-loader'},
+      {
+        test: /\.css$/,
+        use: [
+          'style-loader',
+          {loader: 'css-loader', options: {url: false}},
+        ],
+      },
       {
         test: /\.(png|jpg|gif)$/,
         use: [
```


添加一个 `app.css`  文件
![image.png](https://cdn.nlark.com/yuque/0/2020/png/204954/1592726921867-ebe241f4-9da9-4fb7-b6ec-bcdb75138911.png#align=left&display=inline&height=315&margin=%5Bobject%20Object%5D&name=image.png&originHeight=630&originWidth=1396&size=89418&status=done&style=none&width=698)


在 `app.tsx` 里 import 一下
```diff
 import React, {FC} from 'react';
 import ReactDom from 'react-dom';
 import logo from './logo.jpg';
+import './app.css';
 
 const Hello: FC<{title: string}> = ({title}) => {
   return <h1><img src={logo} />{title}</h1>;
```
重新 `npm start` 一下
![image.png](https://cdn.nlark.com/yuque/0/2020/png/204954/1592726906987-5304b3cd-3699-4430-9cee-330b2a243b63.png#align=left&display=inline&height=165&margin=%5Bobject%20Object%5D&name=image.png&originHeight=330&originWidth=1090&size=39930&status=done&style=none&width=545)
打开页面，发现我们的 css 样式应用上去了。


### 其它有用的 loader


webpack 提供了很多 loader，处理各种不同的情景，如果你还有更多的需求，请参考页面：[https://webpack.js.org/loaders/](https://webpack.js.org/loaders/)


## 6. 仓库地址

如果你想直接用我配置好的仓库，请访问地址：[https://github.com/lovefengruoqing/demo](https://github.com/lovefengruoqing/demo)，克隆或者下载下来以后，修改下 `package.json`  里面的信息即可使用。
