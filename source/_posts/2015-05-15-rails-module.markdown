---
layout: post
title: "Rails Module"
date: 2015-05-15 10:08:00 +0800
comments: true
categories: 
---
`DRY` is a very important concept in Rails, we can use `partial` to extrat `Views`'s code. To extract `Controllers`'s code can be put in the `application_controller.rb` file. But how about extract the code in `Model` layer? the answer is `Module`.
<!-- more -->
<h2>Create a path to place all modules in safe</h2>
```
# Application Configuration should go into files in config/initializers
# -- all .rb files in that directory are automatically loaded.
```
According to this two lines in the file `application.rb`, Every files inside the initializers will fire before the application starts. so here might be a place we can put our module in there. If we want to put somewhere else? we can add one extra line just under the default statement.

``` rb
config.autoload_paths += %W(#{config.root}/lib)
```
`config.autoload_path` is a array which store all the paths that will auto load when initial this app. The `config.root` means the root path of our app, in this case we add the `lib` folder into the list.

<h2>Normal metaprogramming style Module</h2>

now we can create a file under `lib` to put our module code. let's create a `voteable.rb` file. Next, write the below basic structure in the `.rb` file.
```
module Voteable #must be the same name as file name

  def self.included(base)
    #here to define the meta programming
  end

  module ClassMethods
    # here is the place to put the class methods
  end

  module InstanceMethods
    # here is the place to put the instance methods
  end

end
``` 
Let's say we want to extract all the code regarding voting function to this module, move all the instance methos to the InstanceMethods module.

```
module InstanceMethods
  def count_vote
    vote_up - vote_down
  end
  def vote_up
    self.votes.where(vote: true).size
  end
  def vote_down
    self.votes.where(vote: false).size
  end
  def vote_size
    self.votes
  end
end
```

Aftre which, some methods like association is the class level method, so we can put the association in the ClassMethods module.  

```
module ClassMethods
  def association
    has_many :votes, as: :voteable
  end
end
```
Last thing, we have to use meta programming to really integrate these modules into the classes, which needs these specific modules.
```
def self.included(base)
  base.send(:include, InstanceMethods)
  base.extend ClassMethods
  base.class_eval do
    association
  end
end
```
The line2 includes the `InstanceMethods` while line3 shows that base is extended by adding the `ClassMethods`, from line4 to line6 fire up the modules when the Class is called.

<h2>Rails Concern</h2>
To simplify the code approach same function. we can use Rails Concern.

``` 
module Voteable 
  extend ActiveSupport::Concern

    included do
      has_many :votes, as: :voteable #polymorphic syntex.
    end 

    def count_vote
      vote_up - vote_down
    end
    def vote_up
      self.votes.where(vote: true).size
    end
    def vote_down
      self.votes.where(vote: false).size
    end
    def vote_size
      self.votes
    end
end
```
<h2>Passing in attribute to module</h2>
If the module is called by multiple Class, then we will not be allowed to hardcode the `ClassName` or the `argument`.

For example, if we want to make slug for both `Post` and `Comment` class. we will have to pass in the Class Name like `Post` or pass in the argument like `@post.title`. 













