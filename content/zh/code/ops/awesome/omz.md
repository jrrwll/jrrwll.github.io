
---
title: "Oh My Zsh"
---

## zsh

```shell
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)" 

# themes
cp ~/.oh-my-zsh/themes/agnoster.zsh-theme ~/.oh-my-zsh/custom/themes/myagnoster.zsh-theme
sed -i 's/blue/cyan/' ~/.oh-my-zsh/custom/themes/myagnoster.zsh-theme
sed -i 's/^ZSH_THEME=".*"/ZSH_THEME="myagnoster"/' ~/.zshrc

# plugins
cd ~/.oh-my-zsh/custom/plugins
git clone https://github.com/zsh-users/zsh-autosuggestions --depth 1
git clone https://github.com/zsh-users/zsh-syntax-highlighting --depth 1
sed -i 's/plugins=(git)/plugins=(git zsh-autosuggestions zsh-syntax-highlighting)/' ~/.zshrc
```

### ~/.zshrc

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

### powerlevel10k

```shell
cd ~/.oh-my-zsh/custom/themes
git clone https://github.com/romkatv/powerlevel10k --depth 1
sed -i 's/^ZSH_THEME=".*"/ZSH_THEME="powerlevel10k\/powerlevel10k"/' ~/.zshrc
```
