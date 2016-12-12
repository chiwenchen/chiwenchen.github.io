---
layout: post
title: "Decorator"
date: 2016-02-24 16:32:45 +0800
comments: true
categories: 
---

For the typical situation, all the logic should only live in model layer, and controller only responsible on grap the data we need and show it in view.

But in some situation, we will still need logic code in view layer. we can certainly do this, but this will make the view template too complicate than it should be and harder to read. so in this type of situation, we can use `decorator` to extract logic code from view template.

<!-- more -->

<h2>Create decorator</h2>

Firstly, we need to add a directory under app folder, like `/app/decorators/`, here wwe will make a `video_decorator.rb` for demonstrate.

let's say before we build the decorator, there is a logic code in one of the view template look like this:

```ruby
if video.reviews.present?
  "#{video.rating} / 5.0"
else
  N/A
end
```

Now we can move it to decorator.

```
#in video_decorator.rb

class VideoDecorator

  attr_reader :video

  def initialize(video)
    @video = video
  end

  def rating
    if video.reviews.present?
      "#{video.rating} / 5.0"
    else
      "N/A"
    end
  end

end
```

```
#in video.model, add a new method

def decorate_rating 
  VideoDecorator.new(self).rating
end
```

then back to the view template, we can replace the logic by

```
video.decorate_rating
```

As you can see, the present code in view template is much more simple and readiable. also we can test the decorate code seperately now.

<h2>Draper gem</h2>

By using `draper` gem, we can now implement decorator more easily.

Install `draper` gem by add it to `Gemfile` nad run `bunlde install`.

```
gem draper
```

Now we can trim our decorator to the code like below.

```
class VideoDecorator < Draper::Decorator

  delegate_all
  #this will make all the method defined in original model also available here.

  def decorate_rating
    object.reviews.present? ? "#{object.rating} / 5.0" : "N/A"
  end
end
```

More information and DSL, please see <a href='https://github.com/drapergem/draper'>Draper document</a>

