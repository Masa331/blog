---
layout: post
title:  "Setup and deploy Roda app from scratch"
date:   2016-04-15 21:22:49
comments: true
author: Přemysl Donát
---
# Setup and deploy Roda app from scratch

I'v decided to migrate my projects exclusivelly to AWS. It's been a great experience so far. Before i was using some local hosting provider which was good but it can't beat the AWS's ease of use and endless posibilities.

Here in this post i'm gonna describe how to deploy simple Roda app on new EC2 instance from scratch.

My stack:

* Ubuntu 14.04 basic EC2 image
* Ruby 2.3
* Roda application framework
* SQLite3
* Nginx as a web server
* Puma as an application server
* Capistrano for deployment
* HTTPS

## 1) Setting up the server

This part of the setup is pretty easy and involves just installation of basic packages we are gonna need:

~~~
$ sudo apt-get update
$ sudo apt-get upgrade

# Ubuntu itself doesn't have up to date ruby packages
# the option i prefer is to get ruby from Brightbox PPA: https://www.brightbox.com/docs/ruby/ubuntu/
$ sudo apt-get install software-properties-common
$ sudo apt-add-repository ppa:brightbox/ruby-ng
$ sudo apt-get update
$ sudo apt-get install ruby2.3 ruby2.3-dev

# This is my database of choice but installing PostgreSQL or MySQL should be same
$ sudo apt-get install sqlite3 libsqlite3-dev
# Git will be needed for our deployment
$ sudo apt-get install git
# Web server
$ sudo apt-get install nginx

# Basic packages needed for compilation of Ruby gems native extensions
$ sudo apt-get install make g++ gcc libssl-dev
# Basic monitoring tool
$ sudo apt-get install htop
~~~

## 2) Configurign nginx

Nginx on this box won't serve anything other than this one app. So because of this i'm not using it's base setup with sites-enabled and sites-available directories. I configure the server right inside the nginx.conf file.


This is how the server block in my `nginx.conf` file looks like:

~~~
##
# Virtual Host Configs
##

index index.html;

# Redirect all http trafic to https
server {
  listen         80;
  server_name    suptasks.com www.suptasks.com;
  return         301 https://$server_name$request_uri;
}

upstream puma {
  server unix:///var/www/suptasks/shared/tmp/sockets/puma.sock;
}
server {
  # If you don't need HTTPS just remove the redirect above, change ports
  # here and remove the directives for ssl.
  listen 443 ssl default_server;
  server_name suptasks.com www.suptasks.com;
  ssl_certificate <path_to_certificate>;
  ssl_certificate_key <path_to_key>;
  # Don't use SSLv3 because of Poodle
  ssl_protocols TLSv1 TLSv1.1 TLSv1.2;

  root /var/www/suptasks/current/public;

  location ^~ /assets/ {
    gzip_static on;
    expires max;
    add_header Cache-Control public;
  }

  try_files $uri/index.html $uri @puma;
  location @puma {
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header Host $http_host;
    proxy_redirect off;

    proxy_pass http://puma;
  }

  error_page 500 502 503 504 /500.html;
  client_max_body_size 10M;
  keepalive_timeout 10;
}
~~~

Also my nginx.conf file is missing these 2 lines because as i said, i configure everything in nginx.conf:

~~~
include /etc/nginx/conf.d/*.conf;
include /etc/nginx/sites-enabled/*;
~~~

# 3) Deploy with Capistrano
I'll deploy into /var/www directory. Capistrano and my deploy user needs to be able to create subdirectiories:

~~~
$ sudo chown ubuntu:ubuntu /var/www/
~~~

In order to fetch new versions of your app from remote repository(Gtihub in my case) you need to generate ssh key and upload it to Github.

~~~
$ ssh-keygen -t rsa -b 4096
~~~

Then we can prepare Capistrano config files:

~~~
$ cap install
~~~

this will create necessary `Capfile` and `config/deploy.rb` and `config/deploy/production.rb` which you need to manually customize:

My Capfile:

~~~
require 'capistrano/setup'
require 'capistrano/deploy'
require 'capistrano/puma'
require 'capistrano/bundler'

Dir.glob('lib/capistrano/tasks/*.rake').each { |r| import r }
~~~

This is enough for our Roda app now. Additionally to 'capistrano' gem you only need 'capistrano-puma' and 'capistrano-bundler' in your Gemfile.

config/deploy.rb:

~~~
set :application, 'suptasks'
set :repo_url, 'git@github.com:Masa331/suptasks.git'
set :deploy_to,   '/var/www/suptasks'
set :pty, true

# This is something which will be different for your app probably
set :linked_dirs,  %w{bin log tmp/pids tmp/cache tmp/sockets db/task_databases/databases}
set :linked_files, %w{.production suptasks.log db/user_database/user_database}

namespace :deploy do
end
~~~

and config/deploy/production.rb:

~~~
server '52.58.103.140', user: 'ubuntu', roles: %w{web app db}
set :ssh_options, {
 keys: %w(<path_to_your_ssh_key>)
}
~~~

Now before deploy you need to upload all linked files and directories you listed in `config/deploy.rb`.

When you'r done you can do `cap production deploy` and your app should be up and running.

This was pretty much everything to set up  my small app in production :)

## Problems troubleshooting
If you encounter any problem during deploy i suggest you to try to ssh into the box and try to fire up the exact command Capistrano failed on. Often there can be problems with deploy user permissions etc.

When the deploy doens't fail but the app isn't running either you can check nginx log files and you migth find some clues there:

* `/var/log/nginx/access.log`
* `/var/log/nginx/error.log`

SSL certificate problems will be logged there for example.


I also had a few problems with starting Puma. It was due to missing linked files but the biggest problem is that the deploy won't fail when Puma doesn't start properly. However when this happens and you suspect Puma i suggest ssh into the box, cd inside the app root and try to start puma manually the same way the Capistrano tries it but strip off the `--daemon` parameter. When you do this the Puma should fail with some error message explaining the problem.

In the time of writing the command was:

~~~
$ /usr/bin/env bundle exec puma -C /var/www/suptasks/shared/puma.rb
~~~


