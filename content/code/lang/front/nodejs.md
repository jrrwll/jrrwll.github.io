
---
title: "node.js"
---

## install

### nvm

```shell
brew install nvm
mkdir ~/.nvm
chmod +x /usr/local/opt/nvm/etc/bash_completion.d/nvm

# Add the following to ~/.zshrc or your desired shell
export NVM_DIR="$HOME/.nvm"
# This loads nvm
[ -s "/usr/local/opt/nvm/nvm.sh" ] && . "/usr/local/opt/nvm/nvm.sh"  
# This loads nvm bash_completion
[ -s "/usr/local/opt/nvm/etc/bash_completion.d/nvm" ] && (. "/usr/local/opt/nvm/etc/bash_completion.d/nvm") 

nvm install stable
nvm install lts
```

## stat

```shell
# count code line number
tree -if | grep -v node_modules | egrep '[.](j|t)sx?$' | xargs wc -l
```
