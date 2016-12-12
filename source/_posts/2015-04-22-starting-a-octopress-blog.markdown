---
layout: post
title: "Starting a Octopress Blog"
date: 2015-04-22 13:55:02 +0800
comments: true
categories: Misc
---
<h2>Generate a post</h2>
the first step to generate a octopress post is using rake task to create a file with title.
```
rake new_post["post title"]
```
this command will create a file inside the `source/_posts`, use your text editor to open this file. 
<!--more-->
<h2>Yaml Front matter</h2>

In the very top of the content, you will see some text like this, this is called <strong>yaml front matter</strong>
```
---
layout: post
title: "Starting a Octopress Blog"
date: 2015-04-22 13:55:02 +0800
comments: true
categories: 
---
```
Here is where you can have some simple information of the post, and also assign categories to this post.

Here is how to assign categories.

Only one category:
```
categories: Animal
```
Muitiple categories 1:
```
categories: [Animal, Love, Human, Coding]
```
Muitiple categories 2:
```
categories: 
- Animal
- Love
- Human
- Coding
```
<h2>Start writing Content</h2>
After the <strong>yaml front matter</strong> is where to put the real content.

The octopress blogging framework is using <a href = 'http://markdown.tw'>Markdown</a> syntax which is really powerful to write a coder's blog. 

<h2>Preview and publish</h2>

To preview the Blog.
```
rake preview
```
and then open your brower and navigate to `localhost:4000`
To push to post to public.
```
rake generate
rake deploy
```