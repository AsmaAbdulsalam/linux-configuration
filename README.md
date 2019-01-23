# Project Title
Linux Server Configuration- Udacity - final project.
This project is for configuring Linux server using Flask application using Ubuntu and Apache with Amazon Lightsail. .

# IP & Hostname
Host Name: ec2-3-121-222-167.eu-central-1.compute.amazonaws.com
IP Address: 3.121.222.167

# Set Up Amazon Lightsail
1. Go to the Amazon Lightsail website
2. Sign up choosing professional account, enter payment info. Complete registration.
3. click "Create instance".
4. Select OS Only and Ubuntu 16.04.
5. Click "Create" then wait to start up.
6. Click on instance once running then click on "Account Page" at the bottom so you can download your private SSH key.
7. Go to "SSH keys" tab then "Download", the file type is .pem.
8. Go to "Networking" tab. We need to set up port 2200 and 123. By default the firewall is set to only allow connects from port 22 and port 80.
9. Click on "+Add another". TCP for protocol. Port number 123 and 2200. Then click "Save".

# Linux Configuration
1. Rename the downloaded file .pem key to "udacity.pem" or any name but try not to forget it. Then move it to .ssh file.
2. If you can't find the ".ssh" file try to open your terminal as Administrator. Type "cd .ssh". now you will find it.
3. From here we will log into the server as the user ubuntu. From the terminal type "ssh -i ~/.ssh/udacity.pem ubuntu@00.000.000.00 The zeros your public IP Address of your instance.
5. Once your logged in successfully you will see the command line change to root@[ip-your-private-ip]:$  
6. Type "sudo su -" to switch to the root user.
7. Now we will create a user called grader as one of the given requirements. By typing "sudo adduser grader". It will ask for tow passwords and then a few other fields which you can leave blank.
8. Type "sudo nano /etc/sudoers.d/grader". This will create a file to give the user grader superuser privileges for grader.
9. Once the nano opens. Type only "ALL=(ALL:ALL)ALL". To save the file hit Ctrl-X on your keyboard, type 'Y' to save then Enter , Enter.
10. One of the first things you should always do when configuring a Linux server is updating it's package list, upgrading the current packages, and install new updates with these three commands:
 "sudo apt-get update"
 "sudo apt-get upgrade"
 "sudo apt-get dist-upgrade"
11. Type "sudo apt-get install finger" to install a useful tool called Finger. This tool will allow us to see the users on this server.
12. Now we need to create an SSH Key for our new user grader. In a new terminal type "ssh-keygen -f ~/.ssh/udacity.rsa".
13. Then type "cat ~/.ssh/udacity.rsa.pub". Copy the key from the terminal.
14. Back in the server terminal type "cd /home/grader" to locate the folder for the user grader.
15. Type "mkdir .ssh" to create a directory called .ssh
16. Type "touch .ssh/authorized_keys" to create a file to store the public key.
17. Type "nano .ssh/authorized_keys" to edit that file.
18. Paste the public key you coped.
19. change the permissions of the file and its folder by
	"sudo chmod 700 /home/grader/.ssh"
	"sudo chmod 644 /home/grader/.ssh/authorized_keys"
20. Type "sudo chown -R grader:grader /home/grader/.ssh" to change the owner of the .ssh directory from root to grader
21. Type "sudo service ssh restart" and "sudo service ssh reload" which is the last thing we need to do for the SSH configuration is restart and reload its service.
22. You can Disconnect from the server or Just open new terminal
23. Type "ssh -i ~/.ssh/udacity.rsa grader@00.000.000.00" to login with the grader account.
24. You should now be logged into your server via SSH
25. From the ssh configuration file lets enforce key authorization by editing sshd_config file. Type "sudo nano /etc/ssh/sshd_config". Find the line that says PasswordAuthentication and change it to no. Also find the line that says Port 22 and change it to Port 2200. Lastly change PermitRootLogin to no.
26. Restart ssh again b typing "sudo service ssh restart".
27. Disconnect or simply open new terminal and type "ssh -i ~/.ssh/udacity.rsa grader@0.000.000.000 -p 2200". You should be connected.
28. Now we need to configure the firewall using these commands:
	"sudo ufw allow 2200/tcp"
	"sudo ufw allow 80/tcp"
	"sudo ufw allow 123/udp"
	"sudo ufw enable"
29. Type "sudo ufw status" which should show all of the allowed ports with the firewall configuration.
30. Done with Linux configuration, now onto the app deployment.

# Application Deployment
1. Installing the required software
	"sudo apt-get install apache2"
	"sudo apt-get install libapache2-mod-wsgi python-dev"
	"sudo apt-get install git"
