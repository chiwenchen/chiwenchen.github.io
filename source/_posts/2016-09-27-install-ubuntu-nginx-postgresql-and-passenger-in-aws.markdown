---
layout: post
title: "Install ubuntu, nginx, Postgresql and passenger in AWS"
date: 2016-09-27 11:20:27 +0800
comments: true
categories: 
---
<h4>Create User</h4>
step1: create user
```
$ sudo adduser apps
Enter new UNIX password: apps
Retype new UNIX password: apps
passwd: password updated successfully
Changing the user information for apps
Enter the new value, or press ENTER for the default
        Full Name []:
        Room Number []:
        Work Phone []:
        Home Phone []:
        Other []:
Is the information correct? [Y/n] Y
```

step2: give user the sudo authrization
```
$ sudo visudo
# add [apps ALL=(ALL:ALL) ALL] in the file.
```

step3: switch to apps
```
su apps
```

step4: update server
```
$ sudo apt-get update
$ sudo apt-get upgrade
```

step5: setup SSH
```
$ ssh-keygen
$ cd ~/.ssh
$ touch authorized_keys
$ vi authorized_keys
copy and past local machine's public key here
```

step6: make ssh more easy
```
# in local machine
$ vi ~/.ssh/config

#add below line, mgstg can be any command you want.
Host myapp-staging
  HostName IPorDomainName
  User apps
```

step7: setting time zone and NTP server
```
$ sudo dpkg-reconfigure tzdata
$ sudo apt-get install ntp
$ sudo ntpdate ntp.ubuntu.com # Update time
```

step8: configure hostname
```
$ sudo hostname your-hostname
$ sudo vim /etc/hosts
###
127.0.0.1 your-hostname
###
$ sudo vim /etc/hostname
###
your-hostname
###
$hostname #verify the hostname is set
```

step9: install dependencies for ruby(source from GoRails)
```
sudo apt-get install git-core curl zlib1g-dev build-essential libssl-dev libreadline-dev libyaml-dev libsqlite3-dev sqlite3 libxml2-dev libxslt1-dev libcurl4-openssl-dev python-software-properties libffi-dev
```

step10: install RVM and ruby
```
sudo apt-get install libgdbm-dev libncurses5-dev automake libtool bison libffi-dev
gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3
curl -sSL https://get.rvm.io | bash -s stable
source ~/.rvm/scripts/rvm
rvm install 2.2.5
rvm use 2.2.5 --default
ruby -v
```

step11: config Git
```
$ git config --global color.ui true
$ git config --global user.name "YOUR NAME"
$ git config --global user.email "YOUR@EMAIL.com"
$ ssh-keygen -t rsa -b 4096 -C "YOUR@EMAIL.com"
$ cat ~/.ssh/id_rsa.pub
# paste the ssh key in https://github.com/settings/ssh
$ ssh -T git@github.com check if the ssh worked.
# you should see something like this:
  # Hi chiwen! You've successfully authenticated, but GitHub does not provide shell access.
```

step12: install Rails
Since Rails ships with so many dependencies these days, we're going to need to install a Javascript runtime like NodeJS. This lets you use Coffeescript and the Asset Pipeline in Rails which combines and minifies your javascript to provide a faster production environment.

To install NodeJS, we're going to add it using the official repository:
```
$ curl -sL https://deb.nodesource.com/setup_4.x | sudo -E bash -
$ sudo apt-get install -y nodejs
$ gem install rails -v 4.2.6
$ rails -v
```

step13: install Postgresql
```
$ sudo sh -c "echo 'deb http://apt.postgresql.org/pub/repos/apt/ precise-pgdg main' > /etc/apt/sources.list.d/pgdg.list"
$ wget --quiet -O - http://apt.postgresql.org/pub/repos/apt/ACCC4CF8.asc | sudo apt-key add -
$ sudo apt-get update
$ sudo apt-get install postgresql-common
$ sudo apt-get install postgresql-9.5 libpq-dev
```

step14: create pssql user
```
#replace the `apps` to the username you want
$ sudo -u postgres createuser apps -s

# If you would like to set a password for the user, you can do the following
$ sudo -u postgres psql
$ postgres=# \password chris
```

step15: install passenger and nginx(source from https://www.phusionpassenger.com/library/walkthroughs/deploy/ruby/ownserver/nginx/oss/trusty/install_passenger.html)
```
$ sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 561F9B9CAC40B2F7
$ sudo apt-get install -y apt-transport-https ca-certificates

# Add our APT repository
$ sudo sh -c 'echo deb https://oss-binaries.phusionpassenger.com/apt/passenger trusty main > /etc/apt/sources.list.d/passenger.list'
$ sudo apt-get update

# Install Passenger + Nginx
$ sudo apt-get install -y nginx-extras passenger
```

step16: configuration passenger
```
$ sudo vim /etc/nginx/nginx.conf
# uncomment below line.
# include /etc/nginx/passenger.conf;

# restart the server
$ sudo service nginx restart
```

step17: check installation
```
$ sudo /usr/bin/passenger-config validate-install
# you should see all passed like below
  * Checking whether this Passenger install is in PATH... ✓
  * Checking whether there are no other Passenger installations... ✓
$ sudo /usr/sbin/passenger-memory-stats
# you should see nginx and passenger is having some process running.
```

step18: deploying using capistrano
```
local$ cap staging deploy:check
# create database.yml under /home/apps/myguide/shared/config and paste the local setting to here.
#run again
local$ cap staging deploy:check
$ $ createdb myguide-staging
local$ cap staging deploy
```

step19: change root in nginx.conf
```
$ vi /etc/nginx/nginx.conf

```


Trouble shooting:
From the commandline you can run `nginx -c /etc/nginx/nginx.conf -t` to have nginx check your configuration for errors.
