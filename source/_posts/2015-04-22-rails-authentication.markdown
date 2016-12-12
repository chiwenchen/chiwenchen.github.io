---
layout: post
title: "Rails Authentication"
date: 2015-04-22 15:42:06 +0800
comments: true
categories: 
- Rails
- Mechanism 
---
Here will expose how to add authentication into our entire web app. this will allow users to *Register*, *Login*, and *Logout*, also give users different access level as they logged in and loged out.

<h2>Register</h2>
<h3>Bcrypt Gem</h3>
To have a robust password system, we use bcrypt gem to provide a one way hash password storage system, this one way hash is not decryptable. to use it, firstly open `Gemfile` and add this gem.
``` rb
gem 'bcrypt-ruby', '~>3.0.0'
```
<!-- more -->
<h3>Route</h3>
To give user a page to filling the register form, first we need to add a route to register form url.
in `config/routes.rb`
```ruby
get '/register', to: 'users#new'
```
<h3>Model</h3>
First, run `rails g migration add_password_to_user`.
```
class AddPasswordToUser < ActiveRecord::Migration
  def change
  	add_column :users, :password_digest, :string
  end
end
```
Remember, it should be `password_digest`, NOT `password`, because we never store the really password directly in the database, so no one can access it directly even the administrator.

run `rake db:migrate`

Thanks to bcrypt, now we can add our one way hash to our user model. in `models/user.rb` add this line.
```
has_secure_password validations: false
```
Here we express `validations: false` is because we want keep bcrypt doing one thing only for us, we can add any validations we need later. however you can remove this expression to enable the default validation.

Now we can use `rails c` to see what we got.
```
user = User.new(username: 'Chiwen', password: 'password')
 => #<User id: nil, username: "Chiwen", created_at: nil, updated_at: nil, password_digest: "$2a$10$B7vmU6yHCNwRAJLafaAgYeeRi2A8d/7m5QkQf09tQxtj...", phone_number: nil, email: nil, gender: nil> 
```
See, the password has been transferd to a rendom string `password_digest` and ready to store in our database.

<h3>Controller</h3>
In controller, we only have to do one thing which is add `password` into our strong parameter.
```
(params.require(:user).permit(:username, :password, :phone_number, :email))
```

<h3>View</h3>
In the **model backed form**, add the below syntax to create a password input box.
``` erb
<%= f.label :password %>
<%= f.password_field :password %>
```

Now the register founction is ready to go.

<h2>Login and Logout</h2>
To implement this founction, we will need `session`(cookie) to help us remember the user current status, is he/she logged in? or logged out? Here is how to do it.

<h3>Route</h3>
We will need three routes this time. add those routes into `config/routes.rb`
First route is to render a form for user to keyin username and password so that they can log in.
``` rb
get '/login', to: 'sessions#new'
```
Seconde route is to send back `params` to server so that server can execute session controller create action to verity user or so.
```
post '/login', to: 'sessions#create'
```
Third route is simply route to destroy action to logout the user. 
```
get '/logout', to: 'sessions#destroy'
```

<h3>Model</h3>
Because we are using cookie instead of DB to record the user's login statue, so no DB at all.

<h3>Controller</h3>

<h4>New action</h4>
Unlike the typical `new` action need to newa object and pass it to template, here we do nothing, just let the rails convention take us to the `new` template.
```
def new
end
```
<h4>Create action</h4>
Here in the `create` action, we do two things, one is to verify if the information provided by client is matching any data in our database, another one is store the login information in `session`, aka `cookie` if user is authenticated, or redirect to other page if not.
```
def create
	user = User.find_by(name: params[:name])
	if user && user.authenticate(params[:password])
		#Happy path
		session[:user_id] = user.id
		flash[:notice] = 'You are now log-in!'
		redirect_to root_path
	else
		flash[:error] = 'Sorry, your username or password is uncorrect!'
		render 'sessions/new' # render the login template again
	end
end
``` 
<h4>Destroy action</h4>
We use `destroy` action to logout our user, only two simple thing to do, one is clean the session, another is tell the user that he/she is logged out and redirect he/she to another page.
```
def destroy
	session[:user_id] = nil
	flash[:notice] = 'You are now logged out, goodbye~'
	redirect_to root_path
end
```

<h3>View</h3>
<h4>new_html_erb</h4>
In `new_html_erb`, we need to generate a form for user to key in the information, so that we can pass that piece of information to controller to verify the user and so on.

As the result of we don't have DB for `session` controller, so we don't use model-backed form, instead, we use typical form_helper to generation our form.

```erb
<%= form_tag '/login' do %>
	<%= label_tag :username %>
	<%= text_filed_teg :name, params[:username]|| nil %>

	<%= label_tag :password %>
	<%= password_field_tag :word, params[:password]||nil %>

	<%= submit_tag '/login', class: 'btn btn-primary' %>
<% end %> 
```

Tip: `params[:something] || nil` can help us to cache the information user just typed in. if nothing in `params[:something]`, just show the empty column. this founction is automatically bring out when using model-backed form.

<h3>Trigger</h3>

Last thing to do is to create two buttons to trigger `register`, `login`and `logout`.

<h4>Register button</h4>
```
<%= link_to 'Register', register_path, class: 'btn btn-success' %>
```
<h4>Login button</h4>
```
<%= link_to 'Login', login_path, class: 'btn btn-primary' %>
```
<h4>Logout button</h4>
```
<%= link_to 'Logout', logout_path, class: 'btn btn-warning' %>
```

<h3>User's authority</h3>
To define the user's authority before and after login, we need two method to help us to draw the doundary in our entire app.

In `application_controller.rb`, add this two method.

`current_user` method can help us identify who is the current user, for example, we have to know know is now logged in, so we can provide right profile to him/her. not other people's. 
``` ruby
def current_user
  @current_user ||= User.find(session[:user_id]) if session[:user_id]
end
```
By `logged_in?`, we can now give users some more power when they logged_in, for example, when they logged in, they will be able to comment on the posts.  
```
def logged_in?
  !!current_user
end
```

Bacause this two method is defined in application controller, by inherit, it can only benn used in controll, but if we want to use it in our view, add the code below in the `application_controller.rb`

```
helper_method :current_user, :logged_in?
```
Now we can add boundary like so in our template:

```erb
<% if logged_in? %>
	# Show logout and profile button
<% else %>
	# Show register and login button 	
<% end %>
```

BUT! if we just do the limitation above, user can hack into our controller by simply typing url or other manner. So some more things to do.

in `application_controller.rb`
```ruby
def require_user
	if !logged_in?
		flash[:error] = 'You must log in to do this'
		redirect_to login_path
	end
end
``` 
in `posts_controller.rb`
```
before_action :require_user, except: [:index, :show]
```

Now we can ensure users can only do things they are allowed.




































































































