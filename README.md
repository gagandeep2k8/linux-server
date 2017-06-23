# Project: Linux Server Configuration
By Gagan

*	IP address:  13.126.131.130
*	Accessible SSH port: 2200
*	Application URL: http://13.126.131.130/

# 1) Update all currently installed packages
	1. sudo apt-get update
	2. sudo apt-get upgrade

# 2)Create a new user named grader and give grader sudo permission
	1. sudo adduser grader
	2. vim /etc/sudoers
	3. touch /etc/sudoers.d/grader
	4. vim /etc/sudoers.d/grader,
	5. grader ALL=(ALL) NOPASSWD:ALL, save and quit

# 3)Change the SSH port from 22 to 2200 and Disable remote login of root user
	Logged in as root, edit the sshd_config file by "sudo vi /etc/ssh/sshd_config" and change below configurations.
	1. Port 2200
	2. PermitRootLogin no
	3. PasswordAuthentication no
	4. Restart the ssh service by "sudo service ssh restart"

# 4)Set ssh login using keys
	1. Generate keys on local machine using `ssh-keygen` 
	2. Run below steps:
		$ mkdir /home/grader/.ssh
		$ touch /home/grader/.shh/authorized_keys
		$ chown grader /home/grader/.ssh
		$ chown grader/home/grader/.ssh/authorized_keys
		$ chmod 700 /home/grader/.ssh
		$ chmod 600 /home/grader/.ssh/authorized_keys
	3. Reload SSH using service ssh restart
	4. Now you can use ssh to login with the new user grader by this command "ssh -i ~/.ssh/udacity_rsa grader@172.26.3.49 -p 2200"
	
# 5) Configure the Uncomplicated Firewall (UFW)
	1. sudo ufw allow 2200/tcp
	2. sudo ufw allow 80/tcp
	3. sudo ufw allow 123/udp
	4. sudo ufw enable 

# 6) Configure the local timezone to UTC
	 sudo dpkg-reconfigure tzdata
	
	
# 7) Install Apache
	 sudo apt-get install apache2
	
# 8) Install mod_wsgi
	1. Run sudo apt-get install libapache2-mod-wsgi python-dev
	2. Enable mod_wsgi with sudo a2enmod wsgi
	3. Start the web server with sudo service apache2 start
	
# 9) Clone the Catalog app from Github

	1. Install git by "sudo apt-get install git" and run below commands
		- cd /var/www
		- sudo mkdir catalog
		- Change owner of the newly created catalog folder sudo chown -R grader:grader catalog
		- cd /catalog
	2. Clone project from github git clone https://github.com/gagandeep2k8/item-catalog.git catalog
		Create a catalog.wsgi file, then add this inside:
		* import sys
		* import logging
		* logging.basicConfig(stream=sys.stderr)
		* sys.path.insert(0, "/var/www/catalog/")
		* from catalog import app as application
		* application.secret_key = 'super_secret_key'
		
	3. Rename application.py to init.py mv application.py __init__.py

# 10)Install Flask and other dependencies
	1. Install pip with sudo apt-get install python-pip
	2. Install Flask pip install Flask
	3. Install other project dependencies sudo pip install httplib2 oauth2client sqlalchemy psycopg2 sqlalchemy_utils

# 11)Update path of client_secrets.json file
	Run sudo vi  __init__.py
	Change client_secrets.json path to /var/www/catalog/catalog/client_secrets.json
	
# 12)Configure and enable a new virtual host
	1. Run "sudo nano /etc/apache2/sites-available/catalog.conf"
	2. Paste this code:
		<VirtualHost *:80>
			ServerName 13.126.131.130
			ServerAdmin gagandeep2k8@gmail.com
			WSGIScriptAlias / /var/www/catalog/catalog.wsgi
			<Directory /var/www/catalog/catalog/>
				Order allow,deny
				Allow from all
			</Directory>
			Alias /static /var/www/catalog/catalog/static
			<Directory /var/www/catalog/catalog/static/>
				Order allow,deny
				Allow from all
			</Directory>
			ErrorLog ${APACHE_LOG_DIR}/error.log
			LogLevel warn
			CustomLog ${APACHE_LOG_DIR}/access.log combined
		</VirtualHost>
	3. Enable the virtual host sudo a2ensite catalog	
	
# 13)Install and configure PostgreSQL
	1. sudo apt-get install libpq-dev python-dev
	2. sudo apt-get install postgresql postgresql-contrib
	3. sudo su - postgres
	4. psql
	5. CREATE USER catalog WITH PASSWORD 'password';
	6. ALTER USER catalog CREATEDB;
	7. CREATE DATABASE catalog WITH OWNER catalog;
	8. \c catalog
	9. REVOKE ALL ON SCHEMA public FROM public;
	10. GRANT ALL ON SCHEMA public TO catalog;
	11. \q
	12. exit
	13. Change create engine line in your __init__.py and database_setup.py to: engine = create_engine('postgresql://catalog:password@localhost/catalog')
	14. python /var/www/catalog/catalog/database_setup.py

# 14)Restart Apache
	sudo service apache2 restart
	
# 15)Visit site at http://13.126.131.130/


	



	


