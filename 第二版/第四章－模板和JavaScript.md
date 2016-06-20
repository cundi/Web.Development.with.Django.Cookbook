# Chapter 4. Templates and JavaScript

In this chapter, we will cover the following topics:  

Arranging the base.html template
Including JavaScript settings
Using HTML5 data attributes
Opening object details in a modal dialog
Implementing a continuous scroll
Implementing the Like widget
Uploading images by Ajax

## Introduction

We are living in the Web2.0 world, where social web applications and smart websites communicate between servers and clients using Ajax, refreshing whole pages only when the context changes. In this chapter, you will learn best practices to deal with JavaScript in your templates in order to create a rich user experience. For responsive layouts, we will use the Bootstrap 3 frontend framework. For productive scripting, we will use the jQuery JavaScript framework.  

## Arranging the base.html template

When you start working on templates, one of the first actions is to create the base.html boilerplate, which will be extended by most of the page templates in your project. In this recipe, we will demonstrate how to create such template for multilingual HTML5 websites with responsiveness in mind.  

>#### Tip
>Responsive websites are the ones that adapt to the viewport of the device whether the visitor uses desktop browsers, tablets, or phones.

### Getting ready

Create the templates directory in your project and set TEMPLATE_DIRS in the settings.  

### How to do it…

Perform the following steps:  

1. In the root directory of your templates, create a base.html file with the following content:  

```html
{# templates/base.html #}
<!DOCTYPE html>
{% load i18n %}
<html lang="{{ LANGUAGE_CODE }}">
<head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <title>{% block title %}{% endblock %}{% trans "My Website" %}</title>
    <link rel="icon" href="{{ STATIC_URL }}site/img/favicon.ico" type="image/png" />
   
    {% block meta_tags %}{% endblock %}
   
    {% block base_stylesheet %}
        <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.5/css/bootstrap.min.css" />
        <link href="{{ STATIC_URL }}site/css/style.css" rel="stylesheet" media="screen" type="text/css" />
    {% endblock %}
    {% block stylesheet %}{% endblock %}
   
    {% block base_js %}
        <script src="//code.jquery.com/jquery-1.11.3.min.js"></script>
        <script src="//code.jquery.com/jquery-migrate-1.2.1.min.js"></script>
        <script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.5/js/bootstrap.min.js"></script>
        <script src="{% url "js_settings" %}"></script>
    {% endblock %}
   
    {% block js %}{% endblock %}
    {% block extrahead %}{% endblock %}
</head>
<body class="{% block bodyclass %}{% endblock %}">
    {% block page %}
        <section class="wrapper">
            <header class="clearfix container">
                <h1>{% trans "My Website" %}</h1>
                {% block header_navigation %}
                    {% include "utils/header_navigation.html" %}
                {% endblock %}
                {% block language_chooser %}
                    {% include "utils/language_chooser.html" %}
                {% endblock %}
            </header>
            <div id="content" class="clearfix container">
                {% block content %}
                {% endblock %}
            </div> 
            <footer class="clearfix container">
                {% block footer_navigation %}
                    {% include "utils/footer_navigation.html" %}
                {% endblock %}
            </footer>
        </section>
    {% endblock %}
    {% block extrabody %}{% endblock %}
</body>
</html>
```

2. In the same directory, create another file named base_simple.html for specific cases, as follows:  

```html
{# templates/base_simple.html #}
{% extends "base.html" %}

{% block page %}
    <section class="wrapper">
        <div id="content" class="clearfix">
            {% block content %}
            {% endblock %}
        </div>
    </section>
{% endblock %}
```

### How it works…

The base template contains the <head> and <body> sections of the HTML document with all the details that are reused on each page of the website. Depending on the web design requirements, you can have additional base templates for different layouts. For example, we added the base_simple.html file, which has the same HTML <head> section and a very minimalistic <body> section; and it can be used for the login screen, password reset, or other simple pages. You can have separate base templates for single-column, two-column, and three-column layouts, where each of them extends base.html and overwrites the content of the <body> section.  

Let's look into the details of the base.html template that we defined earlier.  

