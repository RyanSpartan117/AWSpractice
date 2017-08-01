# Development Environments

## Timings

This lesson should take between 60 and 90 minutes to complete.

## Pre-requisites

* None

## This lesson covers

* What is a development environment
* Important features
* Creating an environment
* Installing Programs
* Synced folders
* Networking and IPs
* Hosts

As DevOps engineers, it's your job to make sure that the developers can do their work within the system that you create. They will need a place in which they can write, run and test code. That place is called a development environment. Essentially it's just a space on some machine that has all the programs installed and properly configured that the developer needs to write and run their code.

Usually developers will have some idea of how to set this up for themselves. But  when they work with DevOps systems it's something that we will want to control so that we can make sure it works well with things we will build later.

## Important features

There are a few key features that are important for a development environment:


### It should be the same for everyone, everywhere

Creating shareable development environments really came about to solve a very common Dev problem. The dreaded "It works on my machine". If each developer is left to configure their own machine some may have programs installed that others don't or have them set up differently. It only takes the tiniest difference in configuration to break an application. So we need to make sure everyone is working in the exact same environment.

### It should match the production environment as closely as possible

This is important to know now although we will cover it in more detail in later lessons. But for now you should know that anywhere the code runs should be setup the exact same way. Or errors will occur.

### Fast and robust

If the Devs get annoyed with using it because it's slow or broken all the time they will sneakily try to find ways to stop using it. Make sure it's a pleasure to work with.

### It should be easy to update

The developers will be adding new features to their applications and they may need us to make changes that support that. This should be an easy process. Ideally the developers will be able to make the changes themselves and send them to us for approval and inclusion. We'll be using Git to help us with this.

### It should support only one application

It can be very tempting to build an environment that can be used for a bunch of different applications. But this is dangerous. Here are some funny examples that really do happen if you try this:

* Application 1 needs version 1.1 of a program but application 2 needs version 2.0. One of them ends up broken
*  Application 1 needs a program that conflicts with a program that is needed by  Application 2. One of them ends up broken
*  You add a new program for Application 1 to use and without realising you break Application 2.

Save yourself a tonne of grief. One environment for one application.

Let's start building one.

## Creating an environment

The first problem we face is that not every developer uses the same type of computer. Some of them will use Macs, others PCs. Some will be old, some will have the latest OS some won't. It's a nightmare. So we need to take this out of the equation. 

### Vagrant

We're going to use a program called Vagrant to do this. Vagrant is a program that will run on the developers machine to create a "virtual" machine inside it.

> IMPORTANT NOTE : Try not to think that Vagrant is the only way to do this. It's just one of many possible tools we could use. But it's very common and very popular. This goes for every other tool we use too. 

We can control this virtual machine completely. We can install any operating system on it we like and it is kept completely separate and independent of the developer's machine.

Let's install Vagrant now. You can get the program from the following link for Mac, PC or Linux.

