---
layout: post
title: "Markdown spell check in Sublime"
description: ""
category: 
tags: ["tutorial", "sublime"]
---
{% include JB/setup %}

Okay. There were tons of typos in my previous post, despite all reviews and fixes. So if something can be done automatically, it should be done automatically.
Apparently Sublime has built in spelling checker and it's very easy to enable it for the certain type of files, depending on their syntax. All you need to do is to 
go to the `Preferences -> Settings-More -> Syntax Specific - User` while editing your .md file and add following code there

```json
{
  "spell-check": true
}
```

It will automatically create Markdown.sublime-settings file and enable spell check for all markdown files.

P.S. I'll stand you a beer if you find any typo in this post. :)