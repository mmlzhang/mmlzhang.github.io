---
weight: 10
---



## 安装hugo

```sh
brew install hugo
```



## 创建项目

```shell
hugo new site mlzhang_wiki

cd mlzhang_wiki
git init

# 安装主题
git submodule add https://github.com/alex-shpak/hugo-book themes/hugo-book

hugo mod init github.com/repo/path


# config.toml 添加
[module]
[[module.imports]]
path = 'github.com/alex-shpak/hugo-book'


# 拷贝示例
cp -R themes/hugo-book/exampleSite/content .
```



## 启动本地服务

```
hugo server -D
```