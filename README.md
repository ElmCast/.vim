# ElmCast.vim

I have been asked a lot lately about the Vim setup I use for [ElmCast.io](http://www.elmcast.io/). My config file is about 50 lines long, and I keep the number of plugins to a minimum. Everything should work out of the box on pretty much any platform, but I will describe the exact steps needed to reproduce my day to day environment.

## Choose a Terminal

Chances are you already have a favorite terminal, and I won't try to convince you to change. To get the best experience, however, you need one with 24bit color support.

Since I am on OS X, I use the 2.9 beta of [iTerm2](http://iterm2.com/downloads.html), which works great. If you like [Tmux](https://tmux.github.io/), true color was patched in only recently. You'll need to set the `TERM` of the outer shell to a valid value, and then add a terminal override for it in Tmux.

```
$ brew install tmux --HEAD
```

```zsh
# .zshrc
export TERM="screen-256color"
```

```tmux
# .tmux.conf
set-option -ga terminal-overrides ",screen-256color:Tc"
```

## Install Vim

I prefer [neovim](https://github.com/neovim/neovim) which is just an all around better experience than vanilla Vim. Most plugins work great with it already, and can make use of the improved asynchronous support and performance.

No, that isn't a typo. The Homebrew formula for neovim is provided by the authors separately, so that is why the package name isn't just 'neovim'.

```
$ brew install neovim/neovim/neovim
```

## Make Backups

Backup your .vimrc or init.vim file. **I cannot stress this enough.** You might make a mistake or realize that this setup just isn't your bowl of peaches. Some day you will want to go back to your favorite config from 2004 and just incorporate some of the cool stuff you've learn from this experience.  **BACK IT UP!**

```
$ cp -R .vimrc .vimrc.bak
$ cp -R .vim/init.vim .vim/init.vim.bak
```

## Plugin Management

Any plugin manager should work, but you will need to make the changes yourself. I have been using [vim-plug](https://github.com/junegunn/vim-plug) for years because of its power, simplicity, and minimal interface.

```
$ curl -fLo ~/.vim/autoload/plug.vim --create-dirs \
		https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

## Relax

> call plug#begin()

It doesn't actually take much to make Vim a productive editing environment. I will detail the few plugins that I use below. There are [many more](http://vimawesome.com/) available, and I encourage you explore every so often to see if you can make your editing experience more efficient and enjoyable.

### Plug ['tpope/vim-sensible'](https://github.com/tpope/vim-sensible)

Most of the basic settings that you copy-and-pasted from the internet years ago are probably enabled and perfectly tweaked in `vim-sensible` already. After installing it, do what I did and delete each general option in your config one by one to see if it has become redundant.

The ones left over for me include changing `mapleader` to the space key, enabling line numbers, and configuring the drop down menu. It show up here, but the fillchars setting has a literal 'space' as the end. This removes the '|' from vertical lines, allowing the background color to provide the separation.

And don't forget, `vim-sensible` also configures `<CTRL-l>` to clear search highlights without leaving the home row.

```vim
let $NVIM_TUI_ENABLE_CURSOR_SHAPE=1
let $NVIM_TUI_ENABLE_TRUE_COLOR=1
set tabstop=2 softtabstop=2 shiftwidth=2 noexpandtab
set fillchars+=vert:\ 
let mapleader=" "
set number
set ignorecase
set noswapfile
set completeopt=longest,menuone
```

### Plug ['scrooloose/nerdtree'](https://github.com/scrooloose/nerdtree)

NERDTree is a side bar plugin that shows an interactive project view. You might also want to use something like [ctrl-p](https://github.com/kien/ctrlp.vim) for fuzzy file finding, but my brain gets a lot of value from a visual interface.

I like the tree to start open unless I provided a file on the command line. In that case, I am most likely using Vim as a pager, e.g., git commit.

I tend to use a big chunky font size. It helps me focus and forces me to write smaller functions. Plus it looks great in screencasts. Because of this, I make the view narrower and disable the UI clutter.

```vim
map <LEADER>f :NERDTreeToggle<CR>
let g:NERDTreeWinSize = 24
let g:NERDTreeMinimalUI = 1
autocmd VimEnter * if (0 == argc()) | NERDTree | endif
autocmd bufenter * if (winnr("$") == 1 && exists("b:NERDTree") && b:NERDTree.isTabTree()) | q | endif
```

### Plug ['morhetz/gruvbox'](https://github.com/morhetz/gruvbox)

The Gruvbox suite of color schemes is developed with an extreme level of thought and care. It provides support for all the third party Vim plugins you might use, and ships with hand crafted syntax overrides for many languages.

```vim
set background=dark
colorscheme gruvbox
```

The [gruvox-contrib](https://github.com/morhetz/gruvbox-contrib) repo also has support for most terminals. So go download the [itermcolors](https://raw.githubusercontent.com/morhetz/gruvbox-contrib/master/iterm2/gruvbox-dark.itermcolors) scheme if you want the same look and feel outside of Vim.

### Plug ['vim-airline/vim-airline'](https://github.com/vim-airline/vim-airline)

Airline is a "lean & mean status/tabline for Vim that's light as air." It is fast, looks great out of the box, and is infinitely customizable. And guess what? Gruvbox provides a great theme; No setup needed. I like to change the character that is used between sections in the status bar.

```vim
let g:airline_left_sep= '░'
let g:airline_right_sep= '░'
```

### Plug ['scrooloose/syntastic'](https://github.com/scrooloose/syntastic)

Syntastic is a syntax checking plugin, and it provides support for just about every language ever created. It runs your code through various linting programs, and shows any issues that come up. This allows you to catch errors long before you even try to compile.

I like to make sure the location list is automatically populated and toggled when there is a problem. It's also convenient to always check when you open a file, but ignore errors when exiting. And finally, the bright red error display it adds to airline isn't really needed, so I disable it.

```vim
let g:syntastic_always_populate_loc_list = 1
let g:syntastic_auto_loc_list = 1
let g:syntastic_check_on_open = 1
let g:syntastic_check_on_wq = 0
let g:airline#extensions#syntastic#enabled = 0
```

### Plug ['sheerun/vim-polyglot'](https://github.com/sheerun/vim-polyglot)

I know what you are thinking, "Vim is a code editor. Where are all the language plugins??" Vim Polyglot is a curated collection of language packs. This might be the most contentious choice so far, so I'll have the project speak for itself.

- It won't affect your startup time, as scripts are loaded only on demand.
- It installs and updates 70+ times faster than the 70+ packages it consist of.
- Solid syntax and indentation support, with the best language packs.
- All unnecessary files are ignored, like large documentation folders.
- No support for esoteric languages, only the most popular ones.
- Each build is tested by an automated vimrunner script on CI.

Perhaps it didn't choose the language pack you prefer, or you'd like to use the beta version of a specific plugin. Every language can be overriden, and that is exactly what we will do in the next section.

### Plug ['elmcast/elm-vim'](https://github.com/elmcast/elm-vim)

We are finally ready to configure Elm. First, I disable the elm package in polyglot, which provides the venerable [elm.vim](https://github.com/lambdatoast/elm.vim) plugin. I suggest you go check it out, because you might find you like it more than `elm-vim`.

I prefer turning detailed completion on, which shows the type declaration in the drop down menu. I also have [elm-format](https://github.com/avh4/elm-format) make my code look great every time I save a file. Finally, I have Syntastic show warnings in addition to errors.

```vim
let g:polyglot_disabled = ['elm']
let g:elm_detailed_complete = 1
let g:elm_format_autosave = 1
let g:elm_syntastic_show_warnings = 1
```

There are a few other file types that you often need to edit in an Elm project. Polyglot will take care of HTML, CSS, and Json for you. In Markdown files I like to turn on spelling, enable line breaks, and remove line numbers.

You can also configure the languages that you want highlighted in Markdown code fences.

```vim
autocmd BufNewFile,BufRead *.md set spell | set lbr | set nonu
let g:markdown_fenced_languages = ['html', 'json', 'css', 'javascript', 'elm', 'vim']
```

> call plug#end()

## Summary

I know it seems like a lot of work, but it goes to show how easy is is to take for granted the expert domain knowledge we accumulate over the years. If someone is coming from another editor, or isn't familiar the command line, each of those tiny configuration steps must be gleaned from pouring over disparate blog posts and help files.

I encourage you to think about what it's like for a beginner to get started with your own projects, and to create walkthroughs just for them. Remember, every detail is precious. Happy coding!
