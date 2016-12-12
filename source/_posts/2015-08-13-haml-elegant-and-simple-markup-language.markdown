---
layout: post
title: "HAML - elegant and simple markup language"
date: 2015-08-13 23:29:23 +0800
comments: true
categories: 
---
`ERB` is the most common markup language for rails, but since it inherit most of the script from `HTML`, it will becomes bulky. so here is why we can use `haml` alternatively.

Differ from `erb`, haml use as simple as possible script to describe the markup, and some of the convention to even simpler the code. We will demonstrate main deviation here to show the power of `hame`. It might seems odd at first, but get used to it, it will really help you to speed up and higher the readability.

<!-- more -->

```ruby
ERB
<strong><%= user.name %></strong>
```

```haml
HAML
%strong= user.name
```

Here shows two ability `haml` gives us, one is it use `%` instead of the open and close tag, and this works for every tag in html, `%body`, `%div`, `%a`...you name it. Second, instead using `<%= %>` to wrap the ruby code, it uses `=`, anything after equal sign will consider as ruby script.

```ruby
ERB
<strong class= 'code', id='message'>HELLO WORLD</strong>
```

```haml
HAML
%strong.code#message HELLO WORLD
```

Because `class` and `id` are so common in template file, so we use `.` to represent `class=` and `#` to represent `id=`, and also notice that there is only a space before `HELLO WORLD`, so it is just a normal string, not a ruby code.

```ruby
ERB
<div class= 'content'>HELLO WORLD</div>
```

```haml
HAML
.content HELLO WORLD
```

Also the `div` is so popular, so this time, haml make it even easier, just ignore it, and haml will consider it as div by default!

```ruby
ERB
<% if current_user %>
  <%= "Hi, #{current_user.name} %>
<% end %>
```

```haml
HAML
- if current_user.empty
  = "Hi #{current_user.name}"
```

If we want insert ruby logic into the template, and do not want to show it in screen, we use `-` before the logic, also unlike erb will need a `<% end %>`, we can just leave it, instead, haml uses indentation to identify the scope relation.

You can see how `haml` helps to keep code simpler and reduce noises, but here only demonstrate the most common features of `haml`, there are lot more in <a herf= 'haml.info/docs/yardoc/file.REFERENCE.html'>HAML REFERENCIAL</a>. check it out if you want to learn more. 

