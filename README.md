# Introduction

### How I start this
```sh
brew install hugo
```

##### theme docsy
```sh
git clone https://github.com/google/docsy-example.git jrrwll.github.io
cd jrrwll.github.io
git submodule update --init --recursive --depth 1

yarn
```

##### hugo
```sh
hugo -D
hugo server -D

# hugo new posts/my-first-post.md
```