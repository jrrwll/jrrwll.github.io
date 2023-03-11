
---
title: "Oh My Zsh"
---

## zsh

```shell
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)" 

cp ~/.oh-my-zsh/themes/agnoster.zsh-theme ~/.oh-my-zsh/custom/themes/
mv ~/.oh-my-zsh/custom/themes/agnoster.zsh-theme ~/.oh-my-zsh/custom/themes/myagnoster.zsh-theme
# vim ~/.oh-my-zsh/custom/themes/myagnoster.zsh-theme
# replace blue to cyan
# vim ~/.zshrc
# ZSH_THEME="myagnoster"

# add zsh-autosuggestions zsh-syntax-highlighting
cd ~/.oh-my-zsh/custom/plugins
git clone https://github.com/zsh-users/zsh-autosuggestions --depth 1
git clone https://github.com/zsh-users/zsh-syntax-highlighting --depth 1
# . ~/.zshrc
```

## vim

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