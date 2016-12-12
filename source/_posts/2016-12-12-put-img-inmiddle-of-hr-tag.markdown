---
layout: post
title: "Put img in middle of hr tag"
date: 2016-12-12 15:16:31 +0800
comments: true
categories: 
---

This is one way to put image in the middle of hr

```haml
%hr.half
.step-img= image_tag "step-3.png"
%hr.half
```

```css
.half {
  width: 45%;
  display: inline-block;
}
.step-img {
  display: inline-block;
  width: 9%;
  img {
    display: block;
    margin: 0 auto;
    margin-top: -24px;
  }
}
```