[Install vagrant](https://www.vagrantup.com/)

Once you have vagrant installed you can test it by opening a terminal and typing the following:

```bash
vagrant

# You should see the following

vagrant
Usage: vagrant [options] <command> [<args>]

    -v, --version                    Print the version and exit.
    -h, --help                       Print this help.

Common commands:
     box             manages boxes: installation, removal, etc.
     connect         connect to a remotely shared Vagrant environment
     destroy         stops and deletes all traces of the vagrant machine
     global-status   outputs status Vagrant environments for this user
     halt            stops the vagrant machine
     help            shows the help for a subcommand
     hosts           Information about hostnames managed by the vagrant-hosts plugin
     hostsupdater    
     init            initializes a new Vagrant environment by creating a Vagrantfile
     login           log in to HashiCorp's Atlas
     package         packages a running vagrant environment into a box
     plugin          manages plugins: install, uninstall, update, etc.
     port            displays information about guest port mappings
     powershell      connects to machine via powershell remoting
     provision       provisions the vagrant machine
     push            deploys code in this environment to a configured destination
     rdp             connects to machine via RDP
     rebuild         plugin: vagrant-digitalocean: destroys and ups the vm with the same ip address
     reload          restarts vagrant machine, loads new Vagrantfile configuration
     resume          resume a suspended vagrant machine
     share           share your Vagrant environment with anyone in the world
     snapshot        manages snapshots: saving, restoring, etc.
     ssh             connects to machine via SSH
     ssh-config      outputs OpenSSH valid configuration to connect to the machine
     status          outputs status of the vagrant machine
     suspend         suspends the machine
     up              starts and provisions the vagrant environment
     vbguest         
     version         prints current and latest Vagrant version

For help on any individual command run `vagrant COMMAND -h`

Additional subcommands are available, but are either more advanced
or not commonly used. To see all subcommands, run the command
`vagrant list-commands`.
```

### VirtualBox

Vagrant uses another program called VirtualBox to actually create this "virtual" machine. Vagrant basically pulls together a few tools to setup and start a virtual machine. VirtualBox is the tool that actually creates the virtual machine.

Let's install that now too.

[VirtualBox](https://www.virtualbox.org/wiki/Downloads)

We won't actually use VirtualBox directly. Vagrant will handle everything.

### The operating system and boxes

Now that we can create one of these virtual machines the question is, what type of machine do we want it to be? We could install OSX or Windows on it if we really wanted to. But in DevOps it's much, ***much*** more common to use Linux. 

There are many different types of Linux created by different companies. They come with different programs installed and each have their positives and negatives. We shall be using one called Ubuntu. We won't look at the others in this lesson but it's good to keep in mind that they are out there and you may well have to use them too.

Vagrant has a very simple way to set up a new "box" ( this is a common term so look out for it ).

## Getting the app code

It's always best to start testing your environment with a basic test page. We'll be setting a webserver program called Nginx. It comes with default test page when it's running so that will do for us. 

Type the following to setup a new vagrant configuration file:


```bash
vagrant init ubuntu/xenial64

# you should see

A `Vagrantfile` has been placed in this directory. You are now
ready to `vagrant up` your first virtual environment! Please read
the comments in the Vagrantfile as well as documentation on
`vagrantup.com` for more information on using Vagrant.

```

This is quite a simple command:

* We are asking vagrant to create a new configuration file by using the ``init`` command. 
* We are also telling vagrant which type of system we would like. In this case ubuntu/trusty64
* ubuntu/trusty64 is a vagrant box name. Someone has prepared this box for others to use. It has Ubuntu 14.04 installed on it and by using the name here vagrant knows where to go to download it.
* To install different types of operating system we could look up these names on the vagrant boxes website and ask for a different one.

You will see that vagrant has created a file called Vagrantfile in your directory. This file tells vagrant what to "spin up" on our machine. This is great. If we share this file with other people and they use it to start a virtual machine then we should all have the same environment! That's one of our criteria taken care of at least.

If you open the file you will see a ***lot*** of comments. They are helpful, but to keep things simple we want to clear them all out so that the file looks like this:

```ruby
Vagrant.configure("2") do |config|

  config.vm.box = "ubuntu/xenial64"
 
end
```

Do you recognise the syntax? Vagrant config files are written in Ruby!

This is Ruby code just tells Vagrant which box to use. At the moment our box isn't running though. So let's start it now:

```bash
vagrant up
```

> NOTE: This may take some time as it downloads the Vagrant box the first time round. It will be quicker in the future.

> ANOTHER NOTE: You may get a few warnings ( in orange ) as it starts. It's ok to ignore these as long there are no errors ( in red )

We now have a virtual machine running. It's a funny thing because it runs in the background. It won't open a new window and you won't see it in the console. To interact with it we have to use vagrant.

We can log on to the box with the following command:

```bash
vagrant ssh

# You should see 

ubuntu@ubuntu-xenial:~$ 
```
You are now using a terminal ***inside*** the virtual machine!

> EXERCISE (5 Mins) : Have a look around the machine using ls and cd. You can't permanently break the box so feel free to try out anything you like.

The most important directory for us at the moment is the /home/ubuntu directory. Ubuntu is your username while you're inside the box and /home/ubuntu is your users personal directory.

You can type "exit" to get back to your own terminal.

## Installing Nginx

This particular development environment will host a basic html website and for that we need a webserver program. We'll look in more depth at how to install programs in a later lesson. But for now we're going to install a webserver called Nginx ( engine X ).

Inside your vagrant terminal type the following:

```bash
sudo apt-get update -y
sudo apt-get install nginx -y
```

And watch the installation!


## Networking and IPs

At this moment the virtual machine has an IP address on our system that we could use to view the app. The trouble is, we don't know what it is. There are ways to find out but it might not be the same each time as Vagrant assigns one each time we vagrant up. 

Remember we're trying to make this as easy for the developers to use as possible. So let's fix the IP of our virtual machine to be the same every time.

Open the Vagrantfile again:

```ruby
Vagrant.configure("2") do |config|

  config.vm.box = "ubuntu/trusty64"
  config.vm.network "private_network", ip: "192.168.10.100"
 
end
```

This will force our virtual machine to use 192.168.10.100. We could restart now and test this but we've seen how long that takes. So let's do the last bit and test the whole lot together.

## Hosts

We've given the devs an IP to use to view their app but IPs are clunky and unpleasant. It would be great if we could give them a proper web address to use instead. 

Every developer machine has a file called "hosts" that you can use to point web addresses at IPs. It's the first place your browser checks when it's looking for  web addresses.

Updating this would be pretty tricky as it depends on what type of machine the developer has but luckily there is a plugin for Vagrant itself that can take care of it for us. So let's install it and set it up now. From your host terminal ( not inside the virtual machine ):

```bash
vagrant plugin install vagrant-hostsupdater
```

This let's us add some new config to the Vagrantfile:

```ruby
Vagrant.configure("2") do |config|

  config.vm.box = "ubuntu/trusty64"
  config.vm.network "private_network", ip: "192.168.10.100"
  config.hostsupdater.aliases = ["development.local"]
 
end
```

Now let's restart, fire up the app and see if it works! 

```bash
vagrant destroy
vagrant up
```

Now go to your browser and type:

http://development.local

> NOTE : The http:// is important because your browser might think it's search without it.
 
Oh dear! it's not working!

Vagrant creates virtual machines but it can also destroy them! By running Vagrant destroy and then re-upping we've reset the box back to it's original state! That's not good. 

> EXERCISE ( 10 Minutes ) : Log back in to your box now and reinstall nginx.


If you go to development.local now you should see your test page! Clearly having to reinstall everything each time you restart the box isn't great. 

We'll look at how to solve this in the next lesson.

 
 
## Summary

You just:

* Learned that Vagrant creates a "Virtual" machine on your computer
* This virtual machine can be run from any computer and still be the same
* Dev environments should be fast and easy to use
* They should be easy to share and be the same everywhere













 

















