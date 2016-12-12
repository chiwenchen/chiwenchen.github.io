---
layout: post
title: "Rails Basic Routes"
date: 2015-04-28 16:55:35 +0800
comments: true
categories: 
- Rails
- Route
---
In this post, we will expose some every common routing methods.

<h3>Root Route</h3>
`root` route is the first page people will see when they enter our website. `root` route will
``` rb
root to: 'CONTROLLER#ACTION' #or 
root 'CONTROLLER#ACTION'
```
for example:
```
root to: 'posts#index'
```
<h3>Resource</h3>

<h3>.:format</h3>
```
Rails.application.routes.draw do
  resources :users

  get 'profile', controller: :users, to: :show
end
```
will generate below routes
```
   Prefix Verb   URI Pattern               Controller#Action
    users GET    /users(.:format)          users#index
          POST   /users(.:format)          users#create
 new_user GET    /users/new(.:format)      users#new
edit_user GET    /users/:id/edit(.:format) users#edit
     user GET    /users/:id(.:format)      users#show
          PATCH  /users/:id(.:format)      users#update
          PUT    /users/:id(.:format)      users#update
          DELETE /users/:id(.:format)      users#destroy
  profile GET    /profile(.:format)        users#show
```
We have `.:format` option everywhere, RESTful routes or custom routes - everywhere. However member RESTful routes (show, edit, update, destroy) require the `:id` but custom profile does not require it. Instead optional `.:format` is following. That means if you do `profile_path("whatever")` it will produce URL like 
```
http://localhost:3000/profile.whatever.
```
Be noticed that the url is `/profile.whatever` and not `/profile/whatever`
And also the params will contain
```
format: 'whatever'
```

