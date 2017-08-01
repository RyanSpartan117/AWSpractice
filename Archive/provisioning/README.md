# Provisioning

## Timings

40 - 60 Mins

## This lesson includes

* What is provisioning?
* Syncing folders
* Installing and configuring programs
* Bash scripts

Put simply, provisioning is the act of installing and configuring everything you need to run your application. So far we haven't done a great deal of that. We installed Nginx so we could test our networking. But beyond that, our environment won't do much for our developers. We need to install some programs.

Before we can setup an environment, though we need to know what we're setting it up for! How else will we know what to install. 

In the starter code you will find a node app that we can use for testing and a starter Vagrant file.

We've got this code on our machine but how do we get it on to the virtual machine so the devs can work with it?

Synced folders!

## Syncing folders

We've got a box now. And that's great. But how will the developers get their code into it? Vagrant can move the code in to the box for us so we can run it from inside the dev environment using **synced folders**.

You can think of a synced folders as a folder that exists in two places. Changing it in one place also changes the other. 

> NOTE : Syncing is two way!! so if you delete something that's synced in your Dev environment you'll also delete it on your own computer! so be careful.

Add the following line to your Vagrantfile:

```ruby
Vagrant.configure("2") do |config|

  config.vm.box = "ubuntu/xenial64"
  config.vm.network "private_network", ip: "192.168.10.100"
  config.hostsupdater.aliases = ["development.local"]
  
  # Synced app folder
  config.vm.synced_folder ".", "/home/ubuntu/app"
 
end
```

This line tells vagrant to take the current folder ( . ) and to sync it with a folder called /home/ubuntu/app on the virtual machine.

We now need to restart our box to make the changes take effect.

```bash
vagrant destroy
vagrant up
```

If you login to your virtual machine you should see the code in the /home/ubuntu/app directory!

Ok we're good to start provisioning now.


## Communicating with the Developers

Even though you have all trained as developers it's entirely likely that you'll be given an app written in a language you don't know. This is why good communication with the Devs who built it is crucial. 

A few questions that should get you started:

* What framework is it built in, if any?
* Are there any specific setup requirements or steps
* Are there any packages that you need installed?
* Which versions of those packages are you expecting?
* What will the app look like when it's working.

Luckily for us in this case, we have written a set of tests that will describe whether the environment looks like it should or not. We'll look at how to write these ourselves later on but for the moment we can use them to see what we need.

In the starter code environment folder you'll see a folder called spec-tests. Open that up and run the tests like this:

```bash
rake spec
```

You should see something like this:

```bash
Failed examples:

rspec ./spec/default/sample_spec.rb:4 # Package "nginx" should be installed
rspec ./spec/default/sample_spec.rb:8 # Service "nginx" should be running
rspec ./spec/default/sample_spec.rb:9 # Service "nginx" should be enabled
rspec ./spec/default/sample_spec.rb:13 # Port "80" should be listening
rspec ./spec/default/sample_spec.rb:17 # Package "nodejs" should be installed
rspec ./spec/default/sample_spec.rb:21 # Command "nodejs --version" stdout should match /v6./
rspec ./spec/default/sample_spec.rb:25 # Package "pm2" should be installed by "npm"
```

So we've got 9 tests and 7 failures to fix.

> QUESTION: We haven't installed anything yet. Why have we only got 7 failures?

It's the git tests which are passing because git is installed by default on this verison of ubuntu.

Let'start installing some stuff and fixing things.

## Installing programs

We briefly used apt-get to install Nginx in the previous lesson. Most computer users are used to installing programs through the GUI. On Mac we use the app store. Windows has the Microsoft installer. Well, command line linux has apt-get.

Because we like to make things difficult, certain flavours of linux ( which you might well have to use ) use another program called yum and theres another one after that called RPM.

Luckily, they generally all follow the same prinicipals so we'll just learn apt-get for the moment as that's what Ubuntu uses.


### The database

Apt keeps a local database of all the programs its able to install. The most common task we do is to make sure this database is up to date with information about all the latest versions of all the programs.

It's quite a simple command:

```bash
sudo apt-get update
```

> NOTE We need the sudo because it's an action that will change administrator owned files. 

Our database should now have all the information about any new packages or versions of packages it needs.

It's always a good idea to do this regularly on your environment.

> NOTE this doesn't actually update any of the apps. It only updates the information about available apps.  

### Upgrade

Now that apt knows about the latest versions of all our installed packages we can upgrade them.

```bash
sudo apt-get upgrade
```

> NOTE : You should never do this on a live production environment without testing it first. New packages can introduce breaking changes. 

Now that we have the latest versions of everything that was already installed let's try and install a few things and fix some of our broken tests.

### Installing and sources

By far the most common way you'll install a new program or service is to look up the docs for that program and follow the instructions. Some instructions may have some extra steps that look a bit odd but essentially they all do the same thing:

* Make sure apt has the program information in the database
* Install any other programs that this one needs
* Install the program itself

To install Nginx we didn't need to do the first two steps because apt already knows about Nginx and it has no dependencies.

Let's reinstall Nginx :

```bash
sudo apt-get install nginx -y
```

