---
layout: post
title: "Rails Model Validations"
date: 2015-04-27 16:03:43 +0800
comments: true
categories: 
- Rails 
- Model
---
<strong>Rails Validations</strong> is used when we need to verify data before it stored into real database. 

<h2>Common Validations</h2>
Here will expose some very basic validation mechanisms.

<h4>Common Validations</h4>

This is to vefify if the user have key in value of the `:title` column, will fail if user leave it blank.
``` rb
validates :title, presence: true
```


