puma_jungle_rbenv_cap3
======================

Jungle init scripts for puma that play nice with rbenv and capistrano 3

Note: These were tested on Ubuntu 14.04

Also, these scripts should work for rvm if you just change the run-puma to do the rvm stuff instead of rbenv.

These scripts make it possible to run jungle init scripts with a capistrano 3 and rbenv setup.

So.. you can install you scripts as instructed in the puma site:
* https://github.com/puma/puma/tree/master/tools/jungle/init.d
And you can setup your capistrano 3 like here:
* https://github.com/seuros/capistrano-puma

You can then start puma with
	/etc/init.d/puma start
OR
	cap staging puma:start
	
So they play nice together. If you server reboots, your puma restarts nicely, but you can still use capistrano 3 after that to get status and restart at anytime.


The problem with existing scripts is that
  1) they assume location of pid and statefile is in subdirectory of app, whereas in capistrano 3 it is usually in shared/something...
  
  2) they don't work with rbenv and rvm because

	a) the pumactl doesn't have bundle exec user environment

	b) run-puma doesn't get the deploy user environment

* Modified /etc/puma.conf to take a 2 extra parameters: pid_file, state_file
* Modified /etc/init.d/puma to take the extra parameters, and also to run pumactl in user context so bundle exec works
* Modified puma-run to set rbenv environment before running bundle execa


So, if you like this that's great. Maybe I should submit a pull request to the jungle scripts.

"I love PUMA! Threads are the new NIO :) " <-- (c) Thomas O'Rourke (you can quote me on that)

Thomas O'Rourke
<thomas.orourke@upstreaminno.com>
