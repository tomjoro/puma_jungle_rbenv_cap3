puma_jungle_rbenv_cap3
======================

Jungle init scripts for puma with rbenv and cap3

These scripts make it possible to run jungle init scripts with a capistrano 3 and rbenv setup.

The problem with existing scripts is that
  1) they assume location of pid and statefile is in subdirectory of app, whereas in capistrano 3 it is usually in shared/something...
  2) they don't work with rbenv and rvm because
	a) the pumactl doesn't have bundle exec user environment
	b) puma-run doesn't get the deploy user environment

Modified /etc/puma.conf to take a 2 extra parameters: pid_file, state_file

Modified /etc/init.d/puma to take the extra parameters, and also to run pumactl in user context so bundle exec works

Modified puma-run to set rbenv environment before running bundle execa


So, if you like this that's great. Maybe I should submit a pull request to the jungle scripts.

I love PUMA! Threads are the new NIO :)

Thomas O'Rourke
<thomas.orourke@upstreaminno.com>
