
---
title: "Hugo"
---

## setup

```shell
# sudo apt install hugo
brew install hugo

hugo new site quickstart
cd quickstart
git submodule add https://github.com/dillonzq/LoveIt.git themes/LoveIt
echo "theme = 'LoveIt'" >> hugo.toml
hugo server
```

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

### hugoblox
> https://hugoblox.com/templates/
> https://hugoblox.com/templates/details/docs/

```shell
git clone https://github.com/HugoBlox/theme-blog

# content/authors/admin/_index.md
# content/post/your_folder/index.md
```

### LoveIt
> https://hugoloveit.com/zh-cn/

```shell
git submodule add https://github.com/dillonzq/LoveIt.git themes/LoveIt

# 实时预览
hugo serve --disableFastRender
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

### hugo-book
> https://hugo-book-demo.netlify.app/
```shell
git submodule add https://github.com/alex-shpak/hugo-book.git themes/hugo-book
cp -R themes/hugo-book/exampleSite/content.en/* ./content
```
