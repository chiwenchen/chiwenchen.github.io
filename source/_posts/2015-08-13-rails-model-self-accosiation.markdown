---
layout: post
title: "Rails Model - Self association"
date: 2015-08-13 19:00:39 +0800
comments: true
categories: 
---
In Facebook, Twitter or other social netwrking website, we can easilly follw someone, make friend with someone. this is quite a common feature in nowaday, and intuitively as well, but down to the code level, you will find it's not so simple as appearance. to do this, we will need `self accosiation` to accomplish it, this is a technique allowing us to make one object accosiate with another object in the same model.

In rails, basically we need two concept to achive this goal, one is the HMT stands for `has many through` accosiation and customize arguments.

<!-- more -->

Let's think of a scenario. Tom are in a online videos store, and he want to be able to follow someone who always makes great comments on movies, because the critic helps Tom to make decision on pickng movies a lot.

To do so, first we need a new table called `Relationship`.

```ruby
$ rails g migration create_relationships
```

```
class CreateRelationships < ActiveRecord::Migration
  def change
    create_table :relationships do |t|
      t.integer :leader_id
      t.integer :follower_id
      t.timestamps
    end
  end
end
```

Run `rake db:migrate`

In this table, `leader` will be the critic in this scenario, and Tom are the `follower`, so this will offer us a place to record the relationships we create later.

Next step, we start to make accosiations in our model.

```
class User < ActiveRecord::Base

  has_many :following_relationships, class_name: 'Relationship', foreign_key: 'follower_id'
  has_many :leading_relationships, class_name: 'Relationship', foreign_key: 'leader_id'

end
```

By adding those code, we can do something in rails console:

```
tom = User.find_by(name: 'Tom')
tom.following_relationships
# this will return the relationships array that Tom is the follower.
```

By customize the `class_name` and `foreign_key`, we can skip the rails convention and use `following_relationships` as virtual attribute, it will actually look at the `Relationship` model and search for the item that the `follower_id` is same as the Tom's id.

On the other side in `Relationship` model, we add the code below:
```
class Relationship < Activerecord::Base
  belongs_to follower, class_name: 'User'
  belongs_to leader, class_name: 'User'
end
```

By adding this, we can do some manipulate in rails console too.

```
relationship = Relationship.first
relationship.follower
#this will return the user that the id is same as the follower_id of relationship object, same as .leader method
```