# (FMS01)Homebrew - macOS 不可或缺的套件管理器


macOS 的内核基于 unix 的变种 FreeBSD 来进行开发的, 在内核上面配有苹果公司漂亮的图形界面. 因为 macOS 是基于 unix 变种的, 所以 macOS 在程序员中特别流行. 近年来, 新的软件技术和严肃的程序开发, 大部分在 unix 中或者类 unix 的 Linux 中完成, 而 macOS 天生的 unix 基因, 使得 macOS 也得到了很多便利. 但 macOS 毕竟不是 Linux, 使得很多 Linux 下的优秀软件没有在 macOS 下进行实现, 而 Homebrew 则填补了这方面的空白.

[Homebrew](http://brew.sh/) 是基于 Ruby 语言进行开发的. 你可以再 [Github](https://github.com/Homebrew/brew) 上面查看它的全部源代码.

Homebrew 的使用非常简单, 首先你要确认你安装了 Xcode, 因为 Homebrew 通过 git 获取你要安装的软件包, 然后通过 clang(gcc 的 Apple 实现) 进行编译和安装, 安装的软件都放置在 `/usr/local/Cella` 中, 并在 `/usr/local/bin` 中做了执行文件的链接程序.

### 安装 Homebrew

```sh
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```





### 怎么使用

- brew search 软件名称 (在 Homebrew 中称为 Formula)
- brew update 更细 brew 自身和相应的包版本库
- brew upgrade 升级已经安装的包
- brew install $FORMULA 安装软件包

其他详细信息请看 [FAQ](https://github.com/Homebrew/brew/blob/master/docs/FAQ.md)


---

> 作者: [张景林](https://zhangjinglin.github.io/about/)  
> URL: https://zhangjinglin.github.io/2016/12/FMS01-brew/  

