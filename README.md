# Linux Server Configuration
This is the repo for Udacity's Linux Server Configuration Project. In the project, I Configured Linux server and deployed [Catalog Python App](https://github.com/vvkvivekl/catalogAWS) to server.

This repo contains guidance to Linux Server Configuration.

## Table of Contents

* [Basic Information](#basic-information)
* [Instructions](#instructions)
* [Creator](#creators)

## Basic Information

This project is build on [Amazon Lightsail](https://lightsail.aws.amazon.com/) Linux/Unix Ubuntu 16.04 LTS.
* **Server IP**: 13.127.112.206
* **SSH Port**: 2200
* **SSH User**: grader
* **Server URL**: http://13.127.112.206.xip.io

## Instructions

* **Create [Amazon Lightsail](https://lightsail.aws.amazon.com/) Linux/Unix Ubuntu 16.04 LTS Server**.
* **Connet from SSH**
  ```
  $ ssh root@13.127.112.206
  ```
  * Updating system
    ```
    $ apt update && apt upgrade
    ```
  * Reboot
    ```
    $ reboot
    ```
  * Changing SSH Port
    * Open the `/etc/ssh/sshd_config`
      ```
      $ nano /etc/ssh/sshd_config
      ```
    * Find the line Port 22
    * change it to Port 2200 (Remove `#`, if any)
    * Save file (`ctrl + o`) close nano (`ctrl + x`)
    * Exit SSH 
      ```
      $ exit
      ```
   * Reconnect SSH from Port 2200
     ```
     $ ssh root@206.189.151.124 -p 2200
     ```
     
 * **Configure Timezone**
   ```
   $ sudo dpkg-reconfigure tzdata
   ```
   Selected `Asia/Kolkata`
   
 * **Firewall**
   * allow only incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123)
     ```
     $ ufw allow 2200/tcp
     $ ufw allow 80/tcp
     $ ufw allow 123/udp
     ```
   * Enable Rules
     ```
     $ ufw enable
     ```
   * Firewall Status
     ```
     $ ufw status
     ```
 * **Creating User**
   * Adding New User
     ```
     $ adduser grader
     ```
 * **Adding User to Sudo group**
   ```
   $ usermod -aG sudo grader
   ```
 * **SSH access to User**
   ```
   $ su - grader
   ```
 * **Allow SSH access to the user grader, on Key**
   ```
   $ mkdir .ssh
   $ chmod 700 .ssh
   $ cd .ssh/
   $ touch authorized_keys
   $ chmod 644 authorized_keys
   ```
 * **Create Local Key (RSA Key)**
   ```
   $ ssh-keygen
   ```
   Note: Run this on Local machine
 * **Copy content of `*.pub`**
 * **Paste in server**
   ```
   $ nano authorized_keys
   ```
 * **Disabling Root Login**
   ```
   $ ssh root@206.189.151.124 -p 2200
   $ nano /etc/ssh/sshd_config
   $ service ssh restart
   $ exit
   ```
 * **Installing Apache Web Server**
   ```
   $ sudo apt update
   $ sudo apt install apache2
   ```
 * **Installing pip3**
   ```
   $ sudo apt install python3-pip
   ```
 * **Installing Git**
   ```
   $ sudo add-apt-repository ppa:git-core/ppa
   $ sudo apt update
   $ sudo apt install git
   ```
 * **Setting Up Apache to Run the Flask Application**
   * Installing `mod_wsgi`
     ```
     $ sudo apt install libapache2-mod-wsgi-py3
     $ sudo service apache2 restart
     ```
 * **Cloning the Item Catalog**
   ```
   $ cd /var/www/
   $ sudo mkdir catalog
   $ cd catalog/
   $ sudo git clone https://github.com/vvkvivekl/catalogAWS.git
   ```
 * **Installing virtualenv and Required softwares**
   ```
   $ sudo pip3 install virtualenv
   $ cd /var/www/catalog/
   $ python virtualenv venv
   $ virtualenv venv
   $ chmod -R 777 venv
   $ source venv/bin/activate
   $ pip install -r catalogAWS/requirements.txt
   $ deactivate
   ```
   
   * Install Softwares to run Application.
     * Install `pip`
       ```
       $ sudo apt install python-pip
       ```
     * Install other required software's
					  ```
					  pip install --upgrade Flask SQLAlchemy httplib2 oauth2client requests psycopg2 psycopg2-binary
					  ```
# Configure wsgi / Apache
   In `$ nano /var/www/catalog/catalogAWS.wsgi` Copy and Paste
   ```
   #! /usr/bin/python
   import sys
   import logging
   logging.basicConfig(stream=sys.stderr)
   sys.path.insert(0,'/var/www/catalog/')
   from catalogAWS import app as application
   application.secret_key = 'super_secret_key'
   ```
   In `$ nano /etc/apache2/sites-available/catalogAWS.conf` Copy and Paste
   ```
   <VirtualHost *:80>
      ServerName 13.127.112.206
      ServerAlias 13.127.112.206.xip.io
      ServerAdmin vvk.vivek.v@gmail.com
      WSGIDaemonProcess catalog python-path=/var/www \
        python-home=/var/www/catalog/catalogAWS/venv
      WSGIProcessGroup catalog
      WSGIScriptAlias / /var/www/catalog/catalogAWS.wsgi
      <Directory /var/www/catalog/catalogAWS/>
          Require all granted
      </Directory>
      Alias /static /var/www/catalog/catalogAWS/static
      <Directory /var/www/catalog/catalogAWS/static/>
       Require all granted
      </Directory>
      ErrorLog ${APACHE_LOG_DIR}/error.log
      LogLevel warn
      CustomLog ${APACHE_LOG_DIR}/access.log combined
   </VirtualHost>
   ```
 * **Enable the virtual host**
   ```
   $ sudo a2ensite catalogAWS
   $ sudo service apache2 restart
	  ```
Note: You might still see the default Apache page despite setting everything up correctly. To resolve it and see your Flask app running, run the following commands in order:
```
$ sudo a2dissite 000-default.conf
$ sudo service apache2 restart
``` 

## Third Party Resourses
   - [Deploy a Flask Application on an Ubuntu VPS](https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps)
   - [Running a Flask application under the Apache WSGI module](https://www.jakowicz.com/flask-apache-wsgi/)

## creators

* Vivek Lingayat (Vvk)
    - [GitHub](https://github.com/vvkvivekl)
    - [Twitter](https://twitter.com/vvksl)
    - [Facebook](https://fb.com/vvksl)
