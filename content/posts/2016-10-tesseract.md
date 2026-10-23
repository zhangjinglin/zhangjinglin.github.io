---
title: "2016年第43周最火的Github项目 - Tesseract.js"
date: 2016-10-22T05:17:03+00:00
url: "/2016/10/tesseract/"
draft: false
---

[项目地址](https://github.com/naptha/tesseract.js)
[官方网址](http://tesseract.projectnaptha.com)

> 完全Javascript实现的OCR，能够识别62中语言。
> 截止写作时，本周共收获3146个star。

> 好吧，我承认截止写作时，本周最火的项目不是Tesseract.js，而是另一个项目[Deep Learning Papers Reading Roadmap](https://github.com/songrotek/Deep-Learning-Papers-Reading-Roadmap)，但是本人还没有关注深度学习，所以，你懂得~~~
> 截止写作时，本周共收获3169个star。

### 什么东东?

Tesseract.js 是一个[Tesseract OCR引擎](https://github.com/tesseract-ocr/tesseract)的javascript移植。这个库支持超过60种语言（包括中文），能够自动识别文本方向和笔迹检测。
可以运行在浏览器和NodeJS中。

### 怎么安装？

- 浏览器

```html
<script src='https://cdn.rawgit.com/naptha/tesseract.js/1.0.10/dist/tesseract.js'>
</script>
```




- NodeJS

```plain
yarn add tesseract.js
//or
npm install tesseract.js --save
```





### 基础案例

新建一个项目，进行初始化

```plain
mkdir tesseract-demo
cd tesseract-demo
yarn init
yarn add tesseract.js
touch index.js
```



我们先来找一个中文页面，进行截图，将图像放置到项目目录中。我截取了这样一个图像。

![](/2016/10/tesseract/image.png)
下面来进行index.js的代码：

```js
const Tesseract = require('tesseract.js')

Tesseract.recognize('./image.png', 'chi_sim') //第二个参数表示语言，缺省的话为eng，英文
  .progress((p) => console.log('progress', p))
  .then((result) => console.log('result', result))
```



运行：

```plain
node index.js
```



首次运行会先下载`chi_sim.traineddata`识别库，然后进行识别，需要等待一下，第二次则不需要下载了。

识别结果

```plain
result { text: '截取任慧窗口: 快捷键 【Sh…+command+4)\n入直浸按“Shm+command+4“快捷锂组台、 出现十字架的坐标图标;\n入拖动坐标图标, 选取任慧区域后释放鼠标, 图片会自动保存在桌面恩\n\n',
//后面省略
```



识别结果中有错误，但还是可以接受的。Tesseract.js还有很多的参数可以配置，查看项目文档即可。
