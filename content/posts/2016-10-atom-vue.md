---
title: "Atom下VueJS开发的配置"
date: 2016-10-15T09:51:00+00:00
url: "/2016/10/atom-vue/"
draft: false
---

VueJS中的Vue组件是以`.vue`为扩展名的，atom中不识别，atom默认认为是`Plain Text`，所以没有语法高亮等特性。

在atom中有一个名为`language-vue`的社区包，安装之后能够高亮`.vue`的文件，还有一个`template`的缩略词，能够快速生成Vue组件的结构，看似完美。

> ### 但是Emmet不好用！！！！

作为一个无法离开Emmet的人来说，没有Emmet的功能简直是回到了原始社会，无法忍受。

在google上搜索，得到解决方案，在emmet-atom中有#364的开放issue，joshuajohnson814提出了可以在`keymap.cson`中添加自定义快捷键：

```
'atom-text-editor[data-grammar~="vue"]:not([mini])':
    'tab': 'emmet:expand-abbreviation-with-tab'
```



作为临时的解决方案，但是其他人提出：

> ### CSS 不好用

也就是我没办法输入

```plain
w10[tab] -> width: 10px;
```



其实，`.vue`文件不过就是`html`文件换了个扩展名而已，我们可以直接把`.vue`登记成`.html`文件即可，也就是atom看到了`.vue`文件就用`.html`来处理即可。

## 完美解决方案

在atom中打开`config.json`文件，别告诉我你不知道怎么打开，在MacOS下，在atom菜单中。

在`core`段下面增加：

```cson
customFileTypes:
  'text.html.basic': [
    'vue'
  ]
```



这样，所有的`.vue`文件一律视为`html`，完美解决。

我得意地笑，得意地笑，~~~~

上面那段配置什么意思？给你一个网址，[atom官方手册](http://flight-manual.atom.io/using-atom/sections/basic-customization/)，好好看看`Customizing Language Recognition`这一段就明白了。
