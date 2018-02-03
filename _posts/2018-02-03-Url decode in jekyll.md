---
layout: post
title: Url decode in jekyll
categories: jekyll ruby
description: Url decode in jekyll
keywords: Url decode in jekyll
---

# Url decode in jekyll

## Create file at `_plugins/url_decode.rb`

{% gist cff99d06a92d5bb3ca19c45fd09d27f1 %}

## Usage

``` ruby
{{ page.url | url_decode }}
```