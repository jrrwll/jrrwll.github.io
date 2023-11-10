
---
title: "Hugo"
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
git clone https://kgithub.com/mirrors_google/docsy themes/docsy --depth 1
git clone https://kgithub.com/jrrwll/bootstrap themes/docsy/assets/vendor/bootstrap --depth 1 --branch=v4-dev
git clone https://kgithub.com/jrrwll/Font-Awesome themes/docsy/assets/vendor/Font-Awesome --depth 1
```

### hugo-chart(chart.js)

```shell
# theme = ["hugo-chart", "docsy"]
git clone  https://github.com/Shen-Yu//hugo-chart themes/hugo-chart --depth 1
```

```xml
{ { < chart width height > } }
// Chartjs options goes here
{ { < /chart > } }
```
