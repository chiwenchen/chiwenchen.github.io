---
layout: post
title: "Rspec - Controller test"
date: 2015-06-24 14:47:36 +0800
comments: true
categories: test
---
To better implement controller, we can write specification(test) for each controller to define and test their behavior. The biggest benefit is we can have fully control of how controllers behave in all aspect without testing it manully, if we do not use Rspec to implement auto test, we have to use `rails console` or `browser` to test it by ourself. In other words, when we write Rspec, we have to make sure the spec is covering all the possibility, otherwise, we might be caught by some annoying bugs. 
<!-- more -->
<h2>Fabricator and Faker</h2>
To better way we test our code with rspec is to use both `fabricator` and `faker` along with our testing. this two gems work together can give us a powerful tool to make fake data for testing.

To install it, just add `gem fabricator` and `gem faker` in our Gemfile and run bundle install, and it will be all set.

<h2>Use Fabricator</h2>
To use fabricator, we need a folder to put all the fabricators, which build for each models. Add a folder under `app/spec` called `fabricators`.
Inside the folder, let's say we want to automatically generate fake data for `User` Model, so every time we need to have some data for testing purpose, we don't need to made up data ourself anymore. create a file called `User_fabricator.rb`. inside the file, put some code like this.
```rb
Fabricator(:video) do #video is the model name
    title {Faker::Lorem.words(4).join(" ")} #title is the parameter
    description {Faker::Lorem.sentence}
end
``` 
This is the typical fabricator with faker gem. To see more stuff what faker can create, visit <a href='https://github.com/stympy/faker'>Faker</a>

<!-- Once we give the rule what to attach to the object when calling fabricator, --> we then can use it like this in our test file.

```
Fabricator(:user)
```
This single line will give as a brand new object of `User` Model filled with fake data.

<h2>context, let and before</h2>

<h4>context</h4>

`context` is used when we have some common statement for each test item, then we can group it use `context`. for example.

```
describe "GET show" do 
  context 'with authenticated user' do 
    it 'sets the @video'
    it 'redirect_to video_path'
  end
  context 'user is not authenticated' do 
    it 'not sets the @video'
    it 'redirect_to signin_path'
  end
end
```

<h4>let</h4>

`let` is used to create a variable that can be used in a



<h2>Controller testing</h2>

To test controller, basiclly there are two functions we need to test. First is setting instance variable that is ready for view, second is to render template or redirect to other action.

Before that, let's create file and write out the basic structure.

create a folder called `controllers` under `app/spec`, inside the folder, create a file called `users_controller_spec.rb`. write the fundamental like this:

```
require 'spec_helper'

describe UsersControllers do 
  describe "GET show"
    #put test item here
  end
end
```
The first line is neccessary! line 3 shows which controller we are goning to test, and line 4 is identify which action we are going to test inside this code block. first we need to give the correct HTTP verb to match the route.rb, in this case, it's the `GET` and following is the name of the action.

Next, puts some test item here.
<h3>Test the instance object</h3>

```
...
describe "GET show" do
  it "sets @user" do 
    # 1. setup the data
    user = Fabricator(:user, name: 'Tom')
    # 2. execute the action
    get :show, id: user.id 
    # 3. compare the expect with actual result  
    expect(assigns(:user)).to eq(user)
  end
  it "render show template"
end
``` 
Line 5, the Fabricator will create a new object for us, but if we want the paricular parameter, we can overwrite it just like overwrite the name to Tom in this case.

Line 7 will send a fake request to action `show` with the params[:id] set to user.id.

Line 9, the `:user` equals `@user`, which is inside the `show` action, so we can verify if the returned object `:user`is the same as the user variable here as we expect.

<h3>Test render or redirect_to </h3>
```
...
describe "GET show" do
  it "redirect to the sign in page" do 
    user = Fabricate(:user)
    get :show, id: user.id
    expect(response).to redirect_to front_videos_path
  end
end
```
Here we alse need to set the instance variable and call the action. the only difference is we call `responce` reserverd word to specify that we want to verify the routing after the action. 

Generally, if the routing is given by rails by convention, then we will skip this test. For example, if the action looks like `def show; end`, then we won't need to test routing for this action.

<h3>Execute test in Terminal</h3>
running below comand will only execute the test for controllers.

```
rspec spec/controllers
```
running the below comand will only execute the test written in this particular file.
```
rspec spec/controllers/users_controller_spec.rb
```
running the below comand will only execute the test written in the line you specify.
for example, the test we want to run is this one:
```
it 'set up the @user'
  #testing code
end
```
then we can execute the test like this.
```
rspec spec/controllers/users_controller_spec.rb:1
```