The ``-y`` here tells apt not to ask any questions when it installs. This is ***really*** important for us because we like to automate things. And anything that needs a user to say yes to something can't be run automatically.

## Bash scripts

Right, we still have the problem that everything we installed was destroyed when we restarted the box. Let's fix that now. 

We can create files that are a collection of any commands we would run in the terminal. These are known as bash scripts and you'll pretty soon become an expert in writing them.

They are actually very simple. Let's create one that will provision our machine:

```bash
cd environment
touch provision.sh
```

Open the file and add the following:

```bash
#!/bin/bash
```

This first line is actually very clever. It tells linux which program it should use to run this file. We could have made it ruby or perl or anything else. But we're writing a bash script. It's just a shortcut but it's useful.

Let's add our update, upgrade and nginx install to the file:

```bash
#!/bin/bash

sudo apt-get update -y
sudo apt-get upgrade -y
sudo apt-get install nginx -y
```

This file won't be run automatically. But we can ask Vagrant to run it for us when we spin up the machine. Let's add the line to our Vagrantfile:

```ruby
Vagrant.configure("2") do |config|

  config.vm.box = "ubuntu/xenial64"
  config.vm.network "private_network", ip: "192.168.10.100"
  config.hostsupdater.aliases = ["development.local"]
  
  # Synced app folder
  config.vm.synced_folder ".", "/home/ubuntu/app"

  # Provisioning
  config.vm.provision "shell", path: "environment/provision.sh"

end
```

Destroy your box and ``vagrant up`` again. This time you should see your provisioner running and the packages being updated and installed!

If you check development.local you should see the start page too!

Let's try running our tests and see if we've made a difference.

```bash
Finished in 0.59581 seconds (files took 11.09 seconds to load)
9 examples, 3 failures
```

Not bad. Four of our tests are now passing. Those that check Nginx is installed and running and that it's listening on port 80.

 > EXERCISE ( 10 Minutes ) : Install nodejs as well.

You can install nodejs like this:

```bash
sudo apt-get install nodejs -y
```

But if you rerun the tests you'll find the nodejs test is still failing. 

```bash
Finished in 0.57328 seconds (files took 11.81 seconds to load)
9 examples, 2 failures
```

> QUESTION : Why is it failing?

It's failing because even though nodejs was installed we don't have the correct version!


## Sources

The database that apt uses relies on a list of websites that it can go to to get information about packgages. Sometimes we need to add some more stuff to it so that we can access other versions of things or sometimes to be able to access packages at all!

This is our problem with node. Ubuntu comes with a source for a very, very old version of nodejs. We need to update this.

Now, luckily when you install most packages, the websites for those packages will tell you exactly what you need to do. So this is what we have to do to install the correct version of Node.

> EXERCISE ( 10 Minutes ) : Research on google how to install Nodejs 6.x on ubuntu xenial

Hopefully you'll have found the official docs or a good tutorial and it will have shown you this:

```bash
sudo apt-get install python-software-properties
curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -
sudo apt-get install nodejs -y 
```

Let's look at these steps in regards to our list of steps:

```bash
# Install things that nodejs needs
sudo apt-get install python-software-properties

# Make sure that apt has the info it needs to install nodejs. 
# This uses a custom built setup program provided by node
curl -sL https://deb.nodesource.com/setup_6rack .x | sudo -E bash -

# Install node
sudo apt-get install nodejs -y 
```


> NOTE : It's important to note here that you will see many different ways of installing programs but the concepts will be the same. The installation steps should always be documented by the package provider and some good googling will be necessary.

Let's restart and check our tests!

```bash
Finished in 1.73 seconds (files took 10.09 seconds to load)
9 examples, 1 failures
```

Getting closer!

> EXERCISE (20 Minutes) : Research and install pm2 and make the test pass.

This final step proves why it's important to have a good understanding of many languages. PM2 is a package installed using node's own package manager, npm, rather than using apt.

To install an npm package to be used globally on the system we do the following:

```bash
sudo npm install pm2 -g
```  

As always restart and run the tests.

```bash
Finished in 2.13 seconds (files took 10.33 seconds to load)
9 examples, 0 failures
```

So we're looking pretty good!

## Test the app

We've installed enough to be able to test the running of the app. Let's login to the box and try and spin it up.

As with all things DevOps having the knowledge of how to start the app is something you will come to know but also may need to speak with the dev who built it about.

In this case the following steps are need to start the app. SSH to your vagrant machine and run the following:

```bash
cd /home/ubuntu/app

# install any node modules that the developer has asked for
npm install

# start the app
node app.js

Your app is ready and listening on port 3000
```

You should now be able to access the app on ``http://development.local:3000``

## Other installers

Apt-get is ***not*** the only way to install things. It's the easiest method if there are packages available. Some packages may need to be downloaded and installed manually. Some programs will have their own installers ( ruby being one of them ).

These installation methods will, 99% of the time, come with fill instructions for your platform. So it's simply a case of googling and following carefully.

## Summary

You just:

* Learned a simple process for setting up environments
* Learned how to use bash scripts to collect groups of commnds together
* Saw how to get your code on to a vagrant machine
* Had a brief intro to testing server architectures

	


