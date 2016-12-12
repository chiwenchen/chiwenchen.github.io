---
layout: post
title: "Test styles of Rails"
date: 2016-02-23 12:42:54 +0800
comments: true
categories: test
---
There are three different testing style or process we use in developing rails app.

<h2>Outside in</h2>
The first is called outside in, this style means when we developing a new feature, we thinking in a integration level or business level first, by doing this, we can clarify the business value of this feature in the first place, than from here, this will drive us to implement a level deeper which is controller level to fulfill the need from the feature, and then one level deeper to model layer.  

The most common usecase of the approach is when we work with customer and the requiremnts is very clear, just write out the descriptions of the feature and implement it step by step will make the developing process more fluent.

<!-- more -->

<h2>Inside out</h2>
Second style is totally bottom up, we start from the most depth layer which is model, we write test description of each unit and then fulfill the test, once all the model we think is complete, then move to the controller layer and then to view template.

The pros of this approach is we can start small, this will allow developer more focus on one thing and make it easier to complete, but the cons of this is some times when we move to next layer will find that some of our model method is useless and we lack some of the function in the other hand, this will become time comsumming and makes a lot of goes and back.

<h2>Meet in the middle</h2>
The third style takes a good balance between the previous two.

In this approach, we firstly build out the view template, this give us a vision of how the web page will look like and imaging how people will interact with the site, and then we start from the controller to make the page alive, once we fiound the method is way too complicate for controller, we will then drop it to model layer, also make unit test for the function, once the unit is well tested, we then back to the controller layer to implement it. after the controller layer and model layer is finished and tested, we then start to write feature test to mimic the user experience.

By doing this, we will have clear business vision in first place just like <strong>Outside in</strong> style, as well as having the pros of easy to start small just like <strong>Inside out</strong>, at the same time, won't waste too much time on guessing what method to work on.
