---
layout: post
title: "Rails Time Zone"
date: 2015-05-16 15:08:12 +0800
comments: true
categories: 
- Time
- Rails
---
In this post, we will introduce how to implement `TimeZone` and also how to allow user to customize their local timezone.
<!-- more -->
<h2>Display time zone</h2>
In `time_format` method in side `application_helper.rb` file, add %Z to display the time zone. 

``` rb
def time_format(original_time)
  original_time.strftime("%b/%d/%Y %I:%M%p %Z")
end
```

<h2>Set default time zone</h2>
In `application.rb` file under `config`. uncomment the below code.

``` rb
config.time_zone = 'Central Time (US & Canada)'
```

You can see now the default time zone is in `UTC`.

To find out all the time zone availiable in rails app. fisrtly we can type `rake -T | grep time` to find any rake task realted to `time`.

``` 
rake time:zones:all         # Displays all time zones, also available: time:zones:us, time:zones:local -- filter with OFFSET parameter, e.g., OFFSET=-6
```

Now we can use `rake time:zones:all` to see all the time zone.
```
* UTC -11:00 *
American Samoa
International Date Line West
Midway Island

* UTC -10:00 *
Hawaii

* UTC -09:00 *
Alaska

* UTC -08:00 *
Pacific Time (US & Canada)
Tijuana

* UTC -07:00 *
Arizona
Chihuahua
Mazatlan
Mountain Time (US & Canada)

...
```
And you can use `grep` to find your own country's string.
```
rake time:zones:all | grep Taipei
```
In my case, I search for `Taipei` and find it.
```
$ Taipei
```
So now I can use it as my default time zone, back to the application.rb file and modify it.
```
config.time_zone = 'Taipei'
``` 
Now the time zone in my app is change to `CST` which is the shortcut for China Standard Time UTC+8.
```
May/13/2015 09:07PM CST
```
<h2>Allow User customize time zone</h2>
<h3>Add an time_zone column</h3>
```
rails generate migration add_timezone_to_user
```
define column in the below migration file and execute `rake db:migration`
```
class AddTimezoneToUser < ActiveRecord::Migration
  def change
  	add_column :users, :time_zone, :string
  end
end
```
In the `users`'s `new` form, add new column for user to select time_zone.

```
<div class = 'control_group'> 
	<%= f.label :time_zone %>
	<%= f.time_zone_select :time_zone ActiveSupport::TimeZone.us_zones %> 
</div>
```
The `ActiveSupport::TimeZone` class have support lots of way to customize our time_zone selecting form.

Put a `binding.pry` in the `create` action to peep the value come with the time_zone form. and here is the params returns back.

```
=> {"utf8"=>"✓",
 "authenticity_token"=>"5ldSHKcI9hvw1vJK7fZQ9hqETmHSB8kp2gflEylDSME=",
 "user"=>
  {"username"=>"test1",
   "password"=>"test1",
   "phone_number"=>"123",
   "email"=>"123",
   "time_zone"=>"Taipei"}, #time zone is here!!
 "commit"=>"Register Now",
 "action"=>"create",
 "controller"=>"users"}
```
If we check our strong_params in command line.
```
Unpermitted parameters: time_zone
=> {"username"=>"test1",
 "password"=>"test1",
 "phone_number"=>"123",
 "email"=>"123"}
```
Now the time_zone is not permitted yet. so add `time_zone` to the permit list.

And now we can display the time zone that given by the user. make some change on the `application_helper.rb`.

```
def time_format(date_time)
	if logged_in? && !current_user.time_zone.blank?
		date_time = date_time.in_time_zone(current_user.time_zone)
	end
  date_time.strftime("%b/%d/%Y %I:%M%p %Z")
end
```
This will then display the right time as per user's want.
























