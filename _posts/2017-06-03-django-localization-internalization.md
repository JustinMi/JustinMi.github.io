---
layout: post
title: "Internalization and Localization (Django)"
date: 2017-06-03
---

# Intro

When you make a web app, it may be necessary to have the website be available in multiple languages. One common use case is a webstore--if the webstore offers shipping and processing to multiple countries, it would be helpful make it language- and region-specific by listing items listed in the store in varying languages and currency types. 

{% include image.html url="/assets/images/localization_blog_post/endclothing.png" description="An example of a webstore that offers differing versions of its website depending on the country it is shipping to" style="width=80%" %}

A commonly used term that describes this process is _"localization"_--making your website accessible in another languages, with respect to cultural nuances and differences. You may commonly see it refered to as _"l10n"_, because there are 10 letters between "l" and "n" in "localization". Kind of a weird custom in my opinion, but its widespread and recognizable. Another term related to (and often used in conjunction with) "localization" is _"internalization"_, which is the process of setting up your website codebase in such a way that it supports easy localization. This definition is more fuzzy and big-picture, but generally that entails developing in such a way that makes coding for translations as easy and scalable as possible. "Internalization" is similarly nicknamed _"i18n"_, because there are 18 letters between "i" and "n". Again, kind of a weird nickname, but it works. 