In the <head> section, we define UTF-8 as the default encoding to support multilingual content. Then, we have the viewport definition that will scale the website in the browser in order to use the full width. This is necessary for small-screen devices that will get specific screen layouts created with the Bootstrap frontend framework. Of course, there is a customizable website title and the favicon will be shown in the browser's tab. We have extendable blocks for meta tags, style sheets, JavaScript, and whatever else that might be necessary for the <head> section. Note that we load the Bootstrap CSS and JavaScript in the template as we want to have responsive layouts and basic solid predefined styles for all elements. Then, we load the JavaScript jQuery library that efficiently and flexibly allows us to create rich user experiences. We also load JavaScript settings that are rendered from a Django view. You will learn about this in the next recipe.  

In the <body> section, we have the header with an overwritable navigation and a language chooser. We also have the content block and footer. At the very bottom, there is an extendable block for additional markup or JavaScript.  

The base template that we created is, by no means, a static unchangeable template. You can add to it the elements that you need, for example, Google Analytics code, common JavaScript files, the Apple touch icon for iPhone bookmarks, Open Graph meta tags, Twitter Card tags, schema.org attributes, and so on.  

### See also

- The Including JavaScript settings recipe

## Including JavaScript settings

Each Django project has its configuration set in the conf/base.py or settings.py settings file. Some of these configuration values also need to be set in JavaScript. As we want a single location to define our project settings, and we don't want to repeat the process when setting the configuration for the JavaScript values, it is a good practice to include a dynamically generated configuration file in the base template. In this recipe, we will see how to do that.  

### Getting ready

Make sure that you have the media, static, and request context processors set in the TEMPLATE_CONTEXT_PROCESSORS setting, as follows:  

```python
# conf/base.py or settings.py
TEMPLATE_CONTEXT_PROCESSORS = (
    "django.contrib.auth.context_processors.auth",
    "django.core.context_processors.debug",
    "django.core.context_processors.i18n",
    "django.core.context_processors.media",
    "django.core.context_processors.static",
    "django.core.context_processors.tz",
    "django.contrib.messages.context_processors.messages",
    "django.core.context_processors.request",
)
```

Also, create the utils app if you haven't done so already and place it under INSTALLED_APPS in the settings.  

### How to do it…

Follow these steps to create and include the JavaScript settings:  

1. Create a URL rule to call a view that renders JavaScript settings, as follows:  

```python
# urls.py
# -*- coding: UTF-8 -*-
from __future__ import unicode_literals
from django.conf.urls import patterns, include, url
from django.conf.urls.i18n import i18n_patterns

urlpatterns = i18n_patterns("",
    # …
    url(r"^js-settings/$", "utils.views.render_js",
        {"template_name": "settings.js"},
        name="js_settings",
    ),
)
```

2. In the views of your utils app, create the render_js() view that returns a response of the JavaScript content type, as shown in the following:  

```python
# utils/views.py
# -*- coding: utf-8 -*-
from __future__ import unicode_literals
from datetime import datetime, timedelta
from django.shortcuts import render
from django.views.decorators.cache import cache_control

@cache_control(public=True)
def render_js(request, cache=True, *args, **kwargs):
    response = render(request, *args, **kwargs)
    response["Content-Type"] = \
        "application/javascript; charset=UTF-8"
    if cache:
        now = datetime.utcnow()
        response["Last-Modified"] = \
            now.strftime("%a, %d %b %Y %H:%M:%S GMT")
        # cache in the browser for 1 month
        expires = now + timedelta(days=31)

        response["Expires"] = \
            expires.strftime("%a, %d %b %Y %H:%M:%S GMT")
    else:
        response["Pragma"] = "No-Cache"
    return response
```

3. Create a settings.js template that returns JavaScript with the global settings variable, as follows:  
 
```python
# templates/settings.js
window.settings = {
    MEDIA_URL: '{{ MEDIA_URL|escapejs }}',
    STATIC_URL: '{{ STATIC_URL|escapejs }}',
    lang: '{{ LANGUAGE_CODE|escapejs }}',
    languages: { {% for lang_code, lang_name in LANGUAGES %}'{{ lang_code|escapejs }}': '{{ lang_name|escapejs }}'{% if not forloop.last %},{% endif %} {% endfor %} }
};
```

4. Finally, if you haven't done it yet, include the rendered JavaScript settings file in the base template, as shown in the following:  

```html
# templates/base.html
<script src="{% url "js_settings" %}"></script>
```

### How it works…

