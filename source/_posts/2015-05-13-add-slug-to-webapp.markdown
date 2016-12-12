---
layout: post
title: "Add Slug to Rails Webapp"
date: 2015-05-13 10:19:48 +0800
comments: true
categories: 
- Rails
- Route
---
By Rails default, if we retrive the object in DB and show related inforamtion to the browser like the `show action`, the url will show the <em>id</em> of the object as well. This is no good in production webapp in some reasons, one is the original information will exposed to the user, higher the risk web been hacked. for instance, user might able to keyin the id one by one to see all the posts in our website. another disadvantage is that the SEO will lower due to the irrelevant url. 

Lucky, we can solve the two issues by introduce slug into our website. <strong>Slug</strong> is a string that will allow us to show some information in the url replacing <em>id</em>. to firstly hide some sensitive information and also provide relatively useful information to user. 

before introducing slug, the url looks like:
```
http://my_web/post/1
``` 
after introducing slug:
```
http://my_web/post/good-post-about-food
```
Let's see how to implement slug.
<!-- more -->
<h3>Model</h3>
Firstly we need to create a column to store the slug for each objects, in the example here, we will create slug for Post model. in commend line:
```
rails generate migration add_slug_to_post
```
in the migration file:
``` rb
class AddSlugToPost < ActiveRecord::Migration
  def change
  	add_column :posts, :slug, :string
  end
end
```
we would like to simple generate slug by converte the title into some proper string. so def a `generate slug` method in the post model.
```
def generate_slug
  self.slug = self.title.gsub(' ', '_').downcase 
end
```
this will translate title `Good post about food` to slug `good-post-about-food`.
but we still need a mechanism to store the slug automatically when the post is create or even updated. So we need <a href = 'http://api.rubyonrails.org/classes/ActiveRecord/Callbacks.html'>`Callbacks`</a> method here.

```
before_save :generate_slug
``` 
By adding the link above, the `generate slug` method will be execute everytime before the object been saved. this means that the slug we generated will saved into DB as well.

One more method to add in model. we will use this method later in the `view` template when calling url named helper.

```
def to_param
	self.slug
end
```
<h3>View</h3>
When we want to create a link or button, this is the code we might write in our view template.

```
<%= link_to post.title, post_path(post), method: 'get' %> 
``` 
Here the `post` is the object of the `Post` class, and when we call named helper like `post_path(post)`, it is actually calling the `to_param` method for the `post` object to turn the object into `id`. and this `id` will show in the url as well as pass to the relative controller action to retrive the right information to give back the correct response to browser.

But the thing here is we want to show `slug` in our url instead of `id`. so that's why we overwrite the `to_param` method in the `Post` Model layer. to be more expressive, we can rewrite the named helper like so.
```
post_path(post.to_param)
```  
<h3>Controller</h3>
In controller, here is the original code to retrive information from DB.
```
@post = Post.find(params[:id])
```
But remember, for now our key is no longer `id`, instead, we use `slug`!!
```
@post = Post.find_by(slug: params[:id])
```
For now, the basic slug is successfully added to our webapp.