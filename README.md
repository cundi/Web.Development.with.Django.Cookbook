 
## 简介

##译文同时以 GitHub Issue 的形式发布，[点此阅读](https://github.com/cundi/Web.Development.with.Django.Cookbook/issues?state=open)。


## 版权协议

除注明外，所有文章均采用 [Creative Commons BY-NC-ND 3.0（自由转载-保持署名-非商用-非衍生）](http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh) 协议发布。

这意味着你可以在非商业的前提下免费转载，但同时你必须：

* 保持文章原文，不作修改。
* 明确署名，即至少注明 `作者：cundi` 字样以及文章的原始链接。

如需商业合作，[请直接联系作者](https://github.com/cundi/Web.Development.with.Django.Cookbook/issues/3)。  
如果你认为译文对你所有帮助，而且希望看到更多，可以考虑[小额捐助](https://github.com/cundi/Web.Development.with.Django.Cookbook/issues/3)。
------------------------- 

# Web.Development.with.Django.Cookbook
中文名：《Django网站开发Cookbook》，14年10月份的新书，可不是09年的那个 Django book  
原版英文：https://www.packtpub.com/web-development/web-development-django-cookbook   
作者：Aidas Bendoraitis  
日期： October 2014  
特色：Over 70 practical recipes to create multilingual, responsive, and scalable websites with Django  
级别：Cookbook  
页数：Paperback 294 pages    
  
 
第五章预览
*********
第五章 定制模板过滤器和标签
------------------------  

本章，我们会学习以下内容：  

* 遵循模板过滤器和标签的约定
* 创建一个模板过滤器显示已经过去的天数
* 创建一个模板过滤器提取第一个媒体对象
* 创建一个模板过滤器使URL可读
* 创建一个模板标签在模板中载入一个QuerySet
* 创建一个模板标签为模板解析内容
* 创建一个模板标签修改request查询参数  
  
## 简介
如你所知，Django有一个非常庞大的模板系统，拥有模板继承，改变具体值的过滤器，以及属于显示逻辑的标签。此外，Django允许你在应用中添加你自己的模板过滤器和标签。定制过滤器或者标签应该位于应用中`templatetags`下的标签库文件。你的模板库在任何模板中使用`{% load %}`模板标签载入。在这一章，我们会创建多个实用的过滤器和对模板编辑带来更多控制的标签。  

## 遵循模板过滤器和标签的规定
如果你么有坚持按指南来做，那么定义模板过滤器和标签会变得极其混乱不堪。模板过滤器和标签应该尽可能地服务于模板。它们应该同时具有方便性和灵活性。在这个做法中，我们会看到用来增强Django模板系统功能的规定。  
  

### 如何做
扩展Django模板系统时遵循以下规定：  

```
1. 当在视图，上下文处理器，或者模型方法中，页面更适合逻辑时，不要创建或者使用定制模板过滤器、标签。你的页面是上下文指定时，比如一个对象列表或者一个详细对象视图，载入视图中的对象。如果你需要在每一个页面都显示某些内容，可以创建一个上下文管理器。当你需要获取一个没有关联到模板上下文的对象的某些特性时，要用模型的定制方法而不是使用模板过滤器。
```
  

```
2. 使用 _tags后缀命名模板标签库。当你的app命名不同于模板标签库时，你可以避免模糊的包导入问题。
```
  
```python
3. 例如，通过使用如下注释，在最新创建的库中，从标签中分离过滤器：  


    # -*- coding: UTF-8 -*-
    from django import template
    register = template.Library()
    ### FILTERS ###
    # .. your filters go here ..

    ### TAGS ###
    # .. your tags go here..
```
  
```python
4. 通过纳入以下格式，创建模板标签也可以被轻松记住：  


for [app_name.model_name]：使用该格式以使用指定的模型

using [template_name]：使用该格式将一个模板的模板标签输出

limit [count]：使用该格式将结果限制为一个指定的数量

as [context_variable]：使用该结构可以将结构保存到一个在之后多次使用的上下文变量
```
  

```
5. 要尽量避免在模板标签中按位置地定义多个值，除非它们都是不解自明的。否则，这会有可能使开发者迷惑。
```
  
```
6. 尽可能的使用更多的可理解的参数。没有引号的字符串应该当作需要解析的上下文变量或者可以提醒你关于模板标签组件的短语。
```
  

## 参见

*创建一个显示已经过去天数的模板过滤器*  

*创建一个提取第一个媒体对象的模板过滤器*  

*创建一个人类可理解的URL的模板过滤器*  

*创建一个接纳已经存在的模板的模板标签*  

*创建一个载入模板中的Queryset的模板标签*  

*创建一个可以把内容解析为模板的模板标签*  

*创建一个修改request查询参数的模板标签*  
  
## 创建一个显示已经过去天数的模板过滤器
不是所有的人都需要持续追踪日期，当论及前沿信息的创建和修改时，对于我们多数人来说，读取时间的差异会更加便利，例如，三天之前发布的博客文章，当日出版的新闻头条，昨日最后登录的用户。此做法中，我们会创建一个称为`days_since`的模板过滤器，它会转换到人类可理解的时间差。  

## 准备开始
如果完成操作，可以在设置中的`INSTALLED_APPS`下创建`utils`应用。然后，在这个应用中创建命名为`templatetags`Python包（Python包是一个拥有空的`__init__.py`文件）。  

## 怎么做
用下面的内容创建一个`utility_tags.py`文件：  

```python
#utils/templatetags/utility_tags.py
# -*- coding: UTF-8 -*-
from datetime import datetime

from django import template
from django.utils.translation import ugettext_lazy as _
from django.utils.timezone import now as tz_now
register = template.Library()

### FILTERS ###

@register.filter
def days_since(value):
    """ 返回天和值之间的天数."""

    today = tz_now().date()
    if isinstance(value, datetime.datetime):
        value = value.date()
    diff = today - value
    if diff.days > 1:
        return _("%s days ago") % diff.days
    elif diff.days == 1:
        return _("yesterday")
    elif diff.days == 0:
        return _("today")
    else:
        # Date is in the future; return formatted date.
        return value.strftime("%B %d, %Y")
```
  
## 工作原理
如果像下边这样在模板中使用这个过滤器，它会返回`yesterday`或者`5 days ago`这样的内容:  

```python
“{% load utility_tags %}
{{ object.created|days_since }}
You can apply this filter to the values of the date and datetime types.
```
  
你可以应用这个过滤器到`date`的值，和`datetime`的类型。  

每个标签库都有一个注册器，它是收集过滤器和标签的地方。Django过滤器使用`register.filter`装饰器所注册的函数。默认，模板系统中的过滤器命名为相同名称的函数，或者其他的可调用对象。如果你想的话，你可以通过传递`name`到装饰器来为过滤器设置不同的名称:  

```python
@register.filter(name="humanized_days_since")
def days_since(value):
    ...
```
  
过滤器本身意思是非常显而易见的。首先，当前日期被读取。如果给出的过滤器值是`datetime`类型，那么日期就会被提取。然后，计算今天和提取值之间的差异。视天数而定，返回不同的字符串结果。  


## 还有更多
过滤器可以很容易地扩展以显示时间的不同，比如`just now`, `7 minutes ago`，或者`3 hours ago`。只要操作`datetime`值便是，而不是操作`date`值。  

## 参阅
*创建一个提取第一个媒体对象的模板过滤器*  

*创建一个人类可理解的URL的模板过滤器*  

## 创建一个提取第一个媒体对象的模板过滤器
想象一下，你正在开发一个博客概述页面，对于每篇文章，你想在这个页面中显示内容中的图片，音乐或者视频。这样的情况下，你需要从文章的HTML内容中提取`<img>`，`<object>`，和`<embed>`标签。这个做法中，我们会见到在`get_first_media`中如何使用正则表达式完成目标。  
  

## 准备开始吧
我们从位于设置中的`INSTALLED_APPS`的`utils`应用开始，并将`templatetags`包置于该应用内部。  

## 如何做
在`utility_tags.py`文件中，添加以下内容：  

```python
#utils/templatetags/utility_tags.py
# -*- coding: UTF-8 -*-
import re
from django import template
from django.utils.safestring import mark_safe
register = template.Library()

### FILTERS ###

media_file_regex = re.compile(r"<object .+?</object>|"
    r"<(img|embed) [^>]+>") )

@register.filter
def get_first_media(content):
    """ 返回html内容中第一个图片或者flash文件 """
    m = media_file_regex.search(content)
    media_tag = ""
    if m:
        media_tag = m.group()
    return mark_safe(media_tag)
```
  
## 工作原理
当数据中的HTML内容有效时，把下面的代码写到模板里，它会从对象的内容字段重新取回`<object>`，`<img>`或者`<embed>`标签，或者一个因媒体不存在而出现的空字符串:  

```python
{% load utility_tags %}
{{ object.content|get_first_media }}
```
  
首先，我们把编译过的正则表达式定义为`media_file_regex`，然后，在过滤器中，我们执行正则表达式模式的搜索。默认，结果会把出现的`<`，`>`和`&`转义为条目`&lt;`，`&gt;`，以及`&amp;`。我们使用`mark_safe`函数把结果标记为安全的HTML，为在模板中不实用转义显示做好准备。  

## 还有更多
你可以非常容易地扩展这个过滤器，它也可以提取`<iframe>`标签（最近它们被Vimeo和Youtube用来内嵌视频）或者HTML5的`<audio>`和`<video>`标签，可以像这样修改正则表达式:  

```python
media_file_regex = re.compile(r"<iframe .+?</iframe>|"
    r"<audio .+?</ audio>|<video .+?</video>|"
    r"<object .+?</object>|<(img|embed) [^>]+>") ”
```
  
## 参阅
创建一个显示已经过去天数的模板过滤器  

创建一个人类可理解的URL的模板过滤器  

  
#目录预览  
************

##第一章， **从Django1.6开始**
指导你通过必要的基本配置以新建任意Django项目。本章覆盖内容有，虚拟环境，会话控制，以及项目设置。

##第二章，**数据库结构**
教会你如何写可重复使用的代码片段并用在模型中。当你创建一个新的app时，要做的第一件就是定义模型。你也告知如何使用South迁移管理数据库表变更。

##第三章，**表单和视图**
向你演示使用一些模式为数据创建视图和表单

##第四章，**模板和JavaScript**
向你演示把模板和JavaScript放在一起使用的实际例子。我们把模板和JavaScript放在一起是因为，总是通过渲染模板将内容展现给用户，在现代的网站中，JavaScript对于更丰富的用户体验也是必要的。  

##第五章，**自定义模板过滤器和标签**
本章向你演示如如何创建并使用模板过滤器和标签，因为，Django的模板系统包含内容极广，因此可以有更多的东西对不同的应用场景来添加。  

##第六章，**模型管理**
本章，将指导你通过使用自定义的功能来扩展默认admin，

##第七章，**Django CMS**

##第八章，**分层结构**

##第九章，**数据的导入和导出**

##第十章，**附加功能**
