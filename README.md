Linux Server Configuration
===============================
Online server configuration for Item Catalog app.
The code can be found ['here'](https://github.com/huylllooo/Item-Catalog)

## Server Address
IP address : 52.197.2.209

SSH port : 2200

App URL : http://52.197.2.209/

## Software installed
1) apache2
2) postgresql
3) libapache2-mod-wsgi
4) git
5) other dependencies of flask project

## Tasks

### System tasks

1. Update all currently installed packages

	sudo apt-get update
	sudo apt-get upgrade

2. Configure the time zone 'sudo dpkg-reconfigure tzdata'
3. Disable root login
'sudo nano /etc/ssh/sshd_config'
Change 'PasswordAuthentication' to 'no'

### Give user 'grader' access

```bash
$ useradd -m -s /bin/bash grader
$ usermod -aG sudo grader
```

### Set SSH keys for user 'grader'

### Configure ports

Change port to 2200 in 'sshd_config'. Then the restart SSH service:

```bash
$ sudo nano /etc/ssh/sshd_config
$ sudo service ssh restart
```

Configured ufw to allow specified ports only(2200, 123, 80)

### Create PostgreSQL user and database

```bash
$ sudo -u postgres createuser -P catalog
$ sudo -u postgres createdb -O catalog catalog
```

### Clone Item-Catalog app

1. Install Git: `sudo apt-get install git`
2. `cd /var/www`
3. Create a new directory `sudo mkdir CaApp`
4. `cd CaApp`
5. Clone the repo: `git clone https://github.com/huylllooo/Item-Catalog.git CaApp`
6. `cd CaApp`
7. Rename `project.py` to `__init__.py`: `sudo mv project.py __init__.py`
8. In `db0.py` and `__init__.py` change database engine: `engine = create_engine('postgresql://catalog:catalog@localhost/catalog')`
9. Install pip: `sudo apt-get install python-pip`
10. Use pip to install dependencies:
```bash
$ sudo apt-get install python-psycopg2 python-flask
$ sudo apt-get install python-sqlalchemy python-pip
$ sudo pip install oauth2client
$ sudo pip install requests
$ sudo pip install httplib2
```
11. Create database schema: `sudo python db0.py`

### Configure Apache2
`sudo nano /etc/apache2/sites-available/CaApp.conf`

```bash
	```
	<VirtualHost *:80>
		ServerName 52.197.2.209
		ServerAdmin nmhuy14@gmail.com
		WSGIScriptAlias / /var/www/CaApp/caapp.wsgi
		<Directory /var/www/CaApp/CaApp/>
			Order allow,deny
			Allow from all
		</Directory>
		Alias /static /var/www/CaApp/CaApp/static
		<Directory /var/www/CaApp/CaApp/static/>
			Order allow,deny
			Allow from all
		</Directory>
		ErrorLog ${APACHE_LOG_DIR}/error.log
		LogLevel warn
		CustomLog ${APACHE_LOG_DIR}/access.log combined
	</VirtualHost>
	```
```

### Configure caapp.wsgi file
Create file
	```
	cd /var/www/CaApp
	sudo nano caapp.wsgi
	```
Add the following lines
	```
	#!/usr/bin/python
	import sys
	import logging
	logging.basicConfig(stream=sys.stderr)
	sys.path.insert(0,"/var/www/CaApp/")

	from CaApp import app as application
	application.secret_key = 'Add your secret key'
	```

### Restart Apache
`sudo service apache2 restart`

## References
1) https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps