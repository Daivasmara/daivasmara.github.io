---
title: My .vimrc
date: 2020-04-19 18:37:06
categories:
  - Article
tags:
  - Vim
mp3:
cover: 'https://upload.wikimedia.org/wikipedia/commons/9/9f/Vimlogo.svg'
---
![My Vim](https://i.imgur.com/jQtmpW5.png)

Okay so first of all, a friendly disclaimer: <ins>**I'm not a vim expert by any means.**</ins> This is just a fun, opiniated/subjective article on what plugins and settings I'm using on my vim and why. Another disclaimer is that this **.vimrc** is far from done and will keep updated every time I feel like it or in some cases **needed** to.

## Introduction
In order to for you to get a better context, this vim setup is used daily by me a **full time javascript engineer** who's currently doing some **python** and **rust** on the side.

## .vimrc
```vim
call plug#begin('~/.vim/plugged')

Plug 'challenger-deep-theme/vim', { 'as': 'challenger-deep' }
Plug 'junegunn/fzf', { 'dir': '~/.fzf', 'do': './install --all' }
Plug 'junegunn/fzf.vim'
Plug 'neoclide/coc.nvim', {'branch': 'release'}
Plug 'davidhalter/jedi-vim'
Plug 'vim-airline/vim-airline'
Plug 'vim-airline/vim-airline-themes'
Plug 'junegunn/goyo.vim'
Plug 'vimwiki/vimwiki'
Plug 'voldikss/vim-floaterm'
Plug 'preservim/nerdtree'
Plug 'preservim/nerdcommenter'
Plug 'ryanoasis/vim-devicons'
Plug 'iamcco/markdown-preview.nvim', { 'do': 'cd app & yarn install'  }
Plug 'airblade/vim-gitgutter'
Plug 'dense-analysis/ale'
Plug 'tpope/vim-fugitive'
Plug 'alvan/vim-closetag'
Plug 'tpope/vim-surround'
Plug 'jiangmiao/auto-pairs'
Plug 'ap/vim-css-color'
Plug 'ekalinin/Dockerfile.vim'
Plug 'rust-lang/rust.vim'
Plug 'plasticboy/vim-markdown'
Plug 'stephpy/vim-yaml'
Plug 'mxw/vim-jsx'
Plug 'pangloss/vim-javascript'
Plug 'neoclide/jsonc.vim'
Plug 'leafgarland/typescript-vim'
Plug 'numirias/semshi', {'do': ':UpdateRemotePlugins'}

call plug#end()

let mapleader = ","
syntax on
set encoding=UTF-8
set tabstop=8 softtabstop=0 expandtab shiftwidth=2 smarttab
set number relativenumber
set laststatus=2
set noshowmode
set updatetime=250
set nocompatible
filetype plugin on

noremap <Leader>Y "+y
noremap <Leader>P "+p
nnoremap gb <C-O><C-S>
nnoremap <C-J> <C-W><C-J>
nnoremap <C-K> <C-W><C-K>
nnoremap <C-L> <C-W><C-L>
nnoremap <C-H> <C-W><C-H>
nnoremap <C-E> <C-W><C-V>
nnoremap <C-O> <C-W><C-S>

autocmd FileType json syntax match Comment +\/\/.\+$+
autocmd BufRead,BufNewFile tsconfig.json set filetype=jsonc

let g:python3_host_prog = '/usr/bin/python3.8'
let g:python_host_prog = '/usr/bin/python2.7'
let g:loaded_ruby_provider = 0
let g:floaterm_keymap_toggle = '<Leader>`'
let g:closetag_filenames = '*.html,*.xhtml,*.phtml,*js,*jsx'
let g:ale_linters_explicit = 1
let g:gitgutter_realtime = 1
let g:rustfmt_autosave = 1
let g:vim_markdown_folding_disabled = 1
let g:typescript_indent_disable = 1
let g:typescript_compiler_binary = 'npx tsc'

"FZF RG
nnoremap <silent> <C-p> :FZF<CR>
function! RipgrepFzf(query, fullscreen)
  let command_fmt = 'rg --column --line-number --no-heading --color=always --smart-case %s || true'
  let initial_command = printf(command_fmt, shellescape(a:query))
  let reload_command = printf(command_fmt, '{q}')
  let spec = {'options': ['--phony', '--query', a:query, '--bind', 'change:reload:'.reload_command]}
  call fzf#vim#grep(initial_command, 1, fzf#vim#with_preview(spec), a:fullscreen)
endfunction
command! -nargs=* -bang Rg call RipgrepFzf(<q-args>, <bang>0)

"Challenger Deep
let g:challenger_deep_termcolors = 16
if has('nvim') || has('termguicolors')
  set termguicolors
endif
colorscheme challenger_deep

"Vim-Airline
let g:airline_theme='deus'
let g:airline_powerline_fonts = 1
let g:airline#extensions#hunks#enabled = 0

"CoC
set hidden
set nobackup
set nowritebackup
set cmdheight=2
set shortmess+=c
set signcolumn=yes
set statusline^=%{coc#status()}%{get(b:,'coc_current_function','')}

let g:coc_user_config = 1
let g:coc_global_extensions = 1

nmap <silent> [g <Plug>(coc-diagnostic-prev)
nmap <silent> ]g <Plug>(coc-diagnostic-next)
nmap <silent> gd <Plug>(coc-definition)
nmap <silent> gy <Plug>(coc-type-definition)
nmap <silent> gi <Plug>(coc-implementation)
nmap <silent> gr <Plug>(coc-references)
nmap <leader>rn <Plug>(coc-rename)
nnoremap <silent> <space>a  :<C-u>CocList diagnostics<cr>
nnoremap <silent> <space>e  :<C-u>CocList extensions<cr>
nnoremap <silent> <space>c  :<C-u>CocList commands<cr>
nnoremap <silent> <space>o  :<C-u>CocList outline<cr>
nnoremap <silent> <space>s  :<C-u>CocList -I symbols<cr>
nnoremap <silent> <space>j  :<C-u>CocNext<CR>
nnoremap <silent> <space>k  :<C-u>CocPrev<CR>
nnoremap <silent> <space>p  :<C-u>CocListResume<CR>
nnoremap <silent> K :call <SID>show_documentation()<CR>
inoremap <silent><expr> <TAB>
      \ pumvisible() ? "\<C-n>" :
      \ <SID>check_back_space() ? "\<TAB>" :
      \ coc#refresh()
inoremap <expr><S-TAB> pumvisible() ? "\<C-p>" : "\<C-h>"
inoremap <silent><expr> <c-space> coc#refresh()

xmap <leader>a  <Plug>(coc-codeaction-selected)
nmap <leader>a  <Plug>(coc-codeaction-selected)
nmap <leader>ac  <Plug>(coc-codeaction)
nmap <leader>qf  <Plug>(coc-fix-current)
xmap if <Plug>(coc-funcobj-i)
xmap af <Plug>(coc-funcobj-a)
omap if <Plug>(coc-funcobj-i)
omap af <Plug>(coc-funcobj-a)
nmap <silent> <TAB> <Plug>(coc-range-select)
xmap <silent> <TAB> <Plug>(coc-range-select)
xmap <leader>f  <Plug>(coc-format-selected)
nmap <leader>f  <Plug>(coc-format-selected)

command! -nargs=0 Format :call CocAction('format')
command! -nargs=? Fold :call     CocAction('fold', <f-args>)
command! -nargs=0 OR   :call     CocAction('runCommand', 'editor.action.organizeImport')
autocmd CursorHold * silent call CocActionAsync('highlight')

function! s:show_documentation()
  if (index(['vim','help'], &filetype) >= 0)
    execute 'h '.expand('<cword>')
  else
    call CocAction('doHover')
  endif
endfunction

function! s:check_back_space() abort
  let col = col('.') - 1
  return !col || getline('.')[col - 1]  =~# '\s'
endfunction
if has('patch8.1.1068')
  inoremap <expr> <cr> complete_info()["selected"] != "-1" ? "\<C-y>" : "\<C-g>u\<CR>"
else
  imap <expr> <cr> pumvisible() ? "\<C-y>" : "\<C-g>u\<CR>"
endif

augroup mygroup
  autocmd!
  autocmd FileType typescript,json setl formatexpr=CocAction('formatSelected')
  autocmd User CocJumpPlaceholder call CocActionAsync('showSignatureHelp')
augroup end

"Goyo
let g:goyo_width = 150
nnoremap <Leader>gy :Goyo<CR>
function! s:goyo_enter()
  set number relativenumber
endfunction
autocmd! User GoyoEnter nested call <SID>goyo_enter()

"NERDTree
nnoremap <silent> <C-n> :NERDTreeToggle<CR>
autocmd bufenter * if (winnr("$") == 1 && exists("b:NERDTree") && b:NERDTree.isTabTree()) | q | endif
let g:NERDTreeDirArrowExpandable = ''
let g:NERDTreeDirArrowCollapsible = ''

"Jedi
let g:jedi#goto_definitions_command = "gd"
let g:jedi#rename_command = "<leader>rn"
```

## Plugins Justification

### Theme
```vim
Plug 'challenger-deep-theme/vim', { 'as': 'challenger-deep' }

"Challenger Deep
let g:challenger_deep_termcolors = 16
if has('nvim') || has('termguicolors')
  set termguicolors
endif
colorscheme challenger_deep
```
For theme I'm using [Challenger Deep](https://github.com/challenger-deep-theme/vim). I can't really say much other than I love the color scheme it provides. It is so me.

### Fuzzy Search
![Fuzzy Search using FZF + RipGrep](https://i.imgur.com/LCL7wl2.png)

**.vimrc**
```vim
Plug 'junegunn/fzf', { 'dir': '~/.fzf', 'do': './install --all' }
Plug 'junegunn/fzf.vim'

"FZF RG
nnoremap <silent> <C-p> :FZF<CR>
function! RipgrepFzf(query, fullscreen)
  let command_fmt = 'rg --column --line-number --no-heading --color=always --smart-case %s || true'
  let initial_command = printf(command_fmt, shellescape(a:query))
  let reload_command = printf(command_fmt, '{q}')
  let spec = {'options': ['--phony', '--query', a:query, '--bind', 'change:reload:'.reload_command]}
  call fzf#vim#grep(initial_command, 1, fzf#vim#with_preview(spec), a:fullscreen)
endfunction
command! -nargs=* -bang Rg call RipgrepFzf(<q-args>, <bang>0)
```

**.zshrc / .bshrc**
```zsh
[ -f ~/.fzf.zsh ] && source ~/.fzf.zsh

export FZF_DEFAULT_COMMAND='rg --files --no-ignore --hidden --follow --glob "!.git/*"'
```

So for fuzzy search I'm using [FZF](https://github.com/junegunn/fzf.vim) combined with [RipGrep](https://github.com/BurntSushi/ripgrep), main reason: **Performance**. RipGrep is another *grep* which claimed by many people as the fastest *grep* there is. It's written in rust, which is another reason why I like it. This setup above let me use `:Rg text` to fuzzy find, and yes as it's claimed, it's blazingly fast even in extremly heavy code base. It also provides a peeking window.

### Terminal
![Opened FloaTerm](https://i.imgur.com/C6WOQHW.png)

```vim
Plug 'voldikss/vim-floaterm'

"FloaTerm
let g:floaterm_keymap_toggle = '<Leader>`' 
```

For terminal I'm using [FloaTerm](https://github.com/voldikss/vim-floaterm), I was initially planning on not using any terminal emulator in vim since it can be done easily by doing `!` command, but when I saw voldikss' post about this on reddit it got me hooked. First of all its aesthethic and performace is top notch. Second it's so easy to use, not much settings needed, the only custom thing I changed is the keymap.

### File Explorer
![Opened NERDTree](https://i.imgur.com/Boxo0UA.png)

```vim
Plug 'preservim/nerdtree'
Plug 'ryanoasis/vim-devicons'

"NERDTree
nnoremap <silent> <C-n> :NERDTreeToggle<CR>
autocmd bufenter * if (winnr("$") == 1 && exists("b:NERDTree") && b:NERDTree.isTabTree()) | q | endif
let g:NERDTreeDirArrowExpandable = ''
let g:NERDTreeDirArrowCollapsible = ''
```

[NERDTree](https://github.com/preservim/nerdtree) is I think one of the everyone's go-to vim plugin just cause how necessary it is. Above config lets NERDTree to close when all files are closed and also remove the default *expandable* and *collapsible* symbol. Combined with [devicons](https://github.com/ryanoasis/vim-devicons) it will give you nice file icons. **Note**: you have to use [Nerd font compatible](https://github.com/ryanoasis/nerd-fonts#patched-fonts) for the icons to show.

## TO BE CONTINUED WHEN I HAVE TIME, SORRY
