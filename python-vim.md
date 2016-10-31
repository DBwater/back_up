---
title: 用vim打造py开发环境
date: 2016-10-30 22:54:03
tags: [vim,python]
---

如何用vim打造一个python环境
<!--more-->
垃圾电脑打开pycharm实在是太卡了，于是就想通过配置一些vim插件来使vim更加好使用一些，现在记录下我的经历以免以后忘记，c,c++,html,其实也可以用vim来打造相应的环境。

# 插件管理器
首先需要一个用来管理vim插件的管理器

> $git clone https://github.com/VundleVim/Vundle.vim.git ~/.vim/bundle/Vundle.vim

把下面这段代码添加到.vimrc中，plugin的是你需要使用的插件，可以自行去掉。
```
" bundle配置文件
set nocompatible              " required
filetype off                  " required

" set the runtime path to include Vundle and initialize
set rtp+=~/.vim/bundle/Vundle.vim
call vundle#begin()

" alternatively, pass a path where Vundle should install plugins
"call vundle#begin('~/some/path/here')

" let Vundle manage Vundle, required
Plugin 'gmarik/Vundle.vim'
Plugin 'tmhedberg/SimpylFold' "防止折叠超过限制
Plugin 'vim-scripts/indentpython.vim' "解决自动缩进的一些小问题
Bundle 'Valloric/YouCompleteMe'
Plugin 'scrooloose/syntastic' " 每次保存文件时Vim都会检查代码的语法
Plugin 'nvie/vim-flake8' "检查代码风格
Plugin 'jnurmine/Zenburn'
Plugin 'altercation/vim-colors-solarized'
Plugin 'scrooloose/nerdtree' "树形目录
Plugin 'jistr/vim-nerdtree-tabs'
Plugin 'kien/ctrlp.vim' " cltr+p在vim中搜索文件
Plugin 'Lokaltog/powerline', {'rtp': 'powerline/bindings/vim/'} "状态栏插件,显示当前的虚拟环境、Git分支、正在编辑的文件

" Add all your plugins here (note older versions of Vundle used Bundle instead of Plugin)
" All of your Plugins must be added before the following line
call vundle#end()            " required
filetype plugin indent on    " required
```
然后输入进入vim,输入:PluginInstal
等待插件更新。
# 安装YouCompleteMe插件
YouCompleteMe现在好像只支持高版本的vim，所以你需要卸载ubuntu自带的vim装上vim8.0。
自己下载YouCompleteMe的插件，它是一个代码自动补全的插件：
进入~/.vim/bundle/YouCompleteMe目录输入

> $ git clone https://github.com/Valloric/YouCompleteMe.git

然后进入YouCompleteMe目录，添加需要的依赖库

> $ cd YouCompleteMe
> $ git submodule update --init --recursive

然后安装cmake和python-dev

> $ sudo apt-get install cmake
> $ sudo apt-get install python-dev

之后有两种方法：
1.进入家目录编译

> $ cd ~
> $ mkdir ycm_build
> $ cd ycm_build 
> $ cmake -G "Unix Makefiles" . ~/.vim/bundle/YouCompleteMe/third_party/ycmd/cpp
> $ cmake --build . --target ycm_core

2.直接在YouCompleteMe里面编译

> $ ./install.sh --clang-completer 

## 配置YouCompleteMe
然后进入.vimrc进行一些设置
```c
"设置全局配置文件的路径  
let g:ycm_global_ycm_extra_conf = '~/.vim/bundle/YouCompleteMe/cpp/ycm/.ycm_extra_conf.py'
"开启基于tag的补全，可以在这之后添加需要的标签路径  
let g:ycm_collect_identifiers_from_tags_files = 1 
"开启语义补全  
let g:ycm_seed_identifiers_with_syntax = 1  
"在接受补全后不分裂出一个窗口显示接受的项  
set completeopt-=preview  
"不显示开启vim时检查ycm_extra_conf文件的信息  
let g:ycm_confirm_extra_conf=0  
"每次重新生成匹配项，禁止缓存匹配项  
let g:ycm_cache_omnifunc=0  
"在注释中也可以补全  
let g:ycm_complete_in_comments=1  
"输入第一个字符就开始补全  
let g:ycm_min_num_of_chars_for_completion=1 
" YCM自动补全及离开插入模式后自动关闭帮助窗口
let g:ycm_autoclose_preview_window_after_completion=1
let g:ycm_autoclose_preview_window_after_insertion = 1
"YCM配合virtualenv
py << EOF
import os
import sys
if 'VIRTUAL_ENV' in os.environ:
  project_base_dir = os.environ['VIRTUAL_ENV']
  activate_this = os.path.join(project_base_dir, 'bin/activate_this.py')
  execfile(activate_this, dict(__file__=activate_this))
EOF
```
# vim其他插件的配置
## 分屏设置
将:sp和:vsp分割的窗口位置放在右下角,并更改切屏的快捷键:

