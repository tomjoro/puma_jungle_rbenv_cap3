puma_jungle_rbenv_cap3
======================


You've decided to deploy Rails on the world best production environment. Naturally, you've chosen:
* Ubuntu 14.04
* Nginx (of course! easy decision)
* Puma (threads are the new nio, and Puma is awesomeness)
* Rbenv (ok, you debated about rvm vs rbenv and both will work with these instructions)
* Capistrano 3 (you banged your head against the wall trying to update your cap 2 scripts, but feel you must keep up)
* Systemd (because you realized upstart is a dying thing)
* Pumactl (because pumactl works! and is very nice)

So at the end of the day you *want* you all these to work, and play nicely together
- cap staging deploy
- cap puma:start
- cap nginx:restart
- /etc/init.d/puma start (on server)
- /etc/init.d/puma stop (on server)
- everything should start on server reboot AND you can still use cap puma:restart, etc.
etc.

With this much awesomeness, you assume someone else has this working already. I found lots of different examples, but nothing with this exact stack that worked the way I expected it to. Plus I wanted to use the default scripts, as much as possible.

Here's how you use this setup:
1. First read this about systemd scripts
* https://github.com/puma/puma/tree/master/tools/jungle/init.d

2. Then read this about capistrano 3
* https://github.com/seuros/capistrano-puma

Now do these, but use my scripts instead.

Here's the main (only differences)
1. I symlinked my nginx sites-enabled into the config in my project.
* /etc/nginx/sites-enabled
server -> /home/deploy/apps/server/current/config/nginx.conf
2. These use the *standard* capistrano 3 generated nginx.conf and puma.rb
Just be sure to create the directories 
* shared/log
* shared/tmp/pids
* shared/tmp/sockets 

Also, these scripts should work for rvm if you just change the run-puma to do the rvm stuff instead of rbenv.

These scripts make it possible to run jungle init scripts with a capistrano 3 and rbenv setup.

You can then start puma with
* /etc/init.d/puma start (on server)

OR
* cap staging puma:start


The problem with existing scripts is that
  1) systemd scripts assume location of pid and statefile is in subdirectory of app, whereas in capistrano 3 it is usually in shared/something...
  
  2) they don't work with rbenv and rvm because

	a) the pumactl doesn't have bundle exec user environment

	b) run-puma doesn't get the deploy user environment

Changes to the files:
* Modified /etc/puma.conf to take a 2 extra parameters: pid_file, state_file (other cleanups)
* Modified /etc/init.d/puma to take the extra parameters, and also to run pumactl in user context so bundle exec works
* Modified puma-run to set rbenv environment before running bundle exec

So, if you like this that's great. 

"I love PUMA! Threads are the new NIO :) " (you can quote me on that)
Thomas O'Rourke
<thomas.orourke@upstreaminno.com>
