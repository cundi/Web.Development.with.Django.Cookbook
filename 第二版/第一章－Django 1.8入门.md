第一章 Django 1.8入门
******************************************

本章我们将讨论以下话题:

- 使用虚拟环境  
- 创建项目文件的结构  
- 使用pip处理依赖
- 让你的代码兼容Python2.7和Python 3  
- 在项目中包括外部依赖  
- 分别为开发、测试、过度和生产环境配置settings文件  
- 在settings中定义相对路径  
- 创建并包括本地settings  
- 为Subversion用户动态地设置STATIC_URL  
- 为Git用户动态地设置STATIC_URL  
- 设置UTF－8编码为MySQL的默认编码格式  
- 正确地设置Subversion的ignore内容  
- 创建一个Git的ignore文件  
- 删除Python编译文件  
- 考虑Python文件中的导入次序  

## 简介
In this chapter, we will see a few good practices when starting a new project with Django 1.8 on Python 2.7 or Python 3. Some of the tricks introduced here are the best ways to deal with the project layout, settings, and configurations. However, for some tricks, you might have to find some alternatives online or in other books about Django. Feel free to evaluate and choose the best bits and pieces for yourself while digging deep into the Django world.  

I am assuming that you are already familiar with the basics of Django, Subversion and Git version control, MySQL and PostgreSQL databases, and command-line usage. Also, I am assuming that you are probably using a Unix-based operating system, such as Mac OS X or Linux. It makes more sense to develop with Django on Unix-based platforms as the websites will most likely be published on a Linux server, therefore, you can establish routines that work the same while developing as well as deploying. If you are locally working with Django on Windows, the routines are similar; however, they are not always the same.  

## Working with a virtual environment
It is very likely that you will develop multiple Django projects on your computer. Some modules such as Python Imaging Library (or Pillow) and MySQLdb, can be installed once and then shared for all projects. Other modules such as Django, third-party Python libraries, and Django apps, will need to be kept isolated from each other. The virtualenv tool is a utility that separates all the Python projects in their own realms. In this recipe, we will see how to use it.

### Getting ready
To manage Python packages, you will need pip. It is included in your Python installation if you are using Python 2.7.9 or Python 3.4+. If you are using another version of Python, install pip by executing the installation instructions at http://pip.readthedocs.org/en/stable/installing/. Let's install the shared Python modules Pillow and MySQLdb, and the virtualenv utility, using the following commands:

```python
$ sudo pip install Pillow
$ sudo pip install MySQL-python
$ sudo pip install virtualenv
```

### How to do it…
Once you have your prerequisites installed, create a directory where all your Django projects will be stored, for example, `virtualenvs` under your home directory. Perform the following steps after creating the directory:  

1. Go to the newly created directory and create a virtual environment that uses the shared system site packages:  

```shell
$ cd ~/virtualenvs
$ mkdir myproject_env
$ cd myproject_env
$ virtualenv --system-site-packages .
New python executable in ./bin/python
Installing setuptools………….done.
Installing pip……………done.
```

2. To use your newly created virtual environment, you need to execute the activation script in your current shell. This can be done with the following command:  

```shell
$ source bin/activate
```

You can also use the following command one for the same (note the space between the dot and bin):  

```shell
$ . bin/activate
```

3. You will see that the prompt of the command-line tool gets a prefix of the project name, as follows:  

```shell
(myproject_env)$
```

4. To get out of the virtual environment, type the following command:  

```shell
$ deactivate
```

### How it works…
When you create a virtual environment, a few specific directories (`bin`, `build`, `include`, and `lib`) are created in order to store a copy of the Python installation and some shared Python paths are defined. When the virtual environment is activated, whatever you have installed with `pip` or `easy_install` will be put in and used by the site packages of the virtual environment, and not the global site packages of your Python installation.  

To install Django 1.8 in your virtual environment, type the following command:  

```shell
(myproject_env)$ pip install Django==1.8
```

### See also
- The Creating a project file structure recipe  
- The Deploying on Apache with mod_wsgi recipe in Chapter 11, Testing and Deployment  

## Creating a project file structure
A consistent file structure for your projects makes you well-organized and more productive. When you have the basic workflow defined, you can get in the business logic quicker and create awesome projects.  

### Getting ready
If you haven't done this yet, create a `virtualenvs` directory, where you will keep all your virtual environments (read about this in the Working with a virtual environment recipe). This can be created under your home directory.  

Then, create a directory for your project's environment, for example, `myproject_env`. Start the virtual environment in it. I would suggest adding the `commands` directory for local bash scripts that are related to the project, the `db_backups` directory for database dumps, and the `project` directory for your Django project. Also, install Django in your virtual environment.  

