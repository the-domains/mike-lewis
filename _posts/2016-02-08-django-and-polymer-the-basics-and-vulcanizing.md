---
inFeed: true
hasPage: true
inNav: false
inLanguage: null
starred: false
keywords: []
description: ''
datePublished: '2016-02-09T02:43:46.604Z'
dateModified: '2016-02-09T02:43:41.203Z'
title: 'Django and Polymer: The basics and Vulcanizing'
author: []
sourcePath: _posts/2016-02-08-django-and-polymer-the-basics-and-vulcanizing.md
published: true
authors: []
publisher:
  name: null
  domain: null
  url: null
  favicon: null
url: django-and-polymer-the-basics-and-vulcanizing/index.html
_type: Article

---
![](https://the-grid-user-content.s3-us-west-2.amazonaws.com/05456f21-c8f5-46a7-93ff-cdb72200d8c0.jpg)

## Polymer

If you haven't taken a look at Google's [Polymer][0] and [Material Design][1] yet, you should. If only because Google is pushing for it and that will undoubtedly have some reflection in search rankings.

The idea behind Polymer is the usage of web components; everything as an HTML tag. For example, instead of something like this for tabbed navigation:

You might have something like this instead:

## Getting Started

Make sure that you've done the following first:

* Set-up your standard Django project
* Install nodejs
* Install bower

Once you have that done, cd into your static directory, start a bower project, and install polymer:

The above will install the Polymer foundation with the core elements and paper elements. See[here][2]for reference on the built-in elements.

## Polymer with Django

Now to the Django integration. We are_not_going to be using Django as a standard MVC here, we will not use Django to process the template. Firstly, Django templates and Polymer share usage of the double bracket "{{". This causes problems when the template is passed through the template processor. Granted, you can always surround anything Polymer with the Django "{% verbatim %}" template tag to fix this but this is annoying. Secondly, this style of web app architecture is not really in line with how Polymer meant to be used. Due to all of this, we will be using Django solely for API calls.

If you are using Nginx this is as easy as pointing / at your template root and something like /api/ at uwsgi or whatever you use for Django. Your Nginx config might look something like this:

## The First Template

A good place to start is with[core-scaffold][3]. This will get you a basic web app layout.

## Vulcanizing

As you begin adding more Polymer elements, you'll notice an increase in the number of requests due to the includes. To fix this, you can use[Vulcanize][4]. This will concatenate your various includes and add them directly to your file.

To install:

`sudo npm install -g vulcanize`

In order for this to work, the includes have to use relative paths. So we need to change this:

To this:

This works for me because my static directory is adjacent to my template directory. Now that you have that set-up, you run the following command:

`vulcanize -o build.html index.html`

In this case, the output will be build.html using index.html as the input. Because of this, I rename my index.html and then output to index.html.

[0]: https://www.polymer-project.org/
[1]: http://www.google.com/design/spec/material-design/introduction.html
[2]: https://www.polymer-project.org/docs/elements/
[3]: https://www.polymer-project.org/docs/elements/core-elements.html#core-scaffold
[4]: https://github.com/polymer/vulcanize