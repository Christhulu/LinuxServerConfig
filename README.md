Deploying Item Catalog to Linux
===============================

Description
============================
This project is a linux configuration for the `Deploying to Linux` section of the full stack web developer nanodegree from udacity



IP Address to access server: `54.164.37.123`
SSH Port: `2200`
URL: `ec2-54-164-37-123.compute-1.amazonaws.com/MusicStore`
Software installed: Check Third Party Resources and Below
Summary of configurations made: Changed port, added firewall, added user and database wih postgreSQL. Uses WSGI and apache2.
Third party resources: Check Below


**Software Installed**
==========================
Python modules to use
`sudo apt-get install python-psycopg2 python-flask
sudo apt-get install python-sqlalchemy python-pip
sudo pip install oauth2client
sudo pip install requests
sudo pip install httplib2
sudo pip install flask-seasurf`


Flask framework
PostgreSQL (Database software)
Psycopg2 (Python database software)
Apache2 (Deploying application)
Apache and WSGI module (Deploying application)
My item catalog project from earlier: [Music Store](https://github.com/Christhulu/MusicStore)


**Configurations**
==========================

Instructions
============================

Make an SSH key: (I used puttygen and putty since I'm on windows)
there's also a key generator using openssh that people sometimes like
Save the public and the private keys, because you're going to copy the public key in your authorized_keys file on your server

Create a new user named `grader` and give them sudo permissions

Allow user to ssh using same keypair as ubuntu(default login for aws):
You can execute these from the ubuntu user


Disable remote login using root

`sudo nano /etc/ssh/sshd_config`


Restart ssh service
`sudo service ssh restart`

Update all packages
`sudo apt-get update`
`sudo apt-get upgrade`

Change SSH port to 2200 from 22 using 	`sudo nano /etc/ssh/sshd_config`
and changing the port from 22 to 2200

#Check that you can still ssh in on the port

Firewall Configuration
======================
Close all incoming ports: `sudo ufw default deny incoming`
Open all outgoing ports `sudo ufw default allow outgoing`
Open our recently changed SSH port `sudo ufw allow 2200/tcp`
Open http port that our catalog needs to run on: `sudo ufw allow 80/tcp`
Open port for NTP(Network Time Protocol) to run on: `sudo ufw allow 123/udp`
Turn on firewall: `sudo ufw enable`


Install the Apache web server and WSGI module
=============================================
Get apache: `sudo apt-get install apache2`
Get apache's wsgi mod: `sudo apt-get install apache2 libapache2-mod-wsgi`
Enable wsgi with sudo a2enmod wsgi

Database Setup
==============

Install postgreSQL
`sudo apt-get install postgresql postgresql-contrib`


# create a linux user named catalog
sudo adduser catalog

Create postgresql user with database
`sudo -u postgres createuser -P catalog`


Set password to what you want, I chose catalog


Python Modules to install
=========================
`sudo apt-get install python-psycopg2 python-flask
sudo apt-get install python-sqlalchemy python-pip
sudo pip install oauth2client
sudo pip install requests
sudo pip install httplib2
sudo pip install flask-seasurf`


The app
=======
Get git (haha): `sudo apt-get install git`
Install in /var/www/ folder because, this is where apache is able to use by default
`cd /var/www`
`sudo git clone https://github.com/Christhulu/MusicStore.git`


App configurations
===================
Configure config.py using `sudo nano config.py` inside of the MusicStore
Change the database URI to `postgresql://catalog:catalog@localhost/catalog`
The format is: `postgresql://db_name:db_password@localhost/db_name`
Change client_secrets address in views to an absolute address, so change it to `/var/www/MusicStore/client_secrets.json` (That caused me some problems)





Update apache

Populating Database
===================
`python /var/www/MusicStore/lotsofitems.py`

Apache configurations
=====================
#Make a WSGI File in your project

in `/var/www/MusicStore`
Run `sudo nano /var/www/MusicStore/app.wsgi` and copy this in
`#!/usr/bin/python
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/var/www/MusicStore/")
from catalog import app as application
application.secret_key = 'super_secret_key'`


Update apache configuration file
`sudo nano /etc/apache2/sites-enabled/000-default.conf`
Add this alias: `WSGIScriptAlias /MusicStore /var/www/MusicStore/app.wsgi`
Add this directory configuration as well:
`<Directory /var/www/MusicStore/>
Require all granted
</Directory>`

Restart apache
`sudo apache2ctl restart`


Troubleshooting
===============
Check `sudo nano tail error.log` for hints to any problems you might have
Note: you need to add your aws instance's ip/url to your authentication in google developer's console or facebook's console for the javascript origins and redirect URIs


**Third Party Resources**
==========================
PostgreSQL ()
Psycopg2 ()
Apache2 (apache2)
Apache and WSGI module (libapache2-mod-wsgi)
My item catalog project from earlier: [Music Store](https://github.com/Christhulu/MusicStore)