---
layout: post
title: "Macros and Shared Examples"
date: 2015-07-25 08:50:29 +0800
comments: true
categories: 
---
The <strong>Macro</strong> and <strong>Shared Example</strong> are both used to extract codes that will repeated several times. use this two techniques will allow us to build pieces of codes that can be used through our entire rspec test, also make our testing code more clean and readable. 
<!-- more -->
<h2>Macro</h2>
First thing first, create a folder under `spec` called `support`, any file under this `support` folder will automatically loaded when we execute rspec. Next, create a file called 'macros.rb' under the `support`, here is the place we will put our marco code to be used later on. 

It is pretty simple to use macro, let see an example, let's say we want to exract codes that can create a user named `sammy` and log her in by putting her id into the session, this is a really common function we use in our test.

Inside the 'macros.rb'

```rb
def log_in_user
  sammy = Fabricate(:user, name: 'sammy')
  session[:user_id] = sammy.id
end
```

Somewhere in our test code, if we want to log in a user before tests, we can do this

```
before  {log_in_user}

describe 'user can do whatever he/she want'
 ...
end
```

Done! as simple as creating a new method, and good new is that this macro can be used in your entire Rspec code.

another macro example.

```
def current_user
  User.find(session[:user_id])
end
```

This way, we can refer to current_user like this in the test code:

```
sammy = current_user
#now we can use sammy to process our test if we need it
```
<h2>Shared Example</h2>

Again, create a file called 'shared_examples.rb' under `spec/support`. 

Cool thing `shared_example` can do is allow us to group and extract the test items and reuse it.

For example, if we want the 'user not log in' scenario to be tested in mutiple controller action, this is the case we can use shared_examples.

```
shared_examples 'user_not_logged_in' do 
  it 'redirect_to front_videos_path' do 
    clear_current_user # this method is defined in macro
    action
    expect(response).to redirect_to front_videos_path
  end

  it 'shows the flash notice' do 
    clear_current_user # this method is defined in macro
    action
    expect(flash[:notice]).to be_present
  end
end
```

And inside the test, we can use it like this:

```
describe 'POST update_position' do 

  #some other test

  it_behaves_like 'user_not_logged_in' do 
    let(:action){post 'update_queue_item'}
  end

end
```

The `it_behaves_like` is the preserved word to call the shared_example, folloewd by the name of the example. and inside the block, we can put the setting, the action call and the verification. 

Be noticed that the `Macros` can also be used in the shared_examples. `clear_current_user` in this case.

And also we can use `let` to define the variable if we do not want to hard code something. For instance, `action` will be different when we call this test in different controller/action. so it's a good situation to use `let` here.

