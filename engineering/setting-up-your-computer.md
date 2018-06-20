# Setting up your computer

Before you can start coding you will need to set up a development
environment. To do this follow the instructions below:

1. All the repos use git so install it with you package manager.
2. To run the stack locally you first need to [Install Docker](https://docs.docker.com/install/)
3. Even if you prefer coding in Emacs/VIM, InteliJ has some great debugging functionality so go ahead and [Install InteliJ](https://www.jetbrains.com/idea/)
4. To set up the containers you will need `docker-compose`, Install it
   using `sudo curl -L
   https://github.com/docker/compose/releases/download/1.21.2/docker-compose-$(uname
   -s)-$(uname -m) -o /usr/local/bin/docker-compose`
5. `mkdir -p ~/code/z3; cd ~/code/z3/; git clone git@github.com:zensum/dev-env; cd ~/code/z3/dev-env; ./clone.sh; ./setup.sh`
6. Now wait 10 minutes for everything to build.
7. All services should now be running.