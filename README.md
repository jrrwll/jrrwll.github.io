# Introduction

### How I start this
```shell
brew install hugo
```

##### theme docsy
```shell
git clone https://github.com/google/docsy-example.git jrrwll.github.io
cd jrrwll.github.io
git submodule update --init --recursive --depth 1

yarn
```

##### hugo
```shell
hugo -D
hugo server -D

# hugo new posts/my-first-post.md
```