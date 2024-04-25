### 前期准备
1. 下载 [Node.js](https://nodejs.org/en) ，我们需要用到其中的 npm 工具

### 构建项目
1. 使用合适的名字创建项目文件夹
```
mkdir you-project-name && cd you-project-name
```
2. 新建 .gitignore 并写入 node_modules
```
echo "node_modules" >> .gitignore
```
3. 新建 README
```
echo "# You Project Name" >> README.md
```
4. 上 GitHub 创建远程仓库，获得 remote url
5. Git 初始化项目，并关联 remote url
```
git init
git add . && git commit -m "Initial commit"
git remote add origin <Git Remote Url>
git push -u origin master
```
6. NPM 初始化包，生成 package.json
```
npm init -y
```
7. 添加 Typescript 开发依赖至 devDependencies
```
npm i -D typescript
```
8. 在项目根目录新建文件 tsconfig.json，内容如下:
```
{
    "compilerOptions": {
        "target": "es6",
        "module": "ES6",
        "declaration": true,
        "outDir": "./lib",
        "strict": true,
        "esModuleInterop":true,
        "allowJs":true,
        "moduleResolution":"node"
    },
    "include": [
        "src"
    ],
    "exclude": [
        "node_modules",
        "**/__tests__/*"
    ]
}
```
9. 在项目根目录创建 src 文件夹，在里面新建一个 index.ts, 此为 npm 包的入口，你可以在里面实现自己的代码，以下给出示例：
```
class PayManager {
  public pay(productId: string) {
   //code...
  }
}
export const pm = new PayManager();
```
10. 在 package.json 中添加 build script
```
"scripts": {
    ......
    "build": "tsc",
}
```
11. 控制台运行 npm run build，会在项目根目录生成一个 lib 文件夹，此即是你的包编译后的 js 代码和声明文件

![image](https://github.com/liangzcn/blog/assets/15683811/15008920-b1cb-4651-8187-177f69d261be)


12. 给 git 增加文件过滤，避免提交不必要的文件至 git 仓库
```
node_modules
/lib
```
13. 下载 tslint 工具，此为开发阶段用到, 目的是更好的规范代码格式，所以使用 -D 添加至 devDependencies
```
npm i -D prettier tslint tslint-config-prettier
```
14. 在根目录下，新建 tslint.json, 添加以下内容:
```
{
  "extends": ["tslint:recommended", "tslint-config-prettier"]
}
```
15. 在根目录下，新建 .prettierrc, 添加如下内容:
```
{
  "printWidth": 120,
  "trailingComma": "all",
  "singleQuote": true
}
```
16. 最后，在 package.json 中添加 lint 和 format script
```
"scripts": {
    ......
    "format": "prettier --write \"src/**/*.ts\" \"src/**/*.js\"",
    "lint": "tslint -p tsconfig.json"
}
```
17. 在控制台运行 lint 和 format
```
npm run lint
npm run format
```
18. 移除不必要的文件, 在 package.json 中增加白名单, 这样就只有 lib 文件夹会出现在发布的包里
```
"files": ["lib/**/*"]
```

### Jest 测试
1. 安装 jest 和 ts-jest
```
npm i -D jest ts-jest @types/jest
```
2. 配置 jest, 在根目录新建 jestconfig.json, 添加以下内容:
```
{
  "transform": {
    "^.+\\.(t|j)sx?$": "ts-jest"
  },
  "testRegex": "(/__tests__/.*|(\\.|/)(test|spec))\\.(jsx?|tsx?)$",
  "moduleFileExtensions": ["ts", "tsx", "js", "jsx", "json", "node"]
}
```
3. 移除 package.json 中的 "test" script，添加一个新的 test script
```
"scripts": {
    "test": "jest --config jestconfig.json",
    ......
}
```
4. 写一个测试用例. 在src目录下新建一个__test__文件夹, 在其中新建一个测试文件, 如: PayManager.test.ts, 内容如下：
```
import { pm } from '../index';
test('pm test', () => {
  expect(pm.pay("12321421"));
});
```
5. 执行单元测试
```
npm run test
```
![image](https://github.com/liangzcn/blog/assets/15683811/12f50d7b-aaa1-4d20-b9e8-f0f9cb38de5c)
出现PASS 提示则代表测试通过.

### 自动化
1. prepare: 会在打包和发布包之前以及本地 npm install （不带任何参数）时运行
2. prepublishOnly: 在 prepare script 之前运行，并且仅在 npm publish 运行。在这里，我们可以运行 npm run test & npm run lint 以确保我们不会发布错误的不规范的代码。
3. preversion: 在发布新版本包之前运行，为了更加确保新版本包的代码规范，我们可以在此运行 npm run lint
4. version: 在发布新版本包之后运行。如果您的包有关联远程 Git 仓库，像我们的情况一样，每次发布新版本时都会生成一个提交和一个新的版本标记，那么就可以在此添加规范代码的命令。又因为 version script 在  git commit 之前运行，所以还可以在此添加 git add。
5. postversion: 在发布新版本包之后运行，在 git commit之后运行，所以非常适合推送。
6. 最终的 package.json

![image](https://github.com/liangzcn/blog/assets/15683811/009c84ab-6b06-4f8d-a5a8-9d49f2d05c85)


### 发布
1. 发布之前需要将项目的修改提交至git
```
git add . && git commit -m "feat: xxxxx"
```
2. 发布你的第一个版本, 运行成功后，你就可以上 [npmjs](https://www.npmjs.com/) 搜索你的包了
```
npm publish
```
3. 发布更新版本
```
npm version patch
npm publish
```

OK, 大功告成~
本文极大程度借鉴下面文章，避免原文失效仅留存档供自己查看。
https://juejin.cn/post/6844903892119977998
