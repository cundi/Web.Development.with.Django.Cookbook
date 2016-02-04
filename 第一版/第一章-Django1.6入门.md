本章我们会学习到以下内容：  

* 使用虚拟环境  
* 创建一个项目文件结构  
* 用pip处理项目依赖  
* 在项目中包括外部的依赖  
* 在settings中定义相对路径  
* 为Subersion用户动态地配置STATIC_URL  
* 为Git用户动态地配置STATIC_URL  
* 创建并包括本地设置  
* 把UTF-8设置为MySQL配置的默认编码格式  
* 设置Subversion的忽略特性  
* 创建Git的忽略文件  
* 删除Python编译文件  
* Python文件中的导入顺序  
* 定义可重写的app设置  

## 引言
  

## 为子版本用户动态地设置STATIC_URL
如果你对`STATIC_URL`设置一个静态值，那么每次你更新CSS文件，JavaScript文件，或者图片都需要清除浏览器的缓存以应用改变。有一个  

## 预热
  
## 具体做法
  
## 实现原理
  
#### 参见
  
## 为Git用户动态地设置STATIC_URL
  
## 预热
确保你的项目处理Git版本控制器之下。
  
## 具体做法
  
## 实现原理
  
#### 参见
  
## 创建并包含本地设置
你不得不需要至少两个不同的项目实例：一个是创建新特性的开发环境，另一个是托管服务器中的公开网站环境。此外，可能会有针对其他开发者的不同的开发环境。你或许也需要有一个过渡性的环境以在一个类公开网站这样的情况下测试项目。  

## 预热
不同环境的大多数设置都会共享并保存在版本控制中。不过，这里仍人会有一些针对某些项目环境的特别设置，例如，数据库或者电子邮件设置。我们把它们都放进`local_settings.py`文件中。  

## 具体做法
执行以下步骤：  

1. 在`local_settings.py`的尾部添加声明在相同目录下的`local_settings.py`的描述：  

```python
#settings.py
# ... 把这些代码放到文件的结尾 ...
try:
    execfile(os.path.join(os.path.dirname(__file__), ';local_settings'))
except IOError:
    pass
```

2. 创建`local_settings.py`然后把特定的环境设置加入到文件中：  

```python
#local_settings.py
DATABASES = {
    "default": {
        "ENGINE": "django.db.backends.mysql",
        "NAME": "myproject",
        "USER": "root",
        "PASSWORD": "root",
    }
    }
}
EMAIL_BACKEND = "django.core.mail.backends.console.EmailBackend"
INSTALLED_APPS += (
    "debug_toolbar",
)
```

## 工作原理
如你所见，本地设置并没有正常地导入，它们却包含在`settings.py`中并被执行。这样不仅允许你创建或者重写存在的设置，而且也可以调整`settings.py`文件中元组或者列表；例如，这里我们添加`debug_toolbar`到`INSTALLED_APPS`以启用对SQL查询，模板上下文变量，等等的调试。  

## 参见
The Creating a project file structure recipe  
The Toggling Debug Toolbar recipe in Chapter 10, Bells and Whistles  

## 配置UTF_8作为MySQL配置的默认编码
  
## 预热
  

## 具体做法
  
## 工作原理
  
## 设置Subversion的忽略特性
  
## 预热
  
## 具体做法
打开命令行工具并社会默认编辑器为`nano，vi，vim`或者其他的任何你个人喜欢的编辑器：  

```python
$ export EDIOR=nano
```
  
>##### 提示
如果你还没有选择自己喜欢的编辑器，我推荐使用nano

  
## 工作原理
  
## 参见
  
## 创建Git的忽略文件
  
## 预热

## 具体做法
使用你最喜欢的文本编辑器，在Django项目的根目录下创建一个`.gitinore`文件，然后把这些文件和目录放进刚创建的文件中：  

```python
#.gitinore
*.pyc
/myproject/local_settings.py
/myproject/static/
/myproject/tmp/
/myproject/media/
```
  
## 工作原理
  
## 参见
The Setting the Subversion ignore property recipe
  
## 删除Python的编译文件
  
## 预热
  
## 具体做法
  
## 工作原理
  
## 参见
  
## Python文件中的导入顺序
当你创建Python模块时，保持文件内的结构一致是个好的做法。这样做可以让其他的开发者和你自己在阅读代码时相对轻松一些。这个方法会向你演示如何组织导入。  

## 预热
在Django项目中创建一个虚拟目录。  

## 具体做法
在你创建的Python文件中应用下面的结构。然后要做的就是，在第一行定义UTF-8作为默认的Python文件编码，并把分类的导入放进文件区域：  

```python
# -*- coding: UTF-8 -*-
# System libraries
import os
import re
from datetime import datetime

# Third-party libraries
import boto
from PIL import Image

# Django modules
from django.db import models
from django.conf import settings

# Django apps
from cms.models import Page

# Current-app modules
import app_settings
```
  
## 工作原理
如下，我们有五个主要的目录被导入：  

## 更多内容
  
## 参见
  
## 定义可重写的app设置
该做法会向你演示如何给应用定义设置，它可以在之后于项目的`settings.py`或者`local_settings.py`文件中被重写。对于可重复使用的应用该做法特别有效。  

## 预热
手动地创建Django应用，或者利用下面的这个命令：  

```python
(myproject_env)$ django-admin.py startapp myapp1
```
## 具体做法
如果你刚好有一个到两个设置，你可以在`models.py`中使用下面的模式。如果设置也很多，你刚好也想要更好的组织它们，那么你可以在应用中创建一个文件`app_settings.py`，然后以下列方式写设置：  

```python
#models.py or app_settings.py
# -*- coding: UTF-8 -*-
from django.conf import settings
from django.utils.translation import ugettext_lazy as _

SETTING1 = getattr(settings, "MYAPP1_SETTING1", u"default value")
MEANING_OF_LIFE = getattr(settings, "MYAPP1_MEANING_OF_LIFE", 42)
STATUS_CHOICES = getattr(settings, "MYAPP1_STATUS_CHOICES", (
    ('draft', _("Draft")),
    ('published', _("Published")),
    ('not_listed', _("Not Listed")),
))
```
  
然后，你可以在`models.py`中以下面的方法使用应用的设置：  

```python
#models.py
# -*- coding: UTF-8 -*-
from django.db import models
from django.utils.translation import ugettext_lazy as _

from app_settings import STATUS_CHOICES

class NewsArticle(models.Model):
    # ...
    status = models.CharField(_("Status"),
        max_length=20, choices=STATUS_CHOICES
    )
```
  
如果你想要为一个项目重写`STATUS_CHOICES`，你只需简单地打开`settings.py`并加入下面代码：  

```python
#settings.py
# ...
from django.utils.translation import ugettext_lazy as _
MYAPP1_STATUS_CHOICES = (
    ("imported", _("Imported")),
    ("draft", _("Draft")),
    ("published", _("Published")),
    ("not_listed", _("Not Listed")),
    ("expired", _("Expired")),
)
```

## 工作原理
Python函数，`getattr(object, attribute_name[, default_value])`，视图从`object`获取属性`attribute_name`，如果未找到属性则返回`default_value`。这个例子中，不同的设置从Django项目设置模块中被视图取回，如果这些设置没找到，则默认值被使用。  

