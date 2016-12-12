---
layout: post
title: "Rspec - Build Rspec and first Model test"
date: 2015-05-26 14:12:08 +0800
comments: true
categories: test
---
Rspec is a widespread BDD(Behaviour-Driven Development) tool for ruby. coding with Rspec, we can develop our application in TDD way. write testing code(also can be a spec document) and then write code to fulfill the requirement of testing code. once all the test passed, the code base is also finished. Here we will talk about how to set up Rspec environment and also write the Unit test for our model. 
<!-- more -->
<h2>Build up the Rspec test environment</h2>
in `Gemfile`
```rb
group :test, :development do 
  gem 'rspec-rails'
end
```
and then run `bundle install` to install the `rspec`.

```
rails g rspec:install
```

This will generate two documents and one folder: `spec` folder, `.rspec` and `spec/spec_helper.rb`.
Also another thing is to prepare our test database, because the test database is seperate from the development databse and will not interfere each other, but will share the same schema. To run below command generate the test database.

```
rake db:test:prepare
```

the `spec/spec_helper.rb` is the config file used to configure Rspec itself. Meanwhile, `spec` is where we will put our testing file. Now generate a folder `models` under `spec`. Here is to place all the <strong>Unit test</strong> for each models. Under the `models`, we can create a file called `PROJECTNAME_spec.rb`. Which the `PROJECTNAME` will same as your project name. In this case, we name it `myflix_spec.rb`.

```
require 'spec_helper' #this line is required in all test file. the rspec will load rails enviroment alos with this class.

describe Video do #'describe' is the syntax for test, and the Video is the model name matchs the model we are going to test.

  it 'save itself' do # 'it' is the saved word
    #1.  set up the stage(data)
    video = Video.save(title: 'Mission Impossible 3', description: 'Very cool movie')

    #2.  perform a action
    video.save

    #3.  verify the result
    Video.first.title.should == 'Mission Impossible 3'

  end

end

```
Next thing is to run `rspec` in terminal to see the result.

```
➜  myflix git:(master) ✗ rspec
.

Finished in 0.03922 seconds
1 example, 0 failures

Randomized with seed 18414
```




