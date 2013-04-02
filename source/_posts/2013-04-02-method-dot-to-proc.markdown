---
layout: post
title: "Method.to_proc"
date: 2013-04-02 16:15
comments: true
categories: 
---

Most people don't know about ruby's builtin Method.to_proc.
Sure, we all know Rails' symbol.to_proc: 

``` ruby
people.collect(&:firstname)
```

But Method.to_proc is also quite handy.
Let me show you.

<div class='vimeo_wrapper'>
  <iframe src='http://ascii.io/a/2591/raw'></iframe>
</div>

Pretty awesome right?
