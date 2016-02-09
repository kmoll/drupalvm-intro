# Introduction to DrupalVM

Full Drupal VM documentation can be found at:  
http://docs.drupalvm.com/en/latest/

This guide it meant to expand upon the [Quick Startup Guide](https://github.com/geerlingguy/drupal-vm#quick-start-guide)
to help you install and configure a VM for Drupal 8 development.  
I've tried to include some helpful hints with things I've learned along the way.

If you have any issues, the links listed along with the topics should provide a bit  
more information if needed.

__* The instructions here are based on using a Mac Host operating system.__

## Install Dependencies
*** All current versions as of: 02/08/2016

### Virtualbox:

Download the package and walk through installer  
https://www.virtualbox.org/wiki/Downloads

_current version at time: **5.0.14**_

### Vagrant:

Download the package and walk through installer  
https://www.vagrantup.com/downloads.html

_current version: **1.8.1**_

### Git:

For information on how to install latest version of git and keep it updated see:  
http://blog.grayghostvisuals.com/git/how-to-keep-git-updated/

```
brew update
brew upgrade git
```

_current version: **2.7.1**_

### Ansible:
Tutorial for installing:
https://valdhaus.co/writings/ansible-mac-osx/

##### Using PIP:

Install [Xcode](https://developer.apple.com/xcode/)
```
sudo easy_install pip
sudo pip install ansible --quiet
```

##### Using homebrew:
```
brew install ansible
```

_current version: **2.0.0.2**_

### Drush:
I like to install with composer:

To install composer:
http://symfony.com/doc/current/cookbook/composer.html
http://whaaat.com/installing-drush-8-using-composer

Use curl to get the installer, and move the create phar file to the correct bin  
This will allow you to be able to use the `composer` command from your terminal
```
$ curl -sS https://getcomposer.org/installer | php
mv composer.phar /usr/local/bin/composer
```

Add Compose's vendor/bin directory to your PATH  
Edit your `./bash_profile` file and add
```
$ export PATH="$HOME/.composer/vendor/bin:$PATH"
```

Now you can install drush via composer:
``` 
composer global require drush/drush:dev-master
```

Once you've done that you can install (or updated) drush with:

```
composer global update
```

Here's a good reference to the difference between using composer install vs. composer update:  
https://adamcod.es/2013/03/07/composer-install-vs-composer-update.html

_current version: **8.0-dev**_

## Setting up your host

### Host Directories:
Create two directories in home folder :

```
mkdir Sites VirtualMachines
```

One directory to house the virtual machines  
One directory to house your projects file system  

navigate to the vm directory  
```
cd ~/VirtualMachines
```

Clone DrupalVM from the git repo  
```
git clone https://github.com/geerlingguy/drupal-vm.git
```

Install needed ansible roles form anisble galaxy
```
sudo ansible-galaxy install -r provisioning/requirements.yml --force
```

### Configure drupalvm:
```
cp example.config.yml config.yml
cp example.drupal.make.yml drush.make.yml
```

The example config file and drush make is already set up to install Drupal 8.  Double check this is the case by opening the files in a text editor.

### Creating the VM:
Once the above pre requisites are installed and you've configured your VM you can create your vm with one command
```
vagrant up
```

The first time you run this it is going to take a while to run.  It will need to download the Vagrant box that contains the OS image and provision  
the OS to install all packages needed.

Once it is done, your VM is up and running and you will have a working instance of D8.

Open your hosts file in a text editor (you will need admin rights)

```
sudo vim /etc/hosts
```

And add the line
````
192.168.88.88    drupalvm.dev
```

Or you could install a vagrant plugin that will manage your hosts file for you
```
vagrant plugin install vagrant-hostsupdater
```

This will allow you to point your web browser on your `http://drupalvm.dev` and it will serve the site from your virtual machine

Drupal's code base is created in shared folder between your Virtual Machine and your Host Machine.  That means you have access to  
the files directly on your host machine.

Point your favorite text editor or IDE to `~/Sites/drupalvm` and you will see your drupal code base and can edit away!

### Drush Aliases:
Drupal VM will also create drush aliases for you so you can run Drush from your host computer and never have to deal with loging into the VM
  
To Run drush commands on the VM just use the alias before the command
```
drush @drupalvm.dev cache-rebuild
```

### Accessing the database:

If you have the adminer app listed in the `installed_extras` section of `config.yml` you can access the db through the web interface with
```
http://adminer.drupalvm.dev
```

## Useful Tips

Following just the steps above you can get a Drupal 8 development environment going in no time with all the defaults  
But over time I've found some updates that has made it a bit easier for me to work with Drupal VM and to have  
multiple VM's going for different projects.  Here is a list of things I like to do normally when creating a VM  
with Drupal VM

##### Update the host-only IP address of the VM

Drupal VM, by default, uses the host-only network adaptor.  The default IP address is `vagrant_ip: 192.168.88.88`   
If you end up running multiple instances of Drupal VM you will need to update the IP address so there won't be any  
conflicts.  Simply look for this line in `config.yml` and update the address:
```
vagrant_ip: 192.168.88.88
```

##### Update the host name and machine name of the VM

I like to name my VM's different from the default to match the project I am setting the machine up for.  
To change the name, you can update the following lines in `config.yml` to whatever works for you.  
If you have multiple machines, make sure you set different values here for each.
```
vagrant_hostname: drupalvm.dev
vagrant_machine_name: drupalvm
```

##### Install PHP Unit
Drupal 8 uses PHPUnit and many of the libraries installed will also have Unit tests which can be run with PHPUnit

To have Drupal VM install php unit, find the line in `config.yml` and uncomment (remove the `#` from the beginning of the line)    
```
# composer_global_packages:
#  - { name: phpunit/phpunit, release: '@stable' }
```

##### Open port 3306

Although DrupalVM offers a web interface to see the database and run sql commands. I like to use applications on my host  
machine to interact with the DB.  In order to do this (and not have to have your app SSH into the machine), you can open  
port 3306 on the VM which will allow your host application to connect to the DB on your VM.

To do so find the list of firewall tcp ports in `config.yml` and add -3306 to the list:
```
firewall_allowed_tcp_ports:$
- "22"
- "25"
- "80"
- "81"
- "443"
- "4444"
- "8025"
- "8080"
- "8443"
- "8983"
```

##### Increase php_memory_limit

I like to increase PHP's memory limit (in development) in case you are testing API's reading in datasources that can be large.  
To increase the memory limit in the VM find the block of code in `config.yml` that reads  
```
php_memory_limit: "512M"
```

I usually like to set this to 512M.  In most normal cases 192M is plenty, but I don't like to hit the limit when developing and  
have to go back and change it.  

##### Increase mysql_allowed_packet_size

I find that I am not always starting with a fresh install of Drupal and sometimes want to set up an existing site on a  
new VM.  To do this you need to import the database (or use `drush archive-restore` which does it for you).  In this case  
you will almost always run into a max allowed packet size error.  So I always update this.  To do so, find the line that reads  
```
# MySQL Configuration.
mysql_root_password: root
mysql_slow_query_log_enabled: true
mysql_slow_query_time: 2
mysql_wait_timeout: 300
```

And add:  
```
mysql_max_allowed_packet: "512M"
```

##### Set up more than 1 site
__*** Note that if you add the below items after you've already created the machine, you will  
have to re-provision the machine so that the ansible scripts run again creating the new configurations.__

__*** If you added new sync folders, you will have to halt and relaod your machine.  See the commands below.__

If you want to set up more than 1 site on your VM, you need to do 3 things
1. Set up the sync folder
2. Add an apache vhost (or nginx)
3. Add another database


####### Add additional sync folders
For #1 you want to find the block of code that specifies the sync folders:  
```
vagrant_synced_folders:$
# The first synced folder will be used for the default Drupal installation, if$
# build_makefile: is 'true'.
  - local_path: ~/Sites/drupalvm
    destination: /var/www/drupalvm
    type: nfs
    create: true
    id: drupalvm
 ```
 
 All you need to do is set up another block like this and chance the values

####### Add additional apache vhosts

Second step to add a second site is to add an apache vhost.  Find the block that lists the vhosts:   
```
apache_vhosts:$
  - servername: "{{ drupal_domain }}"
    documentroot: "{{ drupal_core_path }}"
   extra_parameters: |
      ProxyPassMatch ^/(.*\.php(/.*)?)$ "fcgi://127.0.0.1:9000{{ drupal_core_path }}"
 ```

Add another value and update the server name and document root for the new site.

####### Add additional databases

To add a database you need to find the block of code in `config.yml` that reads:  
```
mysql_databases:
- name: "{{ drupal_mysql_database }}"
  encoding: utf8
  collation: utf8_general_ci
```

Add in a section for your new db

## Helpful Vagrant commands

### Start/Create the VM
```
vagrant up
```

### Shut down the VM
```
vagrant halt
```

### Reload the VM
```
vagrant reload
```

### Re-Provision the machine (already running machine)
```
vagrant provision
```

### Re-Provision the machine (not yet running)
```
vagrant up --provision
```

### Reload and Provision
Sometimes I find I run into some issues when the script tries to create mysql users and it stops executing.  If that happens run:  
```
vagrant reload --provision
```

For a full list of commands and documentation for vagrant see:
https://www.vagrantup.com/docs/
