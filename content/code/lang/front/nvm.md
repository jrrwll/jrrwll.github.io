
---
title: "nvm"
---

## install

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
```

## config

```shell
​​export NVM_NODEJS_ORG_MIRROR=https://npm.taobao.org/mirrors/node/
export NVM_IOJS_ORG_MIRROR=https://npm.taobao.org/mirrors/iojs/

nvm install stable
nvm install lts
```

## npm

```shell
npm config set registry https://registry.npm.taobao.org/
```