### How to do it…
Follow these steps in order to create a file structure for your project:  

1. With the virtual environment activated, go to the project directory and start a new Django project as follows:  

```shell
(myproject_env)$ django-admin.py startproject myproject
```

For clarity, we will rename the newly created directory as `django-myproject`. This is the directory that you will put under version control, therefore, it will have the `.git`, `.svn`, or similar directories.  

2. In the `django-myproject` directory, create a `README.md` file to describe your project to the new developers. You can also put the pip requirements with the Django version and include other external dependencies (read about this in the Handling project dependencies with pip recipe). Also, this directory will contain your project's Python package named `myproject`; Django apps (I recommend having an app called `utils` for different functionalities that are shared throughout the project); a locale directory for your project translations if it is multilingual; a Fabric deployment script named `fabfile.py`, as suggested in the Creating and using the Fabric deployment script recipe in Chapter 11, Testing and Deployment; and the `externals` directory for external dependencies that are included in this project if you decide not to use pip requirements.  

3. In your project's Python package, `myproject`, create the `media` directory for project uploads, the `site_static` directory for project-specific static files, the `static` directory for collected static files, the `tmp` directory for the upload procedure, and the `templates` directory for project templates. Also, the `myproject` directory should contain your project settings, the `settings.py` and `conf` directories (read about this in the *Configuring settings for development, testing, staging, and production environments recipe*), as well as the `urls.py` URL configuration.  

4. In your `site_static` directory, create the `site` directory as a namespace for site-specific static files. Then, separate the separated static files in directories in it. For instance, `scss` for Sass files (optional), `css` for the generated minified Cascading Style Sheets, `img` for styling images and logos, `js` for JavaScript, and any third-party module combining all types of files such as the tinymce rich-text editor. Besides the `site` directory, the `site_static` directory might also contain overwritten static directories of third-party apps, for example, `cms` overwriting static files from Django CMS. To generate the CSS files from Sass and minify the JavaScript files, you can use the CodeKit or Prepros applications with a graphical user interface.  

5. Put your templates that are separated by the apps in your templates directory. If a template file represents a page (for example, `change_item.html` or `item_list.html`), then directly put it in the app's template directory. If the template is included in another template (for example, `similar_items.html`), put it in the includes subdirectory. Also, your templates directory can contain a directory called `utils` for globally reusable snippets, such as pagination, language chooser, and others.  

### How it works…
The whole file structure for a complete project in a virtual environment will look similar to the following:  

img:omit  


### See also
The Handling project dependencies with pip recipe
The Including external dependencies in your project recipe
The Configuring settings for development, testing, staging, and production environments recipe
The Deploying on Apache with mod_wsgi recipe in Chapter 11, Testing and Deployment
The Creating and using the Fabric deployment script recipe in Chapter 11, Testing and Deployment

## Handling project dependencies with pip
The pip is the most convenient tool to install and manage Python packages. Besides installing the packages one by one, it is possible to define a list of packages that you want to install and pass it to the tool so that it deals with the list automatically.  

You will need to have at least two different instances of your project: the development environment, where you create new features, and the public website environment that is usually called the production environment in a hosted server. Additionally, there might be development environments for other developers. Also, you may have a testing and staging environment in order to test the project locally and in a public website-like situation.  

For good maintainability, you should be able to install the required Python modules for development, testing, staging, and production environments. Some of the modules will be shared and some of them will be specific. In this recipe, we will see how to organize the project dependencies and manage them with pip.  

### Getting ready
Before using this recipe, you need to have pip installed and a virtual environment activated. For more information on how to do this, read the *Working with a virtual environment* recipe.  

### How to do it…
Execute the following steps one by one to prepare pip requirements for your Django project:  

1. Let's go to your Django project that you have under version control and create the requirements directory with these text files: base.txt for shared modules, dev.txt for development environment, test.txt for testing environment, staging.txt for staging environment, and prod.txt for production.  

2. Edit base.txt and add the Python modules that are shared in all environments, line by line, for example:  

```python
# base.txt
Django==1.8
djangorestframework
-e git://github.com/omab/python-social-auth.git@6b1e301c79#egg=python-social-auth
```

3. If the requirements of a specific environment are the same as in the base.txt, add the line including the base.txt in the requirements file of that environment, for example:   

```python
# prod.txt
-r base.txt
```


4. If there are specific requirements for an environment, add them as shown in the following:  

```python
# dev.txt
-r base.txt
django-debug-toolbar
selenium
```

5. Now, you can run the following command in order to install all the required dependencies for development environment (or analogous command for other environments), as follows:  