```
" 将:sp和:vsp分割的窗口位置放在右下角
set splitbelow 
set splitright
" 切换工作窗口的快捷键
nnoremap <C-J> <C-W><C-J>
nnoremap <C-K> <C-W><C-K>
nnoremap <C-L> <C-W><C-L>
nnoremap <C-H> <C-W><C-H>
```
## 代码折叠设置
设置折叠代码的样式和快捷键
```
" 根据缩进折叠代码
set foldmethod=indent
set foldlevel=99
" 空格开启关闭折叠
nnoremap <space> za
" 避免代码折叠的Bug
autocmd BufWinEnter *.py setlocal foldexpr=SimpylFold(v:lnum) foldmethod=expr
autocmd BufWinLeave *.py setlocal foldexpr< foldmethod<
" 希望看到折叠代码的文档字符串
let g:SimpylFold_docstring_preview=1 
```
## 文件管理设置
```
设置NertTreeTabs
" NerdTree自动忽略某些文
let NERDTreeIgnore = ['\.pyc$', '\~$']
" 目录为最后一个窗口时不关闭
let g:nerdtree_tabs_autoclose=0
" 进入vim打开目录
let g:nerdtree_tabs_open_on_console_startup=1
```
## Debug功能
先下载flake8
> $ sudo pip install flake8

```
" 配合插件保存的时候检查语法是否出错
set statusline+=%#warningmsg#
set statusline+=%{SyntasticStatuslineFlag()}
set statusline+=%*
let g:syntastic_always_populate_loc_list = 1
let g:syntastic_auto_loc_list = 1
let g:syntastic_check_on_open = 1
let g:syntastic_check_on_wq = 0
```
## 字体,配色,状态栏
更改字体，状态栏首先需要安装Powerline，支持zsh
安装Powerline
> pip install --user powerline-status

安装Powerline字体 
> $ git clone https://github.com/powerline/fonts
> $ cd fonts/ 
> $ ./install.sh

然后进入命令行选择你ubuntu专用的字体
![](http://img.blog.csdn.net/20160730115839670)


```
" 让代码好看一点
let python_highlight_all=1
syntax on
" 两种不同配色方案在gui和shell下使用
if has('gui_running')
  set background=dark
  colorscheme solarized
else
  colorscheme zenburn
endif
```
## 编译文件
```
" 编译源文件
func! CompileCode()
	exec "w"
	if &filetype == "c"
             exec "!gcc -Wall -std=c99 %<.c -o %<"
	elseif &filetype == "cpp"
             exec "!g++ -Wall -std=c++98 %<.cpp -o %<"
	elseif &filetype == "python"
             exec "!python %<.py"
	endif
endfunc
" 运行可执行文件
func! RunCode()
	exec "w"
	if &filetype == "c" || &filetype == "cpp" || &filetype == "haskell"
             exec "! ./%<"
	elseif &filetype == "python"
             exec "!python %<.py"
 	endif
endfunc
" F5保存，编译文件
map <F5> :call CompileCode()<CR>
imap <F5> <ESC>:call CompileCode()<CR>
vmap <F5> <ESC>:call CompileCode()<CR>
" F6保存、运行
map <F6> :call RunCode()<CR>
imap <F6> <ESC>:call RunCode()<CR>
vmap <F6> <ESC>:call RunCode()<CR>
```
## 其他基本配置
```
set encoding=utf-8 " 支持UTF-8编码
set nu " 显示行号
set nobackup " 关闭备份
set mouse=a " 鼠标能用
" tab键，统一缩进为4个空格
set expandtab
set smarttab
set shiftwidth=4
set tabstop=4 
set noswapfile  " 不使用swp文件，注意，错误退出后无法恢复
" 根据缩进折叠代码
set foldmethod=indent
set foldlevel=99
vmap <C-c> "+y "复制快捷键
vmap <C-x> "+x "剪切快捷键
set backspace=indent,eol,start " 退格键不能使用的bug
```

[完整配置](https://github.com/DBwater/back_up/tree/master/vim%E5%A4%87%E4%BB%BD)
