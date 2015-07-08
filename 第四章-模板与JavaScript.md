第四章-模板和JavaScript
*********************
本章，我们讨论到以下话题：  

```
整理base.html模板

包含JavaScript设置

使用HTML5数据属性

在弹窗中显示对象细节

实现不间断滚动

实现Like部件

使用Ajax上传图片
```

## 引言
我们生活在

## 整理base.html模板

>#####提示

## 预热

## 具体做法
执行以下步骤：  

1.在模板的根目录中使用下列内容创建一个`base.html`文件：  

```python
{#templates/base.html#}
{% block doctype %}<!DOCTYPE html>{% endblock %}
{% load i18n %}
<html lang="{{ LANGUAGE_CODE }}">
<head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <title>{% block title %}{% endblock %}{% trans "My Website" %}</title>
    <link rel="icon" href="{{ STATIC_URL }}site/img/favicon.ico" type="image/png" />

    {% block meta_tags %}{% endblock %}

    <link rel="stylesheet" href="http://netdna.bootstrapcdn.com/bootstrap/3.1.1/css/bootstrap.min.css" />
    “ <link href="{{ STATIC_URL }}site/css/style.css" rel="stylesheet" media="screen" type="text/css" />

    {% block stylesheet %}{% endblock %}
    <script src="http://code.jquery.com/jquery-1.11.0.min.js"></script>
    <script src="http://code.jquery.com/jquery-migrate-1.2.1.min.js"></script>
    <script src="http://netdna.bootstrapcdn.com/bootstrap/3.1.1/js/bootstrap.min.js"></script>
    <script src="{% url "js_settings" %}"></script>
    {% block js %}{% endblock %}
    {% block extrahead %}{% endblock %}
</head>
<body class="{% block bodyclass %}{% endblock %}">
    {% block page %}
        <div class="wrapper">
            <div id="header" class="clearfix">
                <h1>{% trans "My Website" %}</h1>
                {% block header_navigation %}
                    {% include "utils/header_navigation.html" %}
                {% endblock %}
                {% block language_chooser %}
                    {% include "utils/language_chooser.html" %}
                {% endblock %}
            </div>
            <div id="content" class="clearfix">
                {% block content %}
                {% endblock %}
            </div>
            <div id="footer" class="clearfix">
                {% block footer_navigation %}
                    {% include "utils/footer_navigation.html" %}
                {% endblock %}
            </div>
        </div>
    {% endblock %}
    {% block extrabody %}{% endblock %}
</body>
</html>
```
  

2. 在相同的目录中，针对特定的情况创建另外一个命名为`base_simple.html`：  

```python
{# templates/base_simple.html #}
{% extends "base.html" %}

{% block page %}
    <div class="wrapper">
        <div id="content" class="clearfix">
            {% block content %}
            {% endblock %}
        </div>
    </div>
{% endblock %}
```
  
## 具体做法


## 参阅

## 加入JavaScript的设置