```shell
(myproject_env)$ pip install -r requirements/dev.txt
```

### How it works…
The preceding command downloads and installs all your project dependencies from requirements/base.txt and requirements/dev.txt in your virtual environment. As you can see, you can specify a version of the module that you need for the Django framework and even directly install from a specific commit at the Git repository for the python-social-auth in our example. In practice, installing from a specific commit would rarely be useful, for instance, only when having third-party dependencies in your project with specific functionality that are not supported in the recent versions anymore.  

When you have many dependencies in your project, it is good practice to stick to specific versions of the Python modules as you can then be sure that when you deploy your project or give it to a new developer, the integrity doesn't get broken and all the modules function without conflicts.  

If you have already manually installed the project requirements with pip one by one, you can generate the requirements/base.txt file using the following command:  

```shell
(myproject_env)$ pip freeze > requirements/base.txt
```

### There's more…
If you want to keep things simple and are sure that, for all environments, you will be using the same dependencies, you can use just one file for your requirements named requirements.txt, by definition:  

```shell
(myproject_env)$ pip freeze > requirements.txt
```

To install the modules in a new environment simply call the following command:  

```shell
(myproject_env)$ pip install -r requirements.txt
```

>### NOTE
>If you need to install a Python library from other version control system or local path, you can learn more about pip from the official documentation at http://pip-python3.readthedocs.org/en/latest/reference/pip_install.html.  

### See also
The Working with a virtual environment recipe
The Including external dependencies in your project recipe
The Configuring settings for development, testing, staging, and production environments recipe

## Making your code compatible with both Python 2.7 and Python 3
Since version 1.7, Django can be used with Python 2.7 and Python 3. In this recipe, we will take a look at the operations to make your code compatible with both the Python versions.  

### Getting ready
When creating a new Django project or upgrading an old existing project, consider following the rules given in this recipe.  

### How to do it…
Making your code compatible with both Python versions consists of the following steps:  

1. At the top of each module, add from `__future__ import unicode_literals` and then use usual quotes without a `u` prefix for Unicode strings and a `b` prefix for bytestrings.  

2. To ensure that a value is bytestring, use the `django.utils.encoding.smart_bytes` function. To ensure that a value is Unicode, use the `django.utils.encoding.smart_text` or `django.utils.encoding.force_text` function.  

3. For your models, instead of the `__unicode__` method, use the `__str__` method and add the `python_2_unicode_compatible` decorator, as follows:  

```python
# models.py
# -*- coding: UTF-8 -*-
from __future__ import unicode_literals
from django.db import models
from django.utils.translation import ugettext_lazy as _
from django.utils.encoding import \
    python_2_unicode_compatible

@python_2_unicode_compatible
class NewsArticle(models.Model):
    title = models.CharField(_("Title"), max_length=200)
    content = models.TextField(_("Content"))

    def __str__(self):
        return self.title

    class Meta:
        verbose_name = _("News Article")
        verbose_name_plural = _("News Articles")
```

4. To iterate through dictionaries, use `iteritems()`, `iterkeys()`, and i`tervalues()` from `django.utils.six`. Take a look at the following:  

```python
from django.utils.six import iteritems
d = {"imported": 25, "skipped": 12, "deleted": 3}
for k, v in iteritems(d):
    print("{0}: {1}".format(k, v))
```

5. When you capture exceptions, use the `as` keyword, as follows:  

```python
try:
    article = NewsArticle.objects.get(slug="hello-world")
except NewsArticle.DoesNotExist as exc:
    pass
except NewsArticle.MultipleObjectsReturned as exc:
    pass
```

6. To check the type of a value, use `django.utils.six`, as shown in the following:  

```python
from django.utils import six
isinstance(val, six.string_types) # previously basestring
isinstance(val, six.text_type) # previously unicode
isinstance(val, bytes) # previously str
isinstance(val, six.integer_types) # previously (int, long)
```

7. Instead of `xrange`, use `range` from `django.utils.six.moves`, as follows:  

```python
from django.utils.six.moves import range
for i in range(1, 11):
    print(i)
```

8. To check whether the current version is Python 2 or Python 3, you can use the following conditions:  

```python
from django.utils import six
if six.PY2:
    print("This is Python 2")
if six.PY3:
    print("This is Python 3")
```

### How it works…
All strings in Django projects should be considered as Unicode strings. Only the input of `HttpRequest` and output of `HttpResponse` is usually in the UTF-8 encoded bytestring.  

Many functions and methods in Python 3 now return the iterators instead of lists, which make the language more efficient. To make the code compatible with both the Python versions, you can use the six library that is bundled in Django.  

