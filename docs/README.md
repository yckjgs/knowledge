
## 前言
!>网上关于动态文档生成工具有很多如：Docsify、 VuePress、Docute 、Hexo这些都是一些非常优秀的文档生成工具，本章主要介绍如何快速使用Docsify搭建一个快捷、轻量级的个人&团队文档。

### 什么是Docsify？
>一个神奇的文档网站生成器。docsify 可以快速帮你生成文档网站。不同于 GitBook、Hexo 的地方是它不会生成静态的 .html 文件，所有转换工作都是在运行时。如果你想要开始使用它，只需要创建一个 index.html 就可以开始编写文档。
- 无需构建，写完文档直接发布
- 容易使用并且轻量 (压缩后 ~21kB)
- 智能的全文搜索
- 提供多套主题
- 丰富的 API
- 支持 Emoji
- 兼容 IE11
- 支持服务端渲染 SSR ([示例](https://github.com/docsifyjs/docsify-ssr-demo))

### 轻量&完善的Docsify模板
>该模板为一个简洁，并且完善的Docsify模板基本上可以满足百分之八十多的团队需求，你可以按照文章中的Docsify环境配置教程把运行Docsify所需要的环境配置起来，通过命令即可查看效果（配置环境顺利的话只要十来分钟）。

## 安装及运行

### 安装node环境
安装`docsify-cli`之前，我们需要安装`npm`包管理器，而安装了`node.js`就会自动安装`npm`

#### 安装node
官网下载安装程序，双击下载的exe安装，下一步下一步直到完成。
官网地址：<https://nodejs.org/en/>

#### 验证安装
输入node -v，npm -v输出版本就是安装成功了
```
node -v #验证node
npm -v #验证npm
```

### 安装docsify-cli工具
推荐全局安装 `docsify-cli` 工具，可以方便地创建及在本地预览生成的文档。
```
npm i docsify-cli -g #用npm安装全局工具
```

### 下载Docsify-Plus项目
```
Gitee地址：
https://gitee.com/librarycodes/docsify-plus
GitHub地址：
https://github.com/shiming-git/docsify-plus
```

### 运行Docsify-Plus
默认运行端口为 3000  docs为Docsify-Plus的默认目录，此目录可自行更改，可改为更目录

```
docsify serve docs
docsify serve -p 80 docs #指定端口运行
```
游览器打开输入访问地址，[http://localhost:3000](http://localhost:3000)


## Git代码提交命令
```
git add . #加当前目录的所有文件到暂存区
git commit -m [页面优化] #提交暂存区到仓库区
git push origin pro #推送代码到远程仓库
```

## 安装插件
[docsify-sidebar-collapse插件:侧边栏折叠](https://github.com/iPeng6/docsify-sidebar-collapse)

[scroll-to-top插件:滚动到页面顶部](https://gitee.com/zhangx_study/docsify-plugins)
