---
title: Hexo之NexT主题的分类菜单缩进
categories:
  - notes
copyright: true
date: 2018-12-13 16:56:13
tags:
top: true
password:
---



以下代码只针对二级目录进行了缩进，如果要对更深层次缩进，请自行修改。

修改的文件路径为：`themes\next\layout\_partials\header.swig`，

打开后搜索`% if theme.menu %`即可找到位置。

添加代码在`开始判断是否为二级目录 `和` 结束判断是否为二级目录 `之间：

<!-- more -->

```js
  {% if theme.menu %}
    <ul id="menu" class="menu">
      {% for name, path in theme.menu %}
        {% set itemName = name.toLowerCase() %}
        <li class="menu-item menu-item-{{ itemName | replace(' ', '-') }}">
          <a href="{{ url_for(path.split('||')[0]) | trim }}" rel="section">
			<!-- 开始判断是否为二级目录 -->
			{% set tmp = path.split('||')[0] %}
			{% set t1 = tmp.split('/')[2] %}
			{% if t1.length>1 %}
				&nbsp;&nbsp;&nbsp;&nbsp;
			{% endif %}
			<!-- 结束判断是否为二级目录 -->
            {% if theme.menu_icons.enable %}
              <i class="menu-item-icon fa fa-fw fa-{{ path.split('||')[1] | trim | default('question-circle') }}"></i> <br />
            {% endif %}
            {{ __('menu.' + name) | replace('menu.', '') }}
          </a>
        </li>
      {% endfor %}
```