Read more about writing compatible code in the official Django documentation at https://docs.djangoproject.com/en/1.8/topics/python3/.  

>### TIP
>Downloading the example code  

>You can download the example code files for all Packt books that you have purchased from your account at http://www.packtpub.com. If you purchased this book elsewhere, you can visit http://www.packtpub.com/support and register in order to have the files e-mailed directly to you.  

## Including external dependencies in your project
Sometimes, it is better to include external dependencies in your project. This ensures that whenever a developer upgrades third-party modules, all the other developers will receive the upgraded version in the next update from the version control system (Git, Subversion, or others).  

Also, it is better to have external dependencies included in your project when the libraries are taken from unofficial sources, that is, somewhere other than Python Package Index (PyPI), or different version control systems.  

### Getting ready
Start with a virtual environment with a Django project in it.  

### How to do it…
Execute the following steps one by one:  

1. If you haven't done this already, create an externals directory under your Django project django-myproject directory. Then, create the libs and apps directories under it.  

The libs directory is for the Python modules that are required by your project, for example, boto, Requests, Twython, Whoosh, and so on. The apps directory is for third-party Django apps, for example, django-cms, django-haystack, django-storages, and so on.  

>### TIP
>I highly recommend that you create the README.txt files in the libs and apps directories, where you mention what each module is for, what the used version or revision is, and where it is taken from.   

2. The directory structure should look something similar to the following:  

img:omit  


3. The next step is to put the external libraries and apps under the Python path so that they are recognized as if they were installed. This can be done by adding the following code in the settings:  

```python
# settings.py
# -*- coding: UTF-8 -*-
from __future__ import unicode_literals
import os
import sys

BASE_DIR = os.path.abspath(os.path.join(
    os.path.dirname(__file__), ".."
))

EXTERNAL_LIBS_PATH = os.path.join(
    BASE_DIR, "externals", "libs"
)
EXTERNAL_APPS_PATH = os.path.join(
    BASE_DIR, "externals", "apps"
)
sys.path = ["", EXTERNAL_LIBS_PATH, EXTERNAL_APPS_PATH] + \
    sys.path
```


### How it works…
A module is meant to be under the Python path if you can run Python and import that module. One of the ways to put a module under the Python path is to modify the sys.path variable before importing a module that is in an unusual location. The value of sys.path is a list of directories starting with an empty string for the current directory, followed by the directories in the virtual environment, and finally the globally shared directories of the Python installation. You can see the value of sys.path in the Python shell, as follows:  

```shell
(myproject_env)$ python
>>> import sys
>>> sys.path
```

When trying to import a module, Python searches for the module in this list and returns the first result that is found.  

Therefore, we first define the `BASE_DIR` variable, which is the absolute path to one level higher than the settings.py file. Then, we define the `EXTERNAL_LIBS_PATH` and `EXTERNAL_APPS_PATH` variables, which are relative to `BASE_DIR`. Lastly, we modify the `sys.path` property, adding new paths to the beginning of the list. Note that we also add an empty string as the first path to search, which means that the current directory of any module should always be checked first before checking other Python paths.

>### TIP
>This way of including external libraries doesn't work cross-platform with the Python packages that have C language bindings, for example, lxml. For such dependencies, I would recommend using the pip requirements that were introduced in the *Handling project dependencies with pip* recipe.  

### See also
The Creating a project file structure recipe
The Handling project dependencies with pip recipe
The Defining relative paths in the settings recipe
The Using the Django shell recipe in Chapter 10, Bells and Whistles

## Configuring settings for development, testing, staging, and production environments
As noted earlier, you will be creating new features in the development environment, test them in the testing environment, then put the website to a staging server to let other people to try the new features, and lastly, the website will be deployed to the production server for public access. Each of these environments can have specific settings and you will see how to organize them in this recipe.  

### Getting ready
In a Django project, we'll create settings for each environment: development, testing, staging, and production.  

### How to do it…
Follow these steps to configure project settings:  

1. In `myproject` directory, create a `conf` Python module with the following files: `__init__.py`, `base.py` for shared settings, `dev.py` for development settings, `test.py` for testing settings, `staging.py` for staging settings, and `prod.py` for production settings.  

2. Put all your shared settings in `conf/base.py`.  

3. If the settings of an environment are the same as the shared settings, then just import everything from `base.py` there, as follows:  

```python
# myproject/conf/prod.py
# -*- coding: UTF-8 -*-
from __future__ import unicode_literals
from .base import *
```

4. Apply the settings that you want to attach or overwrite for your specific environment in the other files, for example, the development environment settings should go to `dev.py` as shown in the following:  

