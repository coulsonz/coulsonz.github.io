---
title: 注意事项
tags: [test]
categories: hexo
top: true
---



**每次换电脑更新博客前，先执行以下步骤**

1. 先`git pull hexo`最新的hexo文件
2. 执行`npm install`
3. 编辑、撰写文章或其他博客更新改动
4. 依次执行`git add .`、`git commit -m 'back up hexo files'`（引号内容可改）、`git push`指令，保证 hexo 分支版本最新
5. 最后，发布新内容，执行`hexo d -g`指令