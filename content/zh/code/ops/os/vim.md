
---
title: "Vim"
---

## Plug

### Vim-Plug

```shell
# PlugInstall [pluginName]
# PlugUpdate [pluginName]
# PlugDiff : show changelog
# PlugUpgrade : upgrade itself
# PlugStatus
# PlugClean
# PlugSnapshot [filePath] : save a snapshot
curl -fLo ~/.vim/autoload/plug.vim --create-dirs https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

```vmscript
" ~/.vim/bundle for Vundle(https://github.com/VundleVim/Vundle.vim
) compatibility
" Plug 'userName/repoName' or Plug 'pluginName' for https://github.com/vim-scripts
" Vim-Plug download Plugin like this: git -C ~/.vim/bundle clone --recursive https://github.com/vim-scripts/L9.git

call plug#begin('~/.vim/bundle')
    " Plug 'file:///path/to/plugin'
    " Plug "git:https://exmaple.com/user/repo.git"

    " dir tree
    Plug 'preservim/nerdtree'
    " start screen
    Plug 'mhinz/vim-startify' 
    " highlighting and navigating through different words in a buffer
    Plug 'lfv89/vim-interestingwords'
    " Check syntax 
    Plug 'dense-analysis/ale'
    " lean & mean status/tabline for vim that's light as air
    Plug 'vim-airline/vim-airline'
call plug#end()
```
