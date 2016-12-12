---
layout: post
title: "Send email from your app"
date: 2015-08-13 15:30:01 +0800
comments: true
categories: 
---

It is quite common that we receive an mail from some websites, today we will create a system to send out emails to our user from our own app. the sample we give today is to send out a welcome message to user when they first register a new account.

<!-- more -->

<h2>Send out email from app</h2>

like the code below, we add line 4 to call the `AppMail.send_welcome_message`. this will send out a mail to `@user`. remenber to add `.deliver` to actually send the mail. 
```rb
 def create
    @user = User.new(strong_params)
    if @user.save
      AppMailer.send_welcome_message(@user).deliver
      ...
    else
      render 'new'
    end
  end
```

we didn't define the method yet, let's do it now. we have to create a file called `app_mailer.rb` under `app/mailers`, inside the file... 

```
class AppMailer < ActionMailer::Base
  def send_welcome_message(new_user)
    @new_user = new_user
    mail from: 'myflix@google.com', to: @new_user.email, subject: 'welcome to myflix'
  end
end
```

Here we use `mail` method to define the sender and the recipient, also the title of the mail. next we will write the content. it's quite similar to the view template, so let's create a folder named `app_mailer` as well, and the file under the folder named `send_welcome_message.html.haml` as well as the defined method. inside the file...

```haml
!!! 5
%html(lang="en-US") 
  %body 
    %p Welcome to the Myflix, #{@new_user.name}
    %p Enjoy the videos!0
``` 

if we didn't do any mail configuration, we can only verify the mail on rails server console after executed it.

it will looks some thing like...

```ruby
AppMailer#send_welcome_message: processed outbound mail in 13.1ms

Sent mail to carol@example.com (16.8ms)
Date: Thu, 13 Aug 2015 15:14:46 +0800
From: myflix@google.com
To: carol@example.com
Message-ID: <55cc43e6c2b46_50a88082dbf81929c@IvandeMacBook-Air.local.mail>
Subject: welcome to myflix
Mime-Version: 1.0
Content-Type: text/html;
 charset=UTF-8
Content-Transfer-Encoding: 7bit

<!DOCTYPE html>
<html lang='en-US'>
  <body>
    <p>Welcome to the Myflix, carol</p>
    <p>Enjoy the videos!</p>
  </body>
</html>
```

<h2>Mailer Configuration - Development env</h2>
In development environment, because we are working locally, so we will not actually sending out any mail, but only verify the simulated mail in rails server console is not good enough, we can not see if it's right if we add more fancy look like some css or images. so we need a gem called `letter_opener` and further configure it into the environmental config file.

```
group :development do 
  gem letter_opener
end
``` 
run `bundle install` to install the gem

and add this line to the `config/environments/development.rb`

```
config.action_mailer.delivery_method = :letter_opener
```

Restart the rails server and if we create a new account again, it will show the mail in the browser which is better for us to see the context and the style.

<h2>Mailer Configuration - Production env</h2>

<h2>Rspec the mailer sending function</h2>

once we done the ActionMailer setting, we can write the test suit to verify the mailer is working properly. here is the sample of how to write a whole test suit for mailer.

```
context 'sends mail' do 
      after{ActionMailer::Base.deliveries.clear}

      it 'sends out the email' do 
        post :create, user: Fabricate.attributes_for(:user, name: 'sammy')
        expect(ActionMailer::Base.deliveries.last).should_not be_blank
      end
      it 'sends the email to the right person' do 
        sammy = Fabricate.attributes_for(:user, name: 'sammy')
        post :create, user: sammy
        expect(ActionMailer::Base.deliveries.last.to).to eq([sammy[:email]])
      end
      it 'sends out the correct context' do 
        sammy = Fabricate.attributes_for(:user, name: 'sammy')
        post :create, user: sammy
        expect(ActionMailer::Base.deliveries.last.body).to include("Welcome to the Myflix, sammy", "Enjoy the videos")
      end

      it 'does not sends out the mail if the register input is not valid' do 
        carol = Fabricate.attributes_for(:user, name: nil, password: nil)
        post :create, user: carol
        expect(ActionMailer::Base.deliveries.last).should be_blank
      end
    end
```

One special thing here is in line2, `after` script.
the reason to add this is because typically when we run normal specs, thess specs are transactions, that means after each spec, the database will automatically rollback to the initial state, this will avoid specs to interfere each other. But here we are sending email in specs, and each time we send a email, we are actually save those emails into the queue `ActionMailer::Base.deliveries`, and this is not part of the database transaction, so this will not be rollback. That's why we need to make sure the queue will cleared after each spec test.