```python
# myproject/conf/dev.py
# -*- coding: UTF-8 -*-
from __future__ import unicode_literals
from .base import *
EMAIL_BACKEND = \
    "django.core.mail.backends.console.EmailBackend"
```

5. At the beginning of the `myproject/settings.py`, import the configurations from one of the environment settings and then additionally attach specific or sensitive configurations such as `DATABASES` or `API` keys that shouldn't be under version control, as follows:  

```python
# myproject/settings.py
# -*- coding: UTF-8 -*-
from __future__ import unicode_literals
from .conf.dev import *

DATABASES = {
    "default": {
        "ENGINE": "django.db.backends.mysql",
        "NAME": "myproject",
        "USER": "root",
        "PASSWORD": "root",
    }
}
```

6. Create a `settings.py.sample` file that should contain all the sensitive settings that are necessary for a project to run; however, with empty values set.  

### How it works…
By default, the Django management commands use the settings from `myproject/settings.py`. Using the method that is defined in this recipe, we can keep all the required non-sensitive settings for all environments under version control in the conf directory. Whereas, the `settings.py` file itself would be ignored by version control and will only contain the settings that are necessary for the current development, testing, staging, or production environments.  

### See also
The Creating and including local settings recipe
The Defining relative paths in the settings recipe
The Setting the Subversion ignore property recipe
The Creating a Git ignore file recipe  

## Defining relative paths in the settings
Django requires you to define different file paths in the settings, such as the root of your media, the root of your static files, the path to templates, the path to translation files, and so on. For each developer of your project, the paths may differ as the virtual environment can be set up anywhere and the user might be working on Mac OS X, Linux, or Windows. Anyway, there is a way to define these paths that are relative to your Django project directory.  

### Getting ready
To start with, open `settings.py`.

### How to do it…
Modify your path-related settings accordingly instead of hardcoding the paths to your local directories, as follows:  

```python
# settings.py
# -*- coding: UTF-8 -*-
from __future__ import unicode_literals
import os

BASE_DIR = os.path.abspath(
    os.path.join(os.path.dirname(__file__), "..")
)

MEDIA_ROOT = os.path.join(BASE_DIR, "myproject", "media")

STATIC_ROOT = os.path.join(BASE_DIR, "myproject", "static")

STATICFILES_DIRS = (
    os.path.join(BASE_DIR, "myproject", "site_static"),
)

TEMPLATE_DIRS = (
    os.path.join(BASE_DIR, "myproject", "templates"),
)

LOCALE_PATHS = (
    os.path.join(BASE_DIR, "locale"),
)

FILE_UPLOAD_TEMP_DIR = os.path.join(
    BASE_DIR, "myproject", "tmp"
)
```

### How it works…
At first, we define `BASE_DIR`, which is an absolute path to one level higher than the `settings.py` file. Then, we set all the paths relative to `BASE_DIR` using the `os.path.join` function.  

### See also
- The Including external dependencies in your project recipe  

## Creating and including local settings
Configuration doesn't necessarily need to be complex. If you want to keep things simple, you can work with two settings files: `settings.py` for common configuration and `local_settings.py` for sensitive settings that shouldn't be under version control.  

### Getting ready
Most of the settings for different environments will be shared and saved in version control. However, there will be some settings that are specific to the environment of the project instance, for example, database or e-mail settings. We will put them in the `local_settings.py` file.  

### How to do it…
To use local settings in your project, perform the following steps:  

1. At the end of settings.py, add a version of local_settings.py that claims to be in the same directory, as follows:  

```python
# settings.py
# … put this at the end of the file …
try:
    execfile(os.path.join(
        os.path.dirname(__file__), "local_settings.py"
    ))
except IOError:
    pass
```

2 Create local_settings.py and put your environment-specific settings there, as shown in the following:  

```python
# local_settings.py
DATABASES = {
    "default": {
        "ENGINE": "django.db.backends.mysql",
        "NAME": "myproject",
        "USER": "root",
        "PASSWORD": "root",
    }
}

EMAIL_BACKEND = \
    "django.core.mail.backends.console.EmailBackend"

INSTALLED_APPS += (
    "debug_toolbar",
)
```

### How it works…
As you can see, the local settings are not normally imported, they are rather included and executed in the settings.py file itself. This allows you to not only create or overwrite the existing settings, but also adjust the tuples or lists from the settings.py file. For example, we add debug_toolbar to INSTALLED_APPS here in order to be able to debug the SQL queries, template context variables, and so on.  

### See also
- The Creating a project file structure recipe  
- The Toggling the Debug Toolbar recipe in Chapter 10, Bells and Whistles  

