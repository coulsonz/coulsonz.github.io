## 待开发事项：

- 待补充

---
## 注意：修改hexo目录下配置文件同步至coding.net

---

## 每次换电脑更新博客前，先执行以下步骤

1. 先`git remote add origin https://github.com/coulsonz/coulsonz.github.io.git`
2. 然后`git pull origin hexo`最新的hexo文件
3. 执行`npm install`
4. 编辑、撰写文章或其他博客更新改动
5. 依次执行`git add .`、`git commit -m 'back up hexo files'`（引号内容可改）、`git push`指令，保证 hexo 分支版本最新
6. 最后，`hexo clean`（清理缓存有的需要，有的不需要） 发布新内容
7. 执行`hexo d -g`指令

---

新文章常用技巧：

1. 屏蔽MD解析文章中HTML或JS代码块的功能，使用`{% row %}要屏蔽内容{% endrow %}`
2. {% row %}`<!-- more -->`{% endrow %}

---

**常用文件修改路径**

1. 目录菜单和中文翻译

   - _config.yml主题配置文件：\themes\next\ _config.yml

   - zh-Hans.yml汉化文件：\themes\next\languages
