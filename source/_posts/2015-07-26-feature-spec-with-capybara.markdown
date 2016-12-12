---
layout: post
title: "Feature spec with Capybara"
date: 2015-07-26 09:20:06 +0800
comments: true
categories: 
---
Unit test can only test the individual unit like actions inside controllers or models, it helps us to make sure the single unit is working properly. But a application will never function rely on one single unit, interaction is every where, any feature will need controller, model and view work together to perform a complete functionality, so we must have a way to simulation the whole process, and that is exactually what feature spec does.

<!-- more -->

<h2>Read offical documentation</h2>
Here is the <a href='https://www.relishapp.com/rspec/rspec-rails/docs/feature-specs/feature-spec'>feature spec</a> document from Relish, and we will use `capybara gem` to accomplish the feature spec, and here is the <a href='http://www.rubydoc.info/github/jnicklas/capybara/master#the-dsl'>capybara</a> document to reference the DSL.

below is a simple example to show how we use feature spec to mimic the user experience of single request on our website. this will firstly visit the `ROOT/widgets/new` and the fill in the name column with the value you gave and submit the form by pressing the button, and in the end of the test, we will expect some content to been shown on the screen to exam if the whole process is working good.

This example also shows that the feature spec is actually written in high level but will actually test all the conponents (models, controllers, views, routes...etc) that related to the requests.

```ruby
require "rails_helper"

feature "Widget management"  do
  scenario "User creates a new widget" do
    visit "/widgets/new"

    fill_in "Name", :with => "My Widget"
    click_button "Create Widget"

    expect(page).to have_text("Widget was successfully created.")
  end
end

```

From this <a href='https://github.com/chiwenchen/myflix/tree/staging/spec/features'>project</a>, you can see more example of using capybara to test features.

Also, here is a great <a href='https://gist.github.com/zhengjia/428105'>Capybara cheat sheet</a>

<h2>Set up feature spec environment</h2>

First step is to install `capybara` gem, add below lines into our `Gemfile` will scope the capybara only in test environment which is the only env we will need capybara.

```
group :test do
  gem 'capybara'
end
```

<h2>Install launchy gem</h2>

`launchy` gem is equipped with a very powerful method called `save_and_open_page`, if we put this method in any point of our spec, when it excuted, it will open the current state of the page in real brower, so we can visually see what is going on in our test. this is a better way for us when debugging.

<h2>Navigators</h2>

<h4>Use ID</h4>

In some cases, we might have no identical id or label to aim the target we want, most simple way is to append a id for the item we want to identify.

For example...

in view:

```
= text_field_tag 'queue_item[][position]', queue_item.position, id: "video_#{queue_item.video.id}"
```

in spec:

```
fill_in "video_#{inception.id}", with: 2
expect(find("#video_#{inception.id}").value).to eq("2")
```

<h4>Use data attribute</h4>
instead of changing the `id` to allow rspec can aim the item we want, we can append a new attribute called `data attributes`, it will looks something like:

```
= text_field_tag 'queue_item[][position]', queue_item.position, data: {video_id: video_#{inception.id}}
#data is a hash, the video_id is the key!!
```

but with data attribute, we can not directly manipulate the field, for example `fill_in`,because it is only works for `id`, `name` or `label`. Instead, we need to `find` the field first, so we can do this in feature spec:

```
find("input[data-video-id='#{inception.id}']").set(1)
#chain a set method can direct set the value of the found item.
expect(find("input[data-video-id='#{inception.id}']").value).to eq("1")
```

<h4>Use xpath</h4>

Capybara also allows us to use `xpath` selector, here is the <a href='https://github.com/jnicklas/capybara#xpath-css-and-selectors'>reference</a> and here is the <a href='http://www.w3schools.com/xsl/xpath_syntax.asp'>w3schools xpath tutorial</a>.


asserting value using xpath.
```
with_in(:xpath, "//tr[contains(.,'#{monk.title}')]") do 
#this line means find tr row which contains the monk.title string
  fill_in 'queue_item[][position]', with: 3
  #and fill in the value in the item which with specific id or name  
end
```
examing value using xpath.
```
expect(:xpath, "//tr[contains(.,'#{monk.title}')]//input[@type='text']").value).to eq("1")
```



