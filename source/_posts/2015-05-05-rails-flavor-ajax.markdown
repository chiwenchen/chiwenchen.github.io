---
layout: post
title: "Rails Flavored Ajax"
date: 2015-05-05 10:44:18 +0800
comments: true
categories: 
- Rails 
- View
---
<h2>jQuery flavored Ajax</h2>
If we use jQuery flavored ajax, this will be the code we have to write.
``` js
$(document).ready(function() {
	$(document).on('click', 'form#hit_form button', function() { //event listener
		$.ajax({ // ajax request
			type: 'POST',
			url: '/game/player/hit'
		}).done(function(msg) { // response
			$('#game').replaceWith(function(msg);
		});
		return false;
	});
});
```
But for Rails, things got easier!
<!-- more -->
<h2>Rails flavored Ajax</h2>
In Rails world, We can use rails's convention to implement ajax more easily. 

<h3>View</h3>
First, we need to add `remote: true` in the link or button we want to ajax it. For instance.

``` erb
  <%= link_to vote_post_path(post, vote: false), method: 'post',remote: true do %>
```
Just like the `method: 'post'`, the jQuery file will look for the `data-remote: true` which is converted from `remote: true` by html, and then attach itself into the element and convert the link into a ajax call.

Once the link is clicked, we will moving to the related controller, so next, build the action to handle the ajax request.

<h3>Controller</h3>

After we done the DB job with the params passed in, next we have to deal with the response, typically, we will render template or redirect url, but with Ajax in the view, we have to tell what to do with the pure html and what to do with the ajax element. So we split the respond like this.

``` rb
  def vote 
    #@post = Post.find(params[:id])
    #Vote.create(voteable: @post, voter: current_user, vote: params[:vote])
    respond_to do |format|
      format.html {redirect_to :back} #html respond
      format.js #ajax respond
    end
  end
``` 
By convention, if no argument with the `format.js`, controller will look for a file same name as the action `vote`, but subtitle will be `.js.erb`, so we need to create a file called `vote.js.erb` under `/views/posts` to handle the response.

before that, we have to add `id` for elements we want to change, in this example, we want to change the voting number when we click the voting button, so we can give the voting number a `id` like so:
``` js
<span id = 'vote_on_<%= post.id %>_post'>
  <%= post.count_vote %> liked
</span> 
```
<em>hint: the argument of the `id` is html code, so we can use `<%= %>` to write dynamic code inside.</em>

OK, now we can go back `vote.js.erb` to finish the ajax response.

``` js
$('#vote_on_<%= @post.id %>_post').html('<%= @post.count_vote %> liked')
```

<h2>Conclusion</h2>
This means every time we trigger the ajax request by clicking the button we specify by giving it a `remote: true`, it will go through the controller and knowing where to find the ajax response should return back. Firstly, he will look for all the ids in the `.js.erb` file and replace the code all wrapped by the id with the code inside `.html()`  