2. Type "sudo a2enmod wsgi" to enable mod_wsgi.
3. Type "sudo service apache2 restart" to restart Apache
4. We now have to create a directory for our catalog application and make the user grader the owner.
	"cd /var/www"
	"sudo mkdir catalog"
	"sudo chown -R grader:grader catalog"
	"cd catalog"
	We will have:
	catalog.wsgi file var/www/catalog/catalog.wsgi,
	venv virtual environment directory /var/www/catalog/venv,
	catalog another directory which we'll sit inside of it our application /var/www/catalog/catalog.
5. Upload your project to a github repository if its not their
		1. Open your account in github.
		2. Click "New" from repository page or "New repository" from Home page.
		3. Enter the name of your project in "Repository name".
		4. Then Create.
		5. Open a terminal where your project is saved in or open a terminal and move using "cd" command to reach the file palce.
			Type:
			"git init"
			"git add ."
			"git commit -m "first commit"
			" git remote add origin https://github.com/........./.......git"
			"git push -u origin master"
		6. You can see from repository page that there is a new repository. Copy the link of the repository.
6. Type "git clone [repository url] catalog" to clone your Application repository in repository url Paste your own repository url
7. Type "udo nano catalog.wsgi" to create the .wsgi file. Make sure your secret key matches with your project secret key

		"import sys
		import logging
		logging.basicConfig(stream=sys.stderr)
		sys.path.insert(0, "/var/www/catalog/")
		from catalog import app as application
		application.secret_key = 'super_secret_key'"

8. Type "mv name.py __init__.py" to rename your .py file make sure to write your own file name instead of name. If you can't find the file try cd catalog the the command. Then "cd .."
9. lets create our virtual environment, make sure you are in /var/www/catalog.
		"sudo apt-get install pip"
		"sudo apt-get install python-pip python-dev build-essential"
		"sudo pip install --upgrade pip"
		"sudo pip install virtualenv"
		"sudo virtualenv venv"
		"source venv/bin/activate"
		"sudo chmod -R 777 venv"

	if this massages appeared to you	"After this operation, 125 MB of additional disk space will be used.
		Do you want to continue? [Y/n]" press "Y" from your keyboard and Enter.
10. Your command line should look like This "(venv) grader@ip-172-26-5-177:/var/www/catalog/catalog$ "

11. While our virtual environment is activated we need to install all packages required for our Flask application. Here are some defaults but you may have more to install.
		"sudo apt-get install python-pip"
		"pip install flask"
		"pip install httplib2 oauth2client sqlalchemy psycopg2"

12. Now we need to make some changes in the __init__.py file so we can run our application properly.
13. Anywhere in the file where Python tries to open client_secrets.json or fb_client_secrets.json must be changed to its complete path ex: /var/www/catalog/catalog/client_secrets.json
14. Type " sudo nano  __init__.py" to edit the file. Type "ls" to make sure you are in the right folder, if you can't find your file try to type "cd catalog"
15. To find your servers hostname go to:https://whatismyipaddress.com/ip-hostname and past your IP address. You will need it in the next step.
16. Type "sudo a2ensite catalog.conf" to configure and enable our virtual host to run the site.
	Paste in the following:
			"<VirtualHost *:80>
			    ServerName Public IP
			    ServerAlias Hostname
			    ServerAdmin admin@35.167.27.204
			    WSGIDaemonProcess catalog python-path=/var/www/catalog:/var/www/catalog/venv/lib/python2.7/site-packages
			    WSGIProcessGroup catalog
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
			</VirtualHost>"
**
change Public IP and Hostname with your public IP and Hostname.
Save and quite by pressing Ctrl+X and y then Enter.

17. Type "sudo a2ensite catalog.conf" to enable to virtual host.
18. Type "a2dissite 000-default.conf" to DISABLE the default host.
	  otherwise your site will not load with the hostname.
19. Setting up the database:
		"sudo apt-get install libpq-dev python-dev"
		"sudo apt-get install postgresql postgresql-contrib"
		"sudo su - postgres"
		"psql"
	The command line should look like this "postgres=#"
20. Create a database user and password:
		"CREATE USER catalog WITH PASSWORD 'password';" replace password with your password
		"ALTER USER catalog CREATEDB;"
		"CREATE DATABASE catalog with OWNER catalog;"
		"\c catalog"
		"REVOKE ALL ON SCHEMA public FROM public;"
		"GRANT ALL ON SCHEMA public TO catalog;"
		"\q"
		"exit"
	The command line should back to grader.
21. Change the database engine from sqlite://catalog.db to postgresql://username:password@localhost/catalog using nano to edit your __init__.py, database_setup.py, and createitems.py files.
	"sudo nano __init__.py" two palce to change.
	"sudo nano database_setup"
	"sudo nano lotsofmenus.py"
22. Type "sudo service apache2 restart" to restart your apache server.
23. Now your IP address and hostname should both load your application.

## References:
*https://github.com/mulligan121/Udacity-Linux-Configuration/blob/master/README.md#linux-configuration
*https://github.com/Heba-ahmad/Linux-Server-Configuration

## Authors
***Asma Abdullah Abdulsalam** - *Student in KAU and Udacity*
