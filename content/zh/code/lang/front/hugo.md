
---
title: "Hugo"
date: 2023-03-11
---

## deploy

https://gohugo.io/hosting-and-deployment/hosting-on-github/

```shell
touch netlify.toml

yarn add -D netlify-cli
yarn run netlify deploy --prod
```

## theme

### docsy

```shell
git clone  https://gitee.com/mirrors_google/docsy themes/docsy --depth 1
git clone https://gitee.com/jrrwll/bootstrap themes/docsy/assets/vendor/bootstrap --depth 1 --branch=v4-dev
git clone https://gitee.com/jrrwll/Font-Awesome themes/docsy/assets/vendor/Font-Awesome --depth 1
```
