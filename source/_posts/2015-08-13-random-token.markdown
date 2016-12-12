---
layout: post
title: "Random Token"
date: 2015-08-13 22:22:49 +0800
comments: true
categories: Rails
---
To expose raw data to user is always not a ideal situation, for example, if our app can show every user's profile, and the url of each user's profile looks like this:

```rb
www.example.com/user/1
```

Then the user will be able to see the `id` of every user directly, if they want, they can even change the id on the url and see other's profile, or try to predict the total member of our app, this might be the business sensetive information we want to hide. so we need a way to keep this kind of information away from users, a good way to accomplish this is to use `Random Token`. by using random token, we wrap our information by a meanless string to keep it away from user, instead of `id` strightly shown on url, they will see something like this:

```
www.example.com/user/124343njkdsdw
```
<!-- more -->

<h2>Generate and use Random Token</h2>

Firstly, we need to use migration to create a `token` column for the target, for example, the `User` model:

```
$ rails g migration add_token_to_users
```

then run `db:migrate`, this will add a column `token` for us. Next step, we need to automatically generate the random token every time we add a new user account. so let's dive into User's model and add some code:

```
class User < ActiveRecord::Base

  after_create :generate_token

  def generate_token
    self.token = SecureRandom.urlsafe_base64
  end

end
```  

This method will give us the random token everytime right after we call `User.create` method, so it is very important to use `after_create`, not `after_save`, because if we do so, the token will change even we update the user's data, remenber, update also calls `save` method behind the scene, and that is what we don't want in this situation.

PS: Here are some more <a href='http://ruby-doc.org/stdlib-1.9.2/libdoc/securerandom/rdoc/SecureRandom.html'>Ruby doc - SecureRandom</a> that we can use.

Next, we have to overwrite the to_param method in User model.

```
def to_param
  token
end
```

And also in user's controller, before the implementation, we use `find` to grab id and search for target user, now we have to use `token` instead, so we also need to rewrite this part, for example.

```
User.find_by_token(params[:token])
```  

Final step, before we create the token column, we already have some existing data, those old data will leave the token blank at the time we add this new column. to fix it, open the migration file and do so to manually let app to generate token for each old user:

```
class AddTokenToUser < ActiveRecord::Migration
  def change
    add_column :users, :token, :string
  end

  User.all.each do |user|
    user.generate_token
  end
end
```

And that's all, the random token is ready to serve.