---
layout: post
title: "Rails Basic Model Design"
date: 2015-04-27 13:28:35 +0800
comments: true
categories: 
- Rails
- Model

---
`Model` is the `M` of Rails MVC structure, which is responsible for the database management. In Rails we don't need to write SQL syntax to interact with DB layer, instead, by inherit from `ActiveRecord::Base` class, we get a lot of convention in our codebase to store and retrive datas from our database.
<!-- more -->
<h2>Use Migration to create a Table</h2>
in project folder path:
``` rb
rails generate migration create_users  
```
Here is the first convention do the magic!! by execute this commend, we weill get one file looks like `20140316231808_create_users.rb`. open it in text editor.

```
class CreateUsers < ActiveRecord::Migration
  def change
    create_table :users do |t|
    end
  end
end
```
In here, we can add column we want to store particular data we need. like this:
```
class CreateUsers < ActiveRecord::Migration
  def change
    create_table :users do |t|
    	t.string :name
    	t.integer :phone_number
    	t.timestamp
    end
  end
end
```
By doing this, we will create a table called `users` which include columns `id`,`name`, `phone_number` and two columns which will record the created_time and updated_time for every single data. 

Then, next thing is hop into `rails c` and do:
```
rake db:migrate
```
by doing this, all the migration we created so far will migrate into the `schema`.
DB side is done! next one is the `model`.

<h2>Create Model</h2>
The Model of MVC, in Rails we called it `ActiveRecord`.this layer is responsible for the interact between DB layer and our controller. also we tentd to put business logic here.

Firstly, we have to generate a file in `models`, in this case, because we want to map to the table `users` we just create before. so we have to name the file `user.rb`(singular of the table name). By convention, this connection will automatically linked.

Inside the file, we add few line of code.
```
class User < ActiveRecord::Base

end
``` 
That's is, now the Model is create.
Then in this file, we will be able to add features in it. Association, Validations, Authentication, business logic and more!

<h2>Some hint</h2>
<h4>Migration's naming convention</h4>
<ul>
<li>
To create a brand-new table
```
rails generate migration create_TABLENAME 
```
</li>
<li>
To add columns to existing table
```
rails generate migration add_COLUMNNAME_to_TABLENAME
```
</li>
remember, always start the migration file name with `create` or `add`. otherwise, might cause problem when deploy to heroku.
</ul>
<p></p>

<h4>Change? Up and down?</h4>
In 99% use case, we lean on `change` method, only when the `up` and `down` be not totally opposite, then will use `up` and `down` method to define migration.

<h4>rails generation</h4>
migration is the only one file we will use `rails generate` to generate, because it will create timestamp for us like this: `20140316231808_create_users.rb`.



