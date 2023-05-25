
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

```shell
cat <<EOF > ~/.zshrc
export ZSH="\$HOME/.oh-my-zsh"
ZSH_THEME="myagnoster"
plugins=(git zsh-syntax-highlighting zsh-autosuggestions)
source \$ZSH/oh-my-zsh.sh

alias dk="sudo docker"
alias dkn="dk network"
alias dkv="dk volume"
alias dki="dk image"
alias dkl="dk logs -f"
alias dke="dk exec -it"
EOF
```