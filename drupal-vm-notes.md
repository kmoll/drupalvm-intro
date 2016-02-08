# Install Dependencies
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

# Setting up your host

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
````

### Useful Tips

Following just the steps above you can get a Drupal 8 development environment going in no time with all the defaults  
But over time I've found some updates that has made it a bit easier for me to work with Drupal VM and to have  
multiple VM's going for different projects.  Here is a list of things I like to do normally when creating a VM  
with Drupal VM

##### Update the host-only IP address of the VM

##### Update the host name and machine name of the VM

##### Install PHP Unit

##### Open port 3306

##### Increase php_memory_limit

##### Increase mysql_allowed_packet_size

##### Set up more than 1 site

####### Add additional apache vhosts

####### Add additional sync folders

####### Add additional databases




