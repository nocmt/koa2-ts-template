> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [segmentfault.com](https://segmentfault.com/a/1190000041372990)

> 此脚手架优势完整的开发环境，nodejs+git+typeScript+ESLint+Prettier+etc 在 nodejs 中使用 typeScript 在 nodejs 中使用 import 语法更改代码后自动重......

## 此脚手架优势

- 完整的开发环境，nodejs+git+typeScript+ESLint+Prettier+etc
- 在 nodejs 中使用 typeScript
- 在 nodejs 中使用 import 语法
- 更改代码后自动重启 node 服务器
- 自动编译打包为 ts 为 js 文件
- 以 koa2 为例构建
- 可以接入任何你喜欢的 nodejs 框架（expess,koa...）

## 事前准备

#### 0. 构建环境

确定已经安装了 node.js，git 以及 typeScript  
因为本教程使用 yarn 构建，所以还需要安装 yarn，或者使用相对的 npm 命令

检查 node 版本

```
node -v
→ v16.3.0
```

检测 tsc 版本（使用 npx 命令）

```
npx tsc --version
→ v4.4.4
```

os: windows  
编辑器： vs code

## 搭建 nodejs 开发环境

#### 1. 新建文件夹并打开

```
mkdir node
cd node
```

#### 2. 初始化 git

```
git init
```

新建. gitignore，设置 git 忽略文件  
内容根据自己的喜好

```
node_modules
.DS_Store
dist
*.local
*.code-workspace
.vscode
```

#### 3. 初始化 editorconfig

新建. editorconfig，设置编辑器和 ide 规范  
内容根据自己的喜好或者团队规范

```
root = true

[*.{js,ts,json}]
indent_size = 2
charset = utf-8
indent_style = space
trim_trailing_whitespace = true
insert_final_newline = true
```

#### 4. 初始化 package.json

```
yarn init
```

项目设定根据实际情况填写即可

```
{
  "name": "node",
  "version": "1.0.0",
  "main": "index.js",
  "license": "MIT"
}
```

一般到这一步，最基本的 nodejs 开发环境就搭建完了  
下一步开始追加 typescript

## nodejs+typescript 开发环境搭建

#### 5.typescript 依赖安装

安装 typeScript 的基础依赖包

```
yarn add -D typescript @types/node
```

#### 6. 初始化 tsconfig.json

```
npx tsc --init
```

初始化生成的 tsconfig.json 含有许多备注  
如果嫌麻烦可以直接使用下面的模板  
项目设定根据实际情况填写即可

```
{
  "compilerOptions": {
    "target": "esnext",
    "module": "commonjs",
    "sourceMap": true,
    "outDir": "./dist",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true
  },
  "include": ["src"],
  "exclude": ["node_modules", "dist", "public"]
}
```

这里需要注意一下  
outDir: 是编译后文件放置的文件夹  
include: 是需要检查哪的文件来编译  
exclude: 是不需要检查哪的文件来编译

#### 7. 确认 typescript 环境

新建文件夹 src

```
mkdir src
```

并且在 src 内新建 index.ts  
下面是 index.ts，极其极其简单的 typescript 语句

```
const msg: string = 'Hello World'
console.log(msg)
```

编译 typescript

```
npx tsc
```

成功的话，这时会在 dist 文件内生成一个 index.js，内容如下  
很显然，ts 变成了 js

```
"use strict";
const msg = 'Hello World';
console.log(msg);
//# sourceMappingURL=app.js.map
```

使用 node 命令执行 index.js

```
node dist/index.js
→ Hello World
```

## 开发执行脚本配置

#### 8. 安装依赖

安装 ts-node-dev 和 npm-run-all

```
yarn add -D ts-node-dev npm-run-all
```

ts-node-dev：开发用依赖，开发时自动编译 ts 为 js 并重启 node 服务器  
npm-run-all：一个命令来执行一个以上的脚本命令

#### 9. 配置 package.json 脚本

```
"main": "dist/index.js",

  ...省略

  "scripts": {
    "dev": "ts-node-dev --respawn src/index.ts",
    "resetFolder": "rimraf dist/*",
    "compile": "tsc",
    "build": "npm-run-all resetFolder compile",
    "start": "node ."
  },
```

dev：开发的时候用，运行 node 服务器，伴随代码更改自动重启 node 服务器,--respawn 是命令选项，自动重启，必须  
resetFolder：清空 dist 文件夹  
compile：编译 typescript  
build：清理 dist 文件夹并打包  
start：运行 nodejs 脚本

到这一步，最基本的 typescript+nodejs 开发环境就搭建完了  
可以试着运行一下上面的命令

## ESLint + Prettier

#### 10. 初始化并配置 eslint

安装 eslint 依赖

```
yarn add -D eslint typescript @typescript-eslint/parser @typescript-eslint/eslint-plugin
```

新建. eslintrc.js 文件并写入配置  
也可以使用 eslint --init 生成，但这里方便起见直接复制粘贴了

```
module.exports = {
  root: true,

  env: {
    node: true,
    es2021: true,
  },

  parser: '@typescript-eslint/parser',

  parserOptions: {
    ecmaVersion: 12,
    sourceType: 'module',
    tsconfigRootDir: __dirname,
    project: ['./tsconfig.json'],
  },

  plugins: ['@typescript-eslint'],

  extends: [
    'eslint:recommended',
    'plugin:@typescript-eslint/recommended',
    'plugin:@typescript-eslint/recommended-requiring-type-checking',
  ],
}
```

导入了 eslint 后，.eslintrc.js 会出现如下报错

```
Parsing error: "parserOptions.project" has been set for @typescript-eslint/parser.
```

这是由于 eslint 检查和 ts 检查配置造成的，[详见这里](https://link.segmentfault.com/?enc=hw6Pw8%2BrSTwFGSktkK48jw%3D%3D.Y5tvu3QmIWb7lrxAp7UJ7sLiDvZ79wEZNoPCzh%2BPVAFIhmFLcIMPJ2Oylcdtu4l0ArhJ%2B3MwQ1wya75NxcE346hjGsK4mWLZrMmWzSWioPtlS53xv%2B27agWt9er0sLbndWWq7g%2FjWJKusRhCK6OjqQ%3D%3D)  
为了解决问题，在 tsconfig.json 中加入 ".eslintrc.js"

```
...省略

    "include": [ ".eslintrc.js","src"],

  ...省略
```

尝试执行 eslint

```
npx eslint src/index.ts
```

#### 11. 初始化并配置 prettier

prettier 依赖

```
yarn add -D prettier eslint-config-prettier
```

新建. prettierjs 文件并写入配置  
根据自己的喜好

```
module.exports = {
  semi: false,
  tabWidth: 2,
  printWidth: 120,
  proseWrap: 'preserve',
  singleQuote: true,
  trailingComma: 'all',
}
```

在. eslintrc.js 集成 prettier

```
...省略

  extends: [
    'eslint:recommended',
    'plugin:@typescript-eslint/recommended',
    'plugin:@typescript-eslint/recommended-requiring-type-checking',
    'prettier',
    'prettier/@typescript-eslint',
  ],

  rules: {
    'prettier/prettier': 'error',
  },

...省略
```

在 vs code 设置了保存并整理代码的话，适当修改并保存就可以看到 prettier 效果了  
至此，typeScript+nodejs+eslint+prettier 的开发环境就已经搭建结束了  
下面将以 koa.js 为例，实际上你也可以使用你喜欢的 nodejs 框架，比如 express.js 等

## 集成 koa.js

#### 12. 初始化 koa

```
yarn add koa
```

改写 index.ts

```
import Koa from 'koa'

const app = new Koa()
const msg: string = 'Hello World'

app.use(async (ctx: Koa.Context): Promise<void> => {
  ctx.body = msg
})

app.listen(7000)
```

此时会报一些找不到声明文件的错误，所以需要进行下一步，追加声明文件

#### 13. 安装 koa 类型声明文件

```
yarn add -D @types/koa
```

找不到声明文件的错误被成功消除  
如果今后你要使用路由等 koa 插件，也需要同时安装该插件的声明文件

#### 14. 运行

这时 koa.js 的集成也已经完成  
运行 yarn dev 并访问 [http://localhost:7000](https://link.segmentfault.com/?enc=hHiTTXGPBfFQBSlLtQDjXA%3D%3D.ud8Qu7mVc2mBwmHHzEUG1sD0udWT%2BXzDS1o8R98oni8%3D)

```
yarn dev
```

可以发现运行成功，页面上有 Hello World  
![](https://segmentfault.com/img/bVcXLab)

#### 15. 打包

运行 yarn build 会发现 dist 里面的 index.js 文件  
typeScript 已经全都被转换为（不认识的）js 文件了

```
yarn build
```

到这里 typeScript+koa2+eslint+prettier 的开发环境就已经完全搭建结束了

## 最后

附赠上完整 package.json 文件

```
{
  "name": "node",
  "version": "1.0.0",
  "main": "dist/index.js",
  "license": "MIT",

  "scripts": {
    "dev": "ts-node-dev --respawn src/index.ts",
    "resetFolder": "rimraf dist/*",
    "compile": "tsc",
    "build": "npm-run-all resetFolder compile",
    "start": "node ."
  },

  "dependencies": {
    "koa": "^2.13.4"
  },

  "devDependencies": {
    "@types/koa": "^2.13.4",
    "@types/node": "^17.0.16",
    "@typescript-eslint/eslint-plugin": "^5.11.0",
    "@typescript-eslint/parser": "^5.11.0",
    "eslint": "^8.8.0",
    "eslint-config-prettier": "^8.3.0",
    "npm-run-all": "^4.1.5",
    "prettier": "^2.5.1",
    "ts-node-dev": "^1.1.8",
    "typescript": "^4.5.5"
  }
}
```
