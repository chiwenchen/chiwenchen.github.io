---
layout: post
title: "setup_handy_development_env"
date: 2015-05-24 15:10:44 +0800
comments: true
categories: 
---
This post will collect some very handy set up for the RoR develop environment.
<!-- more -->
<h2>Better Terminal and bash</h2>
By following the tutorial in <a herf = 'https://medium.com/salesforce-ux/iterm2-with-zsh-shell-5944ee0502ac'>iTerm2 with Zsh Shell</a>. We will be using the iTerm2 and have two very lovely teature.
<ol>
<li>Shows the git branch in the terminal</li>
<li>Shorten the header of every line</li>
</ol>

To show the work through and get rid of the tutorial. just 3 things to do here to set it up.
<ol>
  <li>install <a href = 'https://www.iterm2.com/'>iTerm2</a></li>
  <li>
    in Terminal, make this command
```
$ chsh -s /bin/zsh
```
  </li>
  <li>
    one more command
```
$ curl -L https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh | sh
```
  </li>  
</ol>
Now, reopen the iTerm2, and you are good to go.