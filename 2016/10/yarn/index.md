# 2016年第42周最火的Github项目 - Yarn


[项目地址](https://github.com/yarnpkg/yarn)
[官方网址](https://yarnpkg.com)

## 快速、可靠、安全的依赖管理。

### 什么东东?

yarn是一个可以替代npm的包管理工具，用于管理NodeJS的包。

### 有什么优势？

- 快速：yarn把每一个package都缓存起来，下次就不需要再下载了。它还可以并行操作，比以往都快。
- 可靠：使用了一个lockfile文件，把当前的package版本确定下来，避免版本的问题。
- 安全：yarn使用checksums校验每一个包，确保正确。

### 怎么安装？

- MacOS

```plain
// brew
brew update
brew install yarn

// shell
curl -o- -L https://yarnpkg.com/install.sh | bash
```




- 其他平台：参考[官方网址](https://yarnpkg.com/en/docs/install)

### 怎么使用

基本上和npm一样，除了一些命令参数以外。

```plain
//开始新项目
yarn init // npm init

//添加包
yarn add [package] // npm install [package] --save
yarn add [package] --dev // npm install [package] --save-dev
//安装所有依赖
yarn | yarn install

//全局安装
yarn global add [package] // npm install -g [package]

//运行脚本
yarn run [script] // npm run [script]
```





### 一些技巧

- `yarn add`等同于`npm install [package]`命令，用于向项目中安装依赖的包，但是如果安装`package.json`中的全部包，等同于`npm install`的命令是`yarn install`命令。
- 如果目录中没有`package.json`的话，yarn会自动建立，每个`yarn add`命令会自动在`dependencies`段中添加依赖，不需要明确的`--save`参数。
- yarn会自动产生`yarn.lock`文件，文件中描述了依赖包的版本。


---

> 作者: [张景林](https://zhangjinglin.github.io/about/)  
> URL: https://zhangjinglin.github.io/2016/10/yarn/  

