---
layout: post
title: "Rails basic controller"
date: 2015-05-13 11:53:23 +0800
comments: true
categories: 
- Rails 
- Controller
---
`C` stands for Controller in the entire `MVC` structure, it basically has two different types of methods including `action` and `helper`. `action` methods handle request routed by url, interact with model and then give response back to the browser. and the `helper` methods is used by the `action` method.
<!-- more -->
In general, controller act the middle man between views and models.

<h2>Controller file and basic content</h2>

By Rails convention, the file name of controller is snakecase and plural, for instance `posts_controller.rb` and will place under the `controllers` folder. so the file structure looks like.
``` rb
PROJECT FOLDER
	-> app
		-> controllers
  		-> posts_controller.rb
```
Next, we have to define the class inside the `.rb` file.

``` rb
class PostsController < ApplicationController
	#the class name must be CamelCase and plural.
end
```
Next, inside the `.rb` file, controller will have basic seven actions defined by RESTFUL structure which are `index`, `show`, `new`, `create`, `edit`, `update` and `destroy`, but be remind that the actions are not limited to this seven basic actions according to particular cases.

<h3>Index action</h3>
`index` action is used to list all the objects of the resource, for example, we can use index to list out all the posts in our webapp. so here is how we define index action.
```
def index
	@post = Post.all
end
```
Here we only do one simple thing(actually two, another one is hidden). we retrive all the instance objects of the Post class and store it to the `@post` instance variable. 

Another thing we do is not shown here that is render the index template. By Rails convention, the controller action will automatically render the template which is sharing the same name of itself. in this case:
```
PROJECT FOLDER
	-> app
		-> views
			-> posts
				-> index.html.erb
```
if we write it out, the method will like this:
```
def index
	@post = Post.all
	render 'posts#index'
end
```
`index` action is done its job! receive the request, interact with DB to retrive data and pass the data to relative template(views).

<h3>Show action</h3>
`show` action is used when we want to deal with single object of the resource, like if we want to display one particular post.
```
def show
	@post = Post.find(params[:id])
end
```
Very similar to the index action, but we retrive the particular post through the information given by params.

`params` is a hash like data structure which is part of the url passing by router. the data will look something like this: 
```
params = {id: 1, title: 'some title', body: 'some content', created_at: '20150101'}
```
So here we can use `find` method to get the right object we want by having the `id` information.

Again, the `show` action will render the template to preceed the information.

<h3>New and Create action</h3>
`new` action is only used to render the form template, so that the user can type data. If we want to create a new post, we will need a form to put in those inforamtion.
```
def new
	@post = Post.new
end 
```
One simple thing happens here, we `new` a object and store it to instance variable `@post` and then pass it to the form template.

When the user complete the form and hit `submit` (See the <a href = '../model-backed-form'>model backed form</a> tutorial here), the routing mechanism will send a `POST` request and route to 'create' action along with the `params`.

In `create` action, we have to store the information into DB and redirect or render basing on the saving condition.

``` rb
def create
	@post = Post.new(strong_params)
	if @post.save
		#HAPPY PATH
		flash[:notice] = 'You have created a new post!'
		redirect_to root_path
	else
		#NO HAPPY PATH
		render 'new'
	end
end
```
Here shows if the `@post` is saved, then show a notice message to the user and redirect to somewhere else. 

If not, render the template again, so the user will be able to fill in the correct information again. (in the `new` template, it should have some machenism to demonstrate the error message to the user, here is introduction about how to show <a href = '../error-message-for-model-backed-form'>error message for the model backed form</a>)

<h3>Edit and Update action</h3>

`edit` is the action similar to `new` used for rendering a form for user to fill in some information, but at this time, it updates a existing object.

```
def edit
	@post = Post.find(params[:id])
end
```  
After 'edit' action, `update` action is executed just like the `create` action to overwrite the data in DB and then redirect or render base on the update condition.

```
def update
	@post = Post.find(params[:id])
	if @post.update(strong_params)
		#HAPPY PATH
		flash[:notice] = 'You have updated this post!'
		redirect_to post_path(@post)
	else
		#NO HAPPY PATH
		render 'edit'
	end
end
```

<h3>Destroy action</h3>

`destroy` action is used to delete the particular data from the DB.

```
def destroy
	Post.destroy(strong_params)
end
```



























































