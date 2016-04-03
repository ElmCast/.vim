# ElmCast.vim

I have been asked a lot lately about the Vim setup I use for [ElmCast.io](http://www.elmcast.io/). My config file is less than 50 lines long, and I keep the number of plugins to a minimum. Everything should work out of the box on pretty much any platform, and I will describe the exact steps to reproduce my day to day environment.

## Choose a Terminal

You probably already have a favorite terminal, and I won't try to convince you to change. To get the best experience, however, you need one with 24bit color support. I use the 2.9 beta of [iTerm2](http://iterm2.com/downloads.html) which works great. The caveat is that tmux only recently got patched and can be a bit fiddly to enable. You'll need to set the `TERM` of the outer shell to a value that supports it, then add a terminal override in tmux.

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

I prefer [neovim](https://github.com/neovim/neovim) which is just an all around better experience than vanilla Vim. Most plugins work great with it already, and can make use of the improved async support and performance.

```
$ brew install neovim
```

## Make Backups

**BACKUP your .vimrc or init.vim file.** I cannot stress this enough, because you might make a mistake or realize that this setup just isn't your bowl of peaches. Some day you will want to go back to your favorite config and just incorporate some of the cool stuff you've learn from this experience.

```
$ cp -R .vimrc .vimrc.bak
$ cp -R .vim/init.vim .vim/init.vim.bak
```

## Plugin Management

Any plugin manager should work, but you will need make to make the needed changes for yourself. I have been using [vim-plug](https://github.com/junegunn/vim-plug) for years because of its power, simplicity, and minimal interface.

```
$ curl -fLo ~/.vim/autoload/plug.vim --create-dirs \
		https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

## Relax.

> call plug#begin()

It doesn't take much to get that IDE feeling from a fresh Vim install. You will probably want to add more cool plugins, and configure languages just how you like it. Go for it!

## [Plug 'tpope/vim-sensible'](https://github.com/tpope/vim-sensible)

Most of the basic settings you copy and pasted into your .vimrc file years ago are perfectly tweaked in `vim-sensible` already. After installing it, do what I did and delete each general option in your config and see if it has become redundant. The ones left form me include changing the `mapleader` to the space key, and enabling line numbers.

And don't forget, it also sets up `<CTRL-l>` to easily clear the search highlight without leaving the home row.

```vim
let $NVIM_TUI_ENABLE_CURSOR_SHAPE=1
let mapleader=" "
set tabstop=2 softtabstop=2 shiftwidth=2 noexpandtab
set number
set ignorecase
set noswapfile
set completeopt=longest,menuone
```

## [Plug 'scrooloose/nerdtree']()

NERDTree is a side bar plugin that shows an interactive project view. You probably also want to use something like [ctrl-p](https://github.com/kien/ctrlp.vim) for fuzzy finding files, but my brain gets a lot of value from a visual interface. I like the tree to start open unless I have opened a specific file from the command line. In that case, I am most likely using Vim as a pager (git commit, for example.)

I tend to use a big chunky font size. It helps me focus and forces me to write smaller functions. Plus it looks great in screencasts. Because of this, I make the view narrower and disable the ui clutter.

```vim
map <LEADER>f :NERDTreeToggle<CR>
let g:NERDTreeWinSize = 24
let g:NERDTreeMinimalUI = 1
autocmd VimEnter * if (0 == argc()) | NERDTree | endif
autocmd bufenter * if (winnr("$") == 1 && exists("b:NERDTree") && b:NERDTree.isTabTree()) | q | endif
```

### [Plug 'morhetz/gruvbox']()

The gruvbox suite of color schemes is developed with an extreme level of care. It provides support for all the third party vim plugins you might use, and ships with hand crafted syntax overrides for many languages. The [gruvox-contrib](https://github.com/morhetz/gruvbox-contrib) repo has support for most terminal emulators.

So now go download the correct [itermcolors](https://raw.githubusercontent.com/morhetz/gruvbox-contrib/master/iterm2/gruvbox-dark.itermcolors) file and install it in iTerm if you want the same look and feel outside of vim.

### [Plug 'vim-airline/vim-airline']()

Airline is a "Lean & mean status/tabline for vim that's light as air." Its fast, looks great out of the box, and is infinitely customizable. And guess what? Gruvbox provides a great theme; no setup needed. The sign of a quality plugin is one that needs no configuration.

```vim
" No really. It just works.
```

### [Plug 'scrooloose/syntastic']()

Syntastic is a syntax checking plugin for Vim, and it provides support for every language ever created. Even that hobby functional compiler you abandoned two years ago. It runs various programs that lint your code, and displays where the problem is. This is going to save you time.

I like to make sure the location list is automatically populated and toggled. This works great with `ElmMake` and `ElmDocDetail` in elm-vim. The ugly, bright red error display in adds to airline just really isn't needed, so I disable it.

```vim
let g:syntastic_always_populate_loc_list = 1
let g:syntastic_auto_loc_list = 1
let g:airline#extensions#syntastic#enabled = 0
```

### [Plug 'sheerun/vim-polyglot']()

I know what you are thinking, "Vim is a code editor. Where are all the language plugins??" Vim Polyglot is a curated collection of language packs for Vim. This might be the most contentious choice so far, so I'll have the project speak for itself.

- It **won't affect your startup time**, as scripts are loaded only on demand.
- It **installs and updates 70+ times faster** than 70+ packages it consist of.
- Solid syntax and indentation support. Only the best language packs.
- All unnecessary files are ignored (like enormous documentation from php support).
- No support for esoteric languages, only most popular ones (modern too, like `slim`).
- Each build is tested by automated vimrunner script on CI. See `spec` directory.

Perhaps it didn't choose the language pack you prefer, or you'd like to use the `HEAD` version of a specific plugin. Every language can be overriden, and that is exactly what we will do in the next section.

### [Plug 'elmcast/elm-vim']()

You came here to learn how to learn about Elm and vim, and we are finally ready. First, I disable the elm package in polyglot, which uses the venerable [elm.vim](https://github.com/lambdatoast/elm.vim). I suggest you go check it out, because you might find you like it more than `elm-vim`.

I prefer having detailed completion on, which shows the type declaration in the drop down menu. I also have [elm-format](https://github.com/avh4/elm-format) make my code look great every time I save a file. Finally, I like for syntastic to also show warnings from the Elm compiler.

```vim
let g:polyglot_disabled = ['elm']
let g:elm_detailed_complete = 1
let g:elm_format_autosave = 1
let g:elm_syntastic_show_warnings = 1
```

There are a few other file types that you will often need to edit in an Elm project. Polyglot will take care of html, css, and json for you. In Markdown files I like to turn on spelling, enable line breaks, turn off line numbers, and add key mappings to easily navigate long lines.

You can also add the languages that you want to be highlighted in Markdown code fences.

```vim
autocmd BufNewFile,BufRead *.md set spell | set lbr | set nonu | nn j gj | nn k gk
let g:markdown_fenced_languages = ['html', 'json', 'css', 'javascript', 'elm', 'vim']
```

> call plug#end()

## Summary

I know it seems like a lot of work, but it just goes to show how it is easy to take for granted the domain knowledge we have accumulated over the years as experts. If someone is coming from another editor, or aren't used to working from the command line, all those tiny configuration steps are going to be hard won over many years. I encourage you to think about what it would be like for a beginner to get started with your projects, and create detailed guides just for them. Remember, every detail is precious. Happy coding!