puma_jungle_rbenv_cap3
======================

#Background

You've decided to deploy Rails on the world best production environment. Naturally, you've chosen:

* Ubuntu 14.04
* Nginx (of course! easy decision)
* Puma (threads are the new nio, and Puma is awesomeness)
* Rbenv (ok, you debated about rvm vs rbenv and both will work with these instructions)
* Capistrano 3 (you banged your head against the wall trying to update your cap 2 scripts, but feel you must keep up)
* System scripts - /etc/init.d/... (not upstart and not systemd)
* Pumactl (because pumactl works! and is very nice)

So at the end of the day you *want* you all these to work, and play nicely together.

- you have a deploy user that *doesn't* need sudo rights at all!
- cap staging deploy
- cap staging puma:start
- <del>cap staging nginx:restart</del> (I don't use the nginx capistrano 3 nginx scripts, please start it manually)
- /etc/init.d/puma start (on server)
- /etc/init.d/puma stop (on server)
- everything should start on server reboot AND you can still use cap puma:restart, etc.
etc.

With this much awesomeness, you assume someone else has this working already. I found lots of different examples, but nothing with this exact stack that worked the way I expected it to. Plus I wanted to use the default scripts, as much as possible.
Also, I deploy as "deploy" user and I don't give it any sudo rights! So I have to deploy thie manually.

Here's how you use this setup:
First read this about jungle scripts

* https://github.com/puma/puma/tree/master/tools/jungle/init.d

Then read this about capistrano 3

* https://github.com/seuros/capistrano-puma

Now do these, but use my scripts instead.

# Instructions

Follow instructions on installing jungle scripts.

Replace /etc/puma.conf with my version *but change 'server' to your app-name*. You can have multiple lines, with multiple apps.

Replace /usr/local/bin/run-puma with my version (uses rbenv, but you can change it to use rvm if you want)

Setup capistrano 3 in your project *with* CAP file like this:
```
# Load DSL and Setup Up Stages
require 'capistrano/setup'

# Includes default deployment tasks
require 'capistrano/deploy'

require 'capistrano/rails'
# require 'capistrano/rvm'
require 'capistrano/rbenv'
require 'capistrano/bundler'
require 'capistrano/puma'
```

Add the puma.rb in app/config so that daemonize=true (because it seems to be false by default now)

Deploy the project

* cap staging deploy

I generated the nginx site, so it is at app/config/nginx.conf, but symlinked my nginx sites-enabled into the config in my project instead of copying it to sites-enabled
* Replacing the 'server' with your real app name (and *inside of nginx* also replace 'server' with your appname

  * sudo ln -nfs /home/deploy/apps/server/current/config/nginx.conf /etc/nginx/sites-enabled/server

So if you change it, you'll need to restart nginx.


Create these directories (I should have these as part of deploy...)
* shared/log
* shared/tmp/pids
* shared/tmp/sockets


That's it!


# Running

These scripts should work for rvm if you just change the run-puma to do the rvm stuff instead of rbenv.

These scripts make it possible to run jungle init scripts with a capistrano 3 and rbenv setup.

You can then start puma with either of these:

* /etc/init.d/puma start (on server)
* cap staging puma:start
* cap staging puma:status


The problem with existing scripts is that:

1.  Scripts assume location of pid and statefile is in subdirectory of app, whereas in capistrano 3 it is usually in shared/something... So, I modified puma script to take log and statefile as parameters.
2. they don't work with rbenv and rvm because
  1. the pumactl doesn't have bundle exec user environment (so I modified script to set the user before running)
  2. run-puma doesn't get the deploy user environment (so added the shell stuff to get rbenv setup before executing)


Changes to the files:

* Modified /etc/puma.conf to take a 2 extra parameters: pid_file, state_file (other cleanups)
* Modified /etc/init.d/puma to take the extra parameters, and also to run pumactl in user context so bundle exec works
* Modified run-puma to set rbenv environment before running bundle exec

So, if you like this star it.

"Puma: threads are the new NIO :) " 
Thomas O'Rourke
<thomas.orourke@upstreaminno.com>
