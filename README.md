# Hack Lang Example Site

## Why does this site exist?

The purpose of this site is to provide examples of how to use the Hack Language. This site should help in the following ways:

1. This site is written in Hack and is open source. You can browse the source code for real world examples
2. You can clone and deploy this site yourself. This site is designed to be simple to make this as easy as possible.
3. The contents of this site consist of a Cookbook of Hack Recipes. Each Recipe is a short Hack example that solves some common, interesting typing problem.

If you haven't already, I recommend checking out the [official documentation](http://hhvm.com/manual/en/index.php).

## Is this site hosted anywhere? ##

Yes, at [cookbook.hacklang.org](http://cookbook.hacklang.org)

## Deploying the site

This site is intended to be easy to deploy. Install and configure HHVM and your webserver of choice, clone the GitHub, install the dependencies with [Composer](https://getcomposer.org) and you should be done!

### Installing HHVM & Webserver

You can either install one of the many HHVM packages or build it from source yourself. I recommend following [this blog post](http://www.hhvm.com/blog/1817/fastercgi-with-hhvm) to set up hhvm with FastCGI. If you're adventurous, the [nightly builds](http://www.hhvm.com/blog/3203/nightly-packages) are pretty cool too.

### Cloning the hack-example-site GitHub

If you're reading this README you probably already found the source code, but the source code lives [here](https://github.com/facebook/hack-example-site).

### Installing Composer dependencies ###

This site uses [Composer](https://getcomposer.org) to manage it's dependencies. If you're new to Composer, check out the Composer [getting started guide](https://getcomposer.org/doc/00-intro.md).

### Example setup for HHVM + Nginx on Ubuntu 13.10

Nginx is a popular webserver and what I used when building this site. Here are instructions for how I set up my environment.

#### Clone the GitHub
You can clone it wherever you like, but for this example I'm putting it in ~/hack-example-site

    cd ~
    git clone https://github.com/hhvm/hack-example-site.git
    
#### Install Nginx

    sudo apt-get install nginx

#### Install HHVM
[Instructions copied from HHVM wiki](https://github.com/facebook/hhvm/wiki/Prebuilt-Packages-on-Ubuntu-13.10)

    wget -O - http://dl.hhvm.com/conf/hhvm.gpg.key | sudo apt-key add -
    echo deb http://dl.hhvm.com/ubuntu saucy main | sudo tee /etc/apt/sources.list.d/hhvm.list 
    sudo apt-get update
    sudo apt-get install hhvm

#### Install dependencies ####
The Hack Example Site uses [Composer](https://getcomposer.org) to manage its dependencies. Composer is easy to install and easy to use. To install Composer, you just curl the installation script and pipe into `php` (the `hhvm` executable cannot read a script from `STDIN` currently). To install the dependencies you just run the `install` command; we are invoking the downloaded `composer.phar` using `hhvm` to make sure the engine dependency in `composer.json` is satisfied. For more information about this step, see the Composer [getting started guide](https://getcomposer.org/doc/00-intro.md#installation-nix).

    cd hack-example-site
    sudo apt-get install curl
    curl -sS https://getcomposer.org/installer | php
    hhvm composer.phar install

#### Copy the HHVM config file
There is a simple HHVM config in this repo, which you can use. I just overwrite the server.hdf file, since that's the config that init.d uses. You can always edit the service or start hhvm yourself if you'd rather not overwrite server.hdf

    // Assuming you cloned hack-example-site to ~/hack-example-site
    // If you don't want to overwrite server.hdf you can always point hhvm 
    // to a config elsewhere
    sudo cp ~/hack-example-site/hhvm.hdf /etc/hhvm/server.hdf

#### Set up the Nginx config

    // Assuming you cloned hack-example-site to ~/hack-example-site
    sudo cp ~/hack-example-site/nginx.conf /etc/nginx/sites-available/hack-example-site
    sudo ln -s /etc/nginx/sites-available/hack-example-site /etc/nginx/sites-enabled/hack-example-site
    // Disable the default config. Or don't. It's up to you.
    sudo rm /etc/nginx/sites-enabled/default
    // Update the root and fastcgi_param directives to point to ~/hack-example-site
    sudo vim /etc/nginx/sites-available/hack-example-site
    // Verify that the config parses
    sudo nginx -t

#### Start everything up
    sudo service hhvm-fastcgi start
    sudo service nginx start

#### Load a page in your browser!

Try going to `localhost` in your browser of choice

### Example setup for Heroku

#### Get a Heroku account and install the toolbelt

If you don't already have a Heroku account, start here: https://id.heroku.com/signup

#### Create a Heroku app, push the code, and open the app in your browser

    heroku create
    git push heroku master
    heroku open

#### Optionally, add a "Procfile" telling Heroku to start your app using Nginx instead of the default Apache

    echo 'web: vendor/bin/heroku-hhvm-nginx' > Procfile # could also be heroku-hhvm-apache2 instead
    git add Procfile
    git commit -m "use Nginx on Heroku"
    git push heroku master
