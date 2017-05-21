title: vim_config
date: 2015-10-15 11:25:12
tags:
- vim
- config
---

> vim 配置和命令

<!--more-->

sudo保存

>:w !sudo tee %

>命令:w !{cmd}，让 vim 执行一个外部命令{cmd}，然后把当前缓冲区的内容从 stdin 传入。
>tee 是一个把 stdin 保存到文件的小工具。
>而 %，是vim当中一个只读寄存器的名字，总保存着当前编辑文件的文件路径。
>所以执行这个命令，就相当于从vim外部修改了当前编辑的文件，好完工。

必备插件
>https://github.com/bling/vim-airline
>set t_Co=256 set laststatus=2

>https://github.com/ctrlpvim/ctrlp.vim

>https://github.com/edkolev/tmuxline.vim

>https://github.com/nvie/vim-flake8

>https://github.com/Valloric/YouCompleteMe


.vimrc增加
>" make YCM compatible with UltiSnips (using supertab)
>let g:ycm_key_list_select_completion = [‘‘, ‘‘]
>let g:ycm_key_list_previous_completion = [‘‘, ‘‘]
>let g:SuperTabDefaultCompletionType = ‘‘
>“ better key bindings for UltiSnipsExpandTrigger
>let g:UltiSnipsExpandTrigger = ““
>let g:UltiSnipsJumpForwardTrigger = ““
>let g:UltiSnipsJumpBackwardTrigger = ““
>set encoding=utf-8
>set fileencodings=ucs-bom,utf-8,cp936,gb18030,latin1
>set termencoding=gb18030
>set expandtab
>set ts=4
>set shiftwidth=4
>set nu
>syntax on

>if has('mouse')
>set mouse-=a
>endif
