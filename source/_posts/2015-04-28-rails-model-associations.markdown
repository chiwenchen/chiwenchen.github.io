---
layout: post
title: "Rails Model Associations"
date: 2015-04-28 15:49:13 +0800
comments: true
categories: 
- Rails 
- Model
---
Here I will record associations syntax for fast reference.

<h2>1 : M Association</h2>

<strong>Use Case: A user can have many posts.</strong>

`user` is <em>one</em> side:
``` rb
class User < ActiveRecord::Base
	has_many :posts
end
```
`post` is <em>many</em> side:
```
class Post < ActiveRecord::Base
	belongs_to :user
end
```
<!-- more -->

<h2>M : M Association</h2>

There are two different ways to implement M : M association, `has_and_belongs_to_many` and `has_many :through`.

Here we will only use `has_many :through`, aka `hmt`, because this will give us more flexibility in future.

<strong>Use case: A category can have many posts, also a posts can have many categories</strong>

`category` side:
```
class Category < ActiveRecord::Base
	has_many :post_categories
	has_many :posts, through: :post_categories
end
```
`post` side:
```
class Post < ActiveRecord::Base
	has_many :post_categories
	has_many :posts, through: :post_categories
end
```

Here is the critical part of the M : M association, we have to create a bridge called `post_category.rb` file. Let's config this association.
```
class PostCategory < ActiveRecord::Base
	belongs_to :post
	belongs_to :category
end
```
Let's it!! the M : M association is now connected

<h2>Polymorphic Association</h2>

`polymorphic` is used when a model can belongs to more than one other model.

<strong>Use case: You can vote on either post or comment</strong>

`vote` side:
```
class Vote < ActiveRecord::Base
	belongs_to :voteable, polymorphic: true
end
```

`post` side:
```
class Post < ActiveRecord::Base
	has_many :votes, as: :voteable
end
```
By creating this type of connection, we will get two columns in `votes` table. `voteable_type` and `voteable_id`

and we can manipulate DB, for example:
```
$ post = Post.first
$ post.votes << Vote.create(voter: User.first)
```
or 
```
$ vote = Vote.new(voter: User.first)
$ vote.voteable = Post.first
$ vote.save
```
either will create a data in DB to record that we have a vote for first post.
