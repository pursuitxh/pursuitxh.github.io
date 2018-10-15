---
title: hexo
date: 2018-10-15 17:13:39
tags: 博客编写
categories: hexo
---
主要参照next主题的指导步骤：
http://theme-next.iissnan.com/getting-started.html

## 1.本地编辑并预览和调试：
a. hexo new page hello-hexo
b. 打开生成的hell-hexo.md文件（一句markdown语法进行编辑）
c. 默认有title和date两项
d. 如果要给此篇文章进行归类和打标签，请在下面加tags： categories： 名字自己取
e.在---下面根据markdown语法，进行博客编写，windows可借助MarkdownPad 2工具
f. hexo clean
g. hexo s --debug
h. local:4000进行预览和调试

## 2.博客提交
a. hexo clean
b. hexo g
c. hexo d
d. 打开网址预览
