---
layout: post
title: "Internalization and Localization (Django)"
date: 2017-06-03
---

# Intro

When you make a web app, it may be necessary to have the website be available in multiple languages. One common use case is a webstore--if the webstore offers shipping and processing to multiple countries, it would be helpful make it language- and region-specific by listing items listed in the store in varying languages and currency types. 

{% include image.html url="/assets/images/localization_blog_post/endclothing.png" description="An example of a webstore that offers differing versions of its website depending on the country it is shipping to" style="width=80%" %}

A commonly used term that describes this process is _"localization"_--making your website accessible in another languages, with respect to cultural nuances and differences. You may commonly see it refered to as _"l10n"_, because there are 10 letters between "l" and "n" in "localization". Kind of a weird custom in my opinion, but its widespread and recognizable. Another term related to (and often used in conjunction with) "localization" is _"internalization"_, which is the process of setting up your website codebase in such a way that it supports easy localization. This definition is more fuzzy and big-picture, but generally that entails developing in such a way that makes coding for translations as easy and scalable as possible. "Internalization" is similarly nicknamed _"i18n"_, because there are 18 letters between "i" and "n". Again, kind of a weird nickname, but hey, it works. 

{% include image.html url="/assets/images/localization_blog_post/shrug.gif" description="" style="width=80%" %}

Luckily, Django has built-in functionality for localization and internalization that makes it simple to store, update, modify, and display translations. Let's explore how it works!

### Prerequisites/Assumptions: 
* You are using OS X or Linux
* You are comfortable with using Django 1.1 with Python 2.7
* You use `conda` or `virtualenv`

# Overview and Setup

In this tutorial, we will be creating a super simple proof-of-concept example page that will display text and have a button that users can click to toggle the text between English and Filipino. To begin, we will setup a new virtual environment using `conda` or `virtualenv`. Commands for `conda`:
```bash
$ conda create --name translate python=2
$ source activate translate
$ pip install Django
```

Commands for `virtualenv`:
```bash
$ virtualenv -p /usr/bin/python2.7 translate
$ source translate/bin/activate
$ pip install Django
```

After, we will set up the Django project and `example` app, where we will create our example page as described before:
```bash
$ django-admin startproject translate
$ cd translate
$ python manage.py startapp example
```

Now, we should have a project directory structure that looks something like this:
```
└── translate
    ├── db.sqlite3
    ├── manage.py
    ├── example
    │   ├── migrations
    │   │   └── __init__.py
    │   ├── templates
    │   ├── __init__.py
    │   ├── admin.py
    │   ├── apps.py
    │   ├── models.py
    │   ├── tests.py
    │   └── views.py
    └── translate
        ├── __init__.py
        ├── settings.py
        ├── urls.py
        └── wsgi.py
```

At this stage, we can run `python manage.py runserver` and navigate to <a href="http://127.0.0.1:8000/">http://127.0.0.1:8000/</a> to double-check that Django shows its default landing page.

# Creating the Static Page

We will now begin creating our example page. In our templates folder, we will create a very simple `html` file called `translation_example.html` and set it up like so:
```html
<!DOCTYPE html>

<html>

<head>
  <title>Example</title>
</head>

<body>
  Hello world!
  <p>
    This is a paragraph
  </p>
  <p>
    Another paragraph
  </p>
  <p>
    Paragraph, part 3
  </p>
</body>

</html>
```
Plain and simple. As a sneak peek ahead, we will allow users to convert `Hello world!` and the other paragraphs from English to Filipino or vice-versa with a click of a button. 

# Rendering the Page

In order to have the template we just created show up when we enter <a href="http://127.0.0.1:8000/">http://127.0.0.1:8000/</a> into our browser, we must edit three files, `settings.py`, `urls.py` and `views.py`, as well as create another `urls.py` in our `example` app folder. 

First, we will edit `settings.py` to allow Django to "recognize" the template we just made. In `translate/settings.py`, we will add `os.path.join(BASE_DIR, 'example/atemplates')` to `TEMPLATES` so the end result looks like this:
```python
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [os.path.join(BASE_DIR, 'example/templates')],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]
```
This will let Django know that a known directory that holds templates is at `example/templates`, and it will look there for any relevant templates. 


