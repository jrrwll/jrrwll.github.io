
---
title: "Oh My Zsh"
---

## zsh

```shell
apt install zsh git curl wget file -y

sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)" 
# or in china mainland
sh -c "$(curl -kfsSL https://raw.fastgit.org/ohmyzsh/ohmyzsh/master/tools/install.sh | sed 's/github.com/gitclone.com\/github.com/')"

# themes
cp ~/.oh-my-zsh/themes/agnoster.zsh-theme ~/.oh-my-zsh/custom/themes/myagnoster.zsh-theme
sed -i 's/blue/cyan/' ~/.oh-my-zsh/custom/themes/myagnoster.zsh-theme
sed -i 's/^ZSH_THEME=".*"/ZSH_THEME="myagnoster"/' ~/.zshrc

# plugins
cd ~/.oh-my-zsh/custom/plugins
git clone https://github.com/zsh-users/zsh-autosuggestions --depth 1
git clone https://github.com/zsh-users/zsh-syntax-highlighting --depth 1
sed -i 's/plugins=(git)/plugins=(git zsh-autosuggestions zsh-syntax-highlighting)/' ~/.zshrc
sed -i '$a source ~/.oh-my-zsh/custom/plugins/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh' ~/.zshrc
```

### ~/.zshrc

```shell
cat <<EOF >> ~/.zshrc

alias dk="sudo docker"
alias dki="dk image"
alias dke="dk exec -it"
alias dkr="dk run --rm=true"
alias dkl="dk logs -f"
alias dkv="dk volume"
alias dkn="dk network"
alias dkc="dk container"
alias dkic="dk inspect container"
alias dkii="dk inspect image"
alias dkm="dk machine"
# alias docker-compose="podman-compose"
alias dkp="docker-compose"
alias dkpe="docker-compose exec"

EOF
```

### powerlevel10k

```shell
cd ~/.oh-my-zsh/custom/themes
git clone https://github.com/romkatv/powerlevel10k --depth 1
sed -i 's/^ZSH_THEME=".*"/ZSH_THEME="powerlevel10k\/powerlevel10k"/' ~/.zshrc
```
