---
layout: post
title: "Rails Partial"
date: 2015-04-27 17:40:37 +0800
comments: true
categories: 
- Rails
- View
---
`Partial` is used when we want to DRY our template code, and put the repitition code in one place. so we can use it later whenever we need it.
<!-- more -->

<h2>Simple Partial</h2>
Let's say we want to extract a form used for both create user and update user. firstly we create a folder called `shared` under `views`. and then create a file called `_form_html_erb` under `shared`. so the file path will look like this.

`/views/shared/_form_html_erb`

Here, the folder name can be anything, even if this partial will be only used by a group of template which under the same folder, we don't even need to create another new folder, just put inside the original one.
But the name of the partial is having some convention. First, <strong>must</strong> start with `_`, second, the name of the file is what we will use later to point to what we want to render.

Let's put some code inside the `_form.html.erb`
```ruby
<%= form_for @post do |f|%> 

  <%= f.label :title %> 
  <%= f.text_field :title %>

  <%= f.label :url %> 
  <%= f.text_field :url %>

  <%= f.submit button %> 

<% end %>   
```
OK, in the `new_html_erb` template, now we can render this partial.
```
render '/shared/form', button: 'Create Post'
``` 
As you can see, now we don't need to put `_` before form. the `button` is the variable that we need to give it a attribute everytime we render this form, otherwise, an error will occur.

<h2>Collection Partial</h2>
Collection Partial is used when the code been renderd is a iterate loop. then we can extract the main body, and let the collection partial convention do the iterate for us.
For instance, we have to iterate all the posts belongs to particular user, we will write code something like this:
```
<%= @user.posts.each do |post| %>
	<%= post.title %>
	<%= post.body %>
<% end %>
``` 
If we want to extract it to a partial file, we then create a folder called `posts` under `views`, and under the `posts`, we create a file `_post.html.erb`.
open `_post.html.erb` in text editor.
```
	<%= post.title %>
	<%= post.body %>
```
In the template where you want to render this partial, let's say in the `index.html.erb`.
```
<%= render @user.posts %>
```
Let's it. Done!

OK, let's look closer how the convention works! 

Firstly, the variable been iterate here is the `|post|`, so the folder name must be the plural of the `post` which is `posts`, and the file's name must be the singular which is `post.html.erb`.

Secondly, the content inside the `_post.html.erb` is the body code of the loop block. No need to put the whole iteration here.

Finally, once you want to render this partial, just put in the variable, in this case `@user.posts`, then ruby will know where to find the partial, and give the full template back for you.