After that, we will add a method to `examples/views.py` so we have a way for the [Django Controller](https://docs.djangoproject.com/en/1.11/faq/general/#django-appears-to-be-a-mvc-framework-but-you-call-the-controller-the-view-and-the-view-the-template-how-come-you-don-t-use-the-standard-names) to communicate with the template. We will edit our `views.py` so it looks like this:
```python
from django.shortcuts import render
from django.http import HttpResponse

# Create your views here.
def index(request):
    return render(request, "translation_example.html")
```
All our `index` function does is make an API call to render the html we just wrote. The function will get a bit more complex than this later, but not by much.

Next, we will create a `urls.py` file in `examples` to help with URL routing. Add the following to the `urls.py` file:
```python
from django.conf.urls import url
from example import views

urlpatterns = [
    url(r'^$', views.index, name='index'),
]
```

Finally, we will edit `translate/urls.py` to accomodate that newly created `examples/urls.py`. Edit the `translate/urls.py` to look like this:
```python
from django.conf.urls import include, url
from django.contrib import admin
import example

urlpatterns = [
    url(r'^$', include('example.urls')),
    url(r'^admin/', admin.site.urls),
]
```
This means that whenever a user enters <a href="http://127.0.0.1:8000/">http://127.0.0.1:8000/</a>, Django will process the URL in `translate/urls.py` then send the URL to `examples/urls.py` for further processing through the `include` function call. In `examples/urls.py`, Django then calls the `views.index` function, which renders the `translation_example.html` template in our browser through the `render` function call. 

Now, if we run `python manage.py runserver` and navigate to <a href="http://127.0.0.1:8000/">http://127.0.0.1:8000/</a>, we will hopefully see something like

{% include image.html url="/assets/images/localization_blog_post/samplepage.png" description="The page you should see at this point" style="width=80%" %}

# Overview to Django Translations

Now that we finished the page that we want to translate, we can actually get started understanding exactly _how_ Django does translations. 

At its core, Django's translation uses two things for its translation functionality: _hooks_, and `*.po` files. The hooks in Django are also called _translation strings_, which are literals on our website that we want translated. We as developers have to mark translation strings ourselves. `*.po` files are essentially mappings of translation strings to their corresponding string in a given language. The hooks and `*.po` files are then tied together with various things in the view. We will get to the details of everything later.

While clearly helping with localization, this setup also helps with internalization by making translations modular--due to the tags, the same templates and views can be used in other projects with different language translations without fear of conflicts, as long as the base language is the same. Making edits to the translation strings to the same project is relatively painless as well. 

# Translation Strings

Django provides [several ways](https://docs.djangoproject.com/en/1.10/topics/i18n/translation/#module-django.conf.urls.i18n) of marking strings as being translatable, but I will focus on just two today: marking in Python and marking in template code. 

## Marking in Python

To make a string in Python translatable, we use the function `ugettext()`. Conventionally, people import the function as `_()` to save typing, since on a website with many strings to be translated having many `ugettext()` function calls can make the code very bulky and cluttered. 

There is also a function `gettext()`, but `ugettext()` is more useful for us because it allows for unicode support. 

Making string as translatable is very simple: we just call `_()` with the translation string as an argument. For example,
```python
from django.utils.translation import ugettext as _

translation_string = _("this string is can now be be translated")
untranslated string = "this string will not be picked up by Django for translation"
```

There is also a related fuction, `ugettext_lazy()`, that translates strings when they are accessed rather than when they are called. The translation itself will be done when the string is used in a string context, such as in template rendering. This is efficient when used in `models.py` or `forms.py`

## Marking in Templates

To make a string in a Django template translatable, we use the tag `{% trans %}`. The `{% trans %}` tag can translate both strings and variables:
```html
<p>{% trans "this string can be translated %}</p>
<p>{% trans translation_var %}</p>
```






If you add a comment starting with the keyword `Translators` in the line directly preceding the translation, , 

