---
layout: post
title: "Update new version in heroku"
date: 2016-03-06 12:24:41 +0800
comments: true
categories: 
---

if we have two different server in heroku like this

```
➜  myflix git:(staging) git remote -v
origin  https://github.com/chiwenchen/myflix.git (fetch)
origin  https://github.com/chiwenchen/myflix.git (push)
production  https://git.heroku.com/chiwen-myflix.git (fetch)
production  https://git.heroku.com/chiwen-myflix.git (push)
staging https://git.heroku.com/chiwen-myflix-staging.git (fetch)
staging https://git.heroku.com/chiwen-myflix-staging.git (push)
```

here is the whole process if we want to push a new version to staging server

```
➜  myflix git:(SOMEOTHER BRANCH) git branch staging # navigate to staging branch

➜  myflix git:(staging) git add .
➜  myflix git:(staging) git commit -m 'new stable version'
➜  myflix git:(staging) git push
...
➜  myflix git:(staging) git push staging staging:master
...
➜  myflix git:(staging) heroku run rake db:migrate
...
➜  myflix git:(staging) heroku restart
...
Done!!
```

let's make another example to clarify the command full of `staging`...

if we have a remote repo named: `live-remote`, and a local repo named: `live`

then the last command will be:

```
➜  myflix git:(staging) git push live-remote live:master
```

the additional :master here is saying push my local `live` branch into the master branch on the remote - note: heroku can only deploy from the master branch.