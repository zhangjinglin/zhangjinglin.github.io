# (FMS03)Vim 的插件


Vim 除了强大的键盘模式以外, Vim 还有插件功能, 可以通过插件将 Vim 的功能增强, 经过多年的发展, Vim 的插件系统有着庞大的规模, 有很多号称神级的插件, 是很多编辑器的模仿对象.

## 怎么管理插件

插件的安装一般通过插件管理器来进行安装. Vim 的插件管理器有很多种, 我采用的是[pathogen](https://github.com/tpope/vim-pathogen) 管理, 你也可以采用其他的管理方式, 比如常见的 vbundle, 请自行 google 之.

在pathogen 的主页上可以学习到安装方式.

```sh
mkdir -p ~/.vim/autoload ~/.vim/bundle && \
curl -LSso ~/.vim/autoload/pathogen.vim https://tpo.pe/pathogen.vim
```



进入到新建的 `bundle` 目录, 下载相应的插件. 下面列出来我用的一些常用的插件

```plain
" 自动关闭括号, 也就是输入左括号(, 然后自动添加右括号), 支持{} [] ()
https://github.com/vim-scripts/AutoClose.git

" 迅速打开当前目录下的所有文件, 支持模糊查找, 按下 ctrl+p 组合件之后, 输入要查找的文件名的一部分, 快速定位, 回车打开, vim 神器之一
https://github.com/kien/ctrlp.vim.git

" html 神器之一, 其他编辑器也有相应的实现
https://github.com/mattn/emmet-vim.git

" 自动完成插件, 需要 lua 支持, 记得我们在安装 vim 的时候增加了 --with-lua 的选项了么? 在 Windows 平台的 vim 中不支持 lua, 需要自行编译 vim 增加 lua 支持, Windows 充满了大坑啊! MacOS 你不变的选择.
https://github.com/Shougo/neocomplete.vim.git

" 缩进美化, 对不同层级的缩进增加可视化竖条
https://github.com/nathanaelkane/vim-indent-guides.git

" vim 神器之一, 快速定位到你需要的位置
https://github.com/easymotion/vim-easymotion.git

" 美化 vim 的状态条
https://github.com/Lokaltog/vim-powerline.git

" solarized 配色, 最流行的一种配色
https://github.com/altercation/vim-colors-solarized.git

" vim 的侧边文件管理器, 不过我用了 ctrl+p 之后, 很少用到它
https://github.com/scrooloose/nerdtree.git
```





## 怎么安装

我举一个例子, 比如 `vim-colors-solarized` 来说明.

```plain
cd ~/.vim/bundle
git clone --depth 1 https://github.com/altercation/vim-colors-solarized.git
```



ok, 大功告成. 不过一般的情况下我们还需要在 `.vimrc` 中进行配置, 很多插件有很多的配置, 请自行在每个插件的网址中详细查看.

附上我的完整 `.vimrc` 配置.

```plain

let mapleader=";"

nmap <Leader>h 0
nmap <Leader>l $
vnoremap <Leader>y "+y
nmap <Leader>p "+p
nmap <Leader>q :q<CR>

" 在 vim 中, :vs 垂直分隔一个窗口, :sp(lit) 水平分割一个窗口, 然后使用下面四个映射命令, 在多个窗口中移动.
nmap <Leader>wh <C-W>h
nmap <Leader>wl <C-W>l
nmap <Leader>wj <C-W>j
nmap <Leader>wk <C-W>k

function! CBProcess()
    if (getline(".")[col(".")-2] == '{' && getline(".")[col(".")-1] == '}') || (getline(".")[col(".")-2] == '[' && getline(".")[col(".")-1] == ']') || (getline(".")[col(".")-2] == '(' && getline(".")[col(".")-1] == ')')
        return "\<CR>\<C-o>O"
    else
        return "\<CR>"
    endif
endfunction

inoremap <expr><CR> CBProcess()
inoremap ;; <C-o>$;<CR>

" 映射启动 NERDTreeToggle 插件的快捷键
map <Leader>n :NERDTreeToggle<CR>

" 为了让插件生效, 必须配置插件管理器
execute pathogen#infect()

filetype plugin indent on

syntax on
syntax enable


set autowrite
set autowriteall
set ignorecase
set hlsearch
set incsearch
set smartcase
set gdefault
set showmatch

set autoindent
set ts=4 sts=4 sw=4 expandtab

set backspace=indent,eol,start
set noswapfile
set visualbell
set cursorline
set number
set laststatus=2
set ruler

set encoding=utf-8
set fileencoding=utf-8

" 设置背景颜色, 采用亮色的 solarized, 也可以设置为 dark 采用暗色调
set background=light
colorscheme solarized
set guifont=Menlo:h20

" 设置 indent guides 插件的参数
let g:indent_guides_enable_on_vim_startup=1
let g:indent_guides_start_level=1
let g:indent_guides_guide_size=1

" 设置 neocomplete
let g:neocomplete#enable_at_startup=1
let g:neocomplete#enable_smart_case=1

" 设置 emmet 的展开方式快捷键
inoremap <expr><TAB>  pumvisible() ? "\<C-n>" : "\<TAB>"
autocmd FileType css setlocal omnifunc=csscomplete#CompleteCSS
autocmd FileType html,markdown setlocal omnifunc=htmlcomplete#CompleteTags
autocmd FileType javascript setlocal omnifunc=javascriptcomplete#CompleteJS
autocmd FileType python setlocal omnifunc=pythoncomplete#Complete
autocmd FileType xml setlocal omnifunc=xmlcomplete#CompleteTags

" 设置 easymotion 是否区分大小写
let g:EasyMotion_smartcase=1
```



有问题, 请联系我.


---

> 作者: [张景林](https://zhangjinglin.github.io/about/)  
> URL: https://zhangjinglin.github.io/2016/12/fms03-vim02-md/  