The Django template system is very flexible; you are not limited to using templates just for HTML. In this example, we will dynamically create the JavaScript file. You can access it in your development web server at http://127.0.0.1:8000/en/js-settings/ and its content will be something similar to the following:  

```python
window.settings = {
    MEDIA_URL: '/media/',
    STATIC_URL: '/static/20140424140000/',
    lang: 'en',
    languages: { 'en': 'English', 'de': 'Deutsch', 'fr': 'Français', 'lt': 'Lietuvi kalba' }
};
```

The view will be cacheable in both server and browser.  

If you want to pass more variables to the JavaScript settings, either create a custom view and pass all the values to the context or create a custom context processor and pass all the values there. In the latter case, the variables will also be accessed in all the templates of your project. For example, you might have indicators such as {{ is_mobile }}, {{ is_tablet }}, and {{ is_desktop }} in your templates, with the user agent string telling whether the visitor uses a mobile, tablet, or desktop browser.  

### See also

- The Arranging the base.html template recipe
- The Using HTML5 data attributes recipe

## Using HTML5 data attributes

When you have dynamic data related to the DOM elements, you need a more efficient way to pass the values from Django to JavaScript. In this recipe, we will see a way to attach data from Django to custom HTML5 data attributes and then describe how to read the data from JavaScript with two practical examples. The first example will be an image that changes its source, depending on the viewport, so that the smallest version is shown on mobile devices, the medium-sized version is shown on tablets, and the biggest high-quality image is shown for the desktop version of the website. The second example will be a Google Map with a marker at a specified geographical position.  

### Getting ready

To get started, perform the following steps:  

1. Create a locations app with a Location model, which will at least have the title character field, the slug field for URLs, the small_image, medium_image, and large_image image fields, and the latitude and longitude floating-point fields.  

>#### Tip
>The term slug comes from newspaper editing and it means a short string without any special characters; just letters, numbers, underscores, and hyphens. Slugs are generally used to create unique URLs.

2. Create an administration for this model and enter a sample location.
3. Lastly, create a detailed view for the location and set the URL rule for it.

### How to do it…

Perform the following steps:  

1. As we already have the app created, we will now need the template for the location detail:  

```html
{# templates/locations/location_detail.html #}
{% extends "base.html" %}

{% block content %}
  <h2>{{ location.title }}</h2>

  <img class="img-full-width"
    src="{{ location.small_image.url }}"
    data-small-src="{{ location.small_image.url }}"
    data-medium-src="{{ location.medium_image.url }}"
    data-large-src="{{ location.large_image.url }}"
    alt="{{ location.title|escape }}"
  />

  <div id="map"
    data-latitude="{{ location.latitude|stringformat:"f" }}"
    data-longitude="{{ location.longitude|stringformat:"f" }}"
  ></div>
{% endblock %}

{% block extrabody %}
  <script src="https://maps-api-ssl.google.com/maps/api/js?v=3"></script>
  <script src="{{ STATIC_URL }}site/js/location_detail.js"></script>
{% endblock %}
```

2. Besides the template, we need the JavaScript file that will read out the HTML5 data attributes and use them accordingly, as follows:  

```js
//site_static/site/js/location_detail.js
jQuery(function($) {

function show_best_images() {
  $('img.img-full-width').each(function() {
    var $img = $(this);
    if ($img.width() > 1024) {
      $img.attr('src', $img.data('large-src'));
    } else if ($img.width() > 468) {
      $img.attr('src', $img.data('medium-src'));
    } else {
      $img.attr('src', $img.data('small-src'));
    }
  });
}

function show_map() {
  var $map = $('#map');
  var latitude = parseFloat($map.data('latitude'));
  var longitude = parseFloat($map.data('longitude'));
  var latlng = new google.maps.LatLng(latitude, longitude);

  var map = new google.maps.Map($map.get(0), {
    zoom: 15,
    center: latlng
  });
  var marker = new google.maps.Marker({
    position: latlng,
    map: map
  });
}show_best_images();show_map();

$(window).on('resize', show_best_images);

});
```

3. Finally, we need to set some CSS, as shown in the following:  

```css
/* site_static/site/css/style.css */
img.img-full-width {
    width: 100%;
}
#map {
    height: 300px;
}
```

### How it works…

If you open your location detail view in a browser, you will see something similar to the following in the large window:  

![img](images/)
