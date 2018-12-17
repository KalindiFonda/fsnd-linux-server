### README for the linux server project for the Udacity Full Stack Nanodegree project

Serves application based off of [this project](https://github.com/KalindiFonda/fsnd-items)

#### Info for project reviewer:

1. IP Address: 34.217.236.246 - Port: 2222
2. URL to application http://ec2-34-217-236-246.us-west-2.compute.amazonaws.com


### Instructions to set up server:

1. set up new instance on AWS Lightsail (linux - os only - ubuntu)
		https://classroom.udacity.com/nanodegrees/nd004/parts/ab002e9a-b26c-43a4-8460-dc4c4b11c379/modules/357367901175462/lessons/3573679011239847/concepts/c4cbd3f2-9adb-45d4-8eaf-b5fc89cc606e

2. get info about instance https://lightsail.aws.amazon.com/ls/webapp/home/instances?#
	ip: 34.217.236.246

3. get private key info:
	https://lightsail.aws.amazon.com/ls/webapp/account/keys

4. put private key into .ssh folder
	[home]/.ssh/[name_file].pem  - /Users/kalindi/.ssh/fsnd1.pem
	set privacy of key to only me:
		chmod 600 /Users/kalindi/.ssh/fsnd1.pem
		Thanks to: https://discussions.udacity.com/t/cannot-connect-to-lightsail-instance/619279

5. log into server
	ssh -i folder/witf/ssh/key  username@ip_address
	ssh -i /Users/kalindi/.ssh/fsnd1.pem  ubuntu@34.217.236.246


6. Update and upgrade all
	sudo apt-get update
	sudo apt-get upgrade
	man apt-get

	sudo apt-get autoremove

7. Make new user:
	install finger
		sudo apt-get install finger
	add user
		sudo adduser [name] (pass [password])
		sudo adduser grader (pass internet)
	check if new user grader exists
		finger grader

8. give new user sudo acces:
	list sudo users:
		sudo ls /etc/sudoers.d
	create copy of sudo file
		sudo cp /etc/sudoers.d/90-cloud-init-users /etc/sudoers.d/grader
	change the file inside the terminal (with nano) and replace ubuntu with name of new user- grader
		sudo nano /etc/sudoers.d/grader

9. generate key pairs (for grader user) in terminal of local computer
	open new terminal window
		ssh-keygen
	save in
		/Users/kalindi/.ssh/fileName
		(pass internet)
	get info on keys
		cat .ssh/[project_name].pub
		cat .ssh/linuxproject.pub


10. Back in user server terminal:
	(make dir where all keys should be stored, if it doesn't exist)
		mkdir .ssh
	create new file where keys will be stored
		touch .ssh/authorized_keys
	grader user terminal change file authorized keys (add keys from before)
		nano .ssh/authorized_keys
	get info on key:
		cat .ssh/authorized_keys

11.
	login into grader (http://www.brianlinkletter.com/
	how-to-set-up-a-new-userid-on-your-amazon-aws-server-instance/)
		sudo su grader

	add ssh key into grader:
		mkdir .ssh
		sudo chmod 700 .ssh
		sudo touch .ssh/authorized_keys
		sudo chmod 600 .ssh/authorized_keys
		sudo nano .ssh/authorized_keys
			(This file needs to have owner grader??
			chown grader:grader authorized_file
			https://www.thegeekstuff.com/2012/06/chown-examples/)

12. exit from the server:
exit
exit

13. login as grader
	ssh -i ~/.ssh/fsnd1.pem grader@34.217.236.246
	ssh -i /Users/kalindi/.ssh/fsnd1.pem grader@34.217.236.246

14. disable login with pass (forcing key based authentication)
 	password authentication change yes to no:
		sudo nano /etc/ssh/sshd_config


15. set up firewall
		sudo ufw status
		sudo ufw default deny incoming
		sudo ufw default allow outgoing
		sudo ufw status
	(still incative which is good, because otherwise we'd be locing ourselves out.)
		sudo ufw allow ssh
		sudo ufw allow 2222/tcp
		sudo ufw allow www
	now we can enable the firewall
		sudo ufw enable
		sudo ufw status



??. Change timezone - why this and does order matter?
	sudo dpkg-reconfigure tzdata


16. install apache
	sudo apt-get install libapache2-mod-wsgi



17. edit apache config
	sudo nano /etc/apache2/sites-available/000-default.conf
		sudo nano /etc/apache2/sites-enabled/000-default.conf
	by adding
		WSGIScriptAlias / /var/www/app/myapp.wsgi
	before the closing </VirtualHost>

	then restat:
		sudo apache2ctl restart
	(didn't work so I did [this](https://discussions.udacity.com/t/myapp-wsgi-doent-open/390787 https://stackoverflow.com/questions/20627327/invalid-command-wsgiscriptalias-perhaps-misspelled-or-defined-by-a-module-not))
		sudo a2enmod wsgi
		sudo service apache2 restart
18.
	Create myapp.wsgi file
		TODO - add image
		https://classroom.udacity.com/nanodegrees/nd004/parts/ab002e9a-b26c-43a4-8460-dc4c4b11c379/modules/357367901175461/lessons/4340119836/concepts/48018692630923
		help? https://discussions.udacity.com/t/not-sure-if-im-on-right-path-how-to-move-forward/625498



19. install git
		sudo apt-get install git
	add username and email
		git config --global user.name "KalindiFonda"
		git config --global user.email "kalindi.fonda@gmail.com"
	check user name
		git config --global user.name

20. install what is needed for project:
	pip
		sudo apt-get install python-pip
		(encountered a problem after upgrading pip so doing this helped:
			python -m pip install --user --upgrade pip==9.0.3
	Flask
	 	sudo pip install Flask
	 sqlalchemy
	 	sudo pip install sqlalchemy
	 postgresql
	 	sudo apt-get install postgresql
	 	sudo apt-get install postgresql postgresql-contrib
	 intall psycopg2
	 	sudo apt install python-psycopg2
	 install oauth2client
	 	pip install --upgrade oauth2client
	 install requests
	 	sudo pip install requests
	 install tree (for seeing where the folders are - ie tree /var/www/)
	 	sudo apt-get install tree
	 	sudo apt-get install tree


21. clone github library:
	first go into folder /var/www/
		git clone https://github.com/KalindiFonda/fsnd-items.git



23. Change /etc/apache2/sites-available/catalog.conf to something along the lines of:

```
<VirtualHost *:80>
    ServerName 34.217.236.246

    WSGIScriptAlias / /var/www/app/myapp.wsgi
    <Directory /var/www/app/todos/>
            Order allow, deny
            Allow from all
    </Directory>
    Alias /static /var/www/app/todos/static
    <Directory /var/www/app/todos/static/>
            Order allow,deny
            Allow from all
    </Directory>
    ErrorLog ${APACHE_LOG_DIR}/error.log
    LogLevel warn
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

24. change /var/www/app/myapp.wsgi to hold info on app - to point to app folder.

```
#!/usr/bin/python
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/var/www/app/todos/")

from project import app as application
application.secret_key = ‘super_secret_key’
```

25. check if all went well and restart server:

	sudo tail /var/log/apache2/error.log
	sudo apache2ctl restart
	sudo a2ensite catalog.conf

26. config database:
	in database_setup.py
	change this line
	`python engine = create_engine('postgresql://catalog:db-password@localhost/catalog')`
	repeat for application.py(main.py)

27. Some stuff in the orginal application are not in the same place/format so aka. move things around to make it work, because I don't fully understand everything

	sudo mv /var/www/html/myapp.wsgi /var/www/app/myapp.wsgi
	copy your main app.py file into the init.py file
	mv app.py __init__.py
	cp __init__.py project.py

28. why??
	Add catalog user sudo adduser catalog

29. create database
	login as postgres super usersudo su - postgres
	enter postgrespsql
	Create user catalog
	`CREATE USER catalog WITH PASSWORD 'db-password';`

	Change role of user catalog to creatDB
	`ALTER USER catalog CREATEDB;`
	Create new DB "catalog" with
	`CREATE DATABASE catalog WITH OWNER catalog;`

	Connect to database
	`\c catalog`

	Revoke all rights
	`REVOKE ALL ON SCHEMA public FROM public;`

	Give accessto only catalog role
	`GRANT ALL ON SCHEMA public TO catalog;`

	Quit postgres
	`\q`

	logout from postgres super user
	`exit`

30. Setup your database schema
	`python database_setup.py`
	I had problems importing psycopg2 so this stack overflow post helped me (lol lost post, TODO find post)

31. restart apache (anytime there are changes in the files)
	sudo service apache2 restart

32. grant usage rights
	grant usage on schema public to public;
	grant create on schema public to public;

33. Make sure all secrets are in place.

34. cd into directory with python files, run them
	sudo python database_setup.py
	sudo python populate_db.py
	sudo python project.py

35. Access website through ip address!!!!
	Read this if logging in issues: https://stackoverflow.com/a/46986030/10792297

