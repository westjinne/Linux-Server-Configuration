# Linux-Server-Configuration
Udacity Nanodegree Project06

## Info
### Basic Info
- Local Machine OS: Mac OS  
- public IP address: 52.78.88.13
- DNS: http://52.78.88.13/  
- DNS: http://52-78-88-13.ap-northeast-2a.compute.amazonaws.com  
- Redirection URI: http://52-78-88-13.ap-northeast-2a.compute.amazonaws.com/oauth2callback  

### Programs
- Apache2  
- Python 2.7  
- Postgresql  
- psql  
- Flask  



## Get a server
### 1. Start a new Ubuntu Linux server instance on Amazon Lightsail. 
Login into Amazon Lightsail.  
Click <code>Create instance</code>.  
Select <code>OS Only</code> and <code>Ubuntu</code>.  

### 2. Follow the instructions provided to SSH into your server
Go to the main page, and download the <code>SSH keys</code>  
Open the key by text editing tool, copy the contents.   
Use terminal: <code>~/.ssh</code>  
Use terminal: <code>touch lightsail_key.rsa</code>  
Use terminal: <code>sudo nano lightsail_key.rsa</code>  
And paste it.  
Use terminal: <code>Command</code> + X and press <code>Y</code>, <code>Enter</code>  
Use terminal: <code>chmod 600 ~/.ssh/lightsail_key.rsa</code>  

To connect with instance, 
Use terminal: <code>ssh -i ~/.ssh/lightsail_key.rsa ubuntu@52.78.88.13</code>

## Secure the server
### 3. Update all currently installed packages  
Use terminal: <code>sudo apt-get update</code>  
Use terminal: <code>sudo apt-get upgrade</code>  

### 4. change the SSH port from 22 to 2200. 
Edit the <code>/etc/ssh/sshd_config</code> file.  
Use terminal: <code>sudo nano /etc/ssh/sshd_config</code>   
change <code>Port 22</code> to <code>Port 2200</code>  
Use terminal: <code>Command</code> + X and press <code>Y</code>, <code>Enter</code>  
Reatart the SSH  
Use terminal: <code>sudo service ssh restart</code>  

### 5. Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH(port 2200), HTTP(port 80) and NTP(port 123). 
Use terminal: <code>sudo ufw status</code>  
Use terminal: <code>sudo ufw default deny incoming</code>  
Use terminal: <code>sudo ufw default allow outgoing</code>  
Use terminal: <code>sudo ufw allow 2200/tcp</code>  
Use terminal: <code>sudo ufw allow www</code>  
Use terminal: <code>sudo ufw allow 123/udp</code>  
Use terminal: <code>sudo ufw deny 22</code>  
Use terminal: <code>sudo ufw enable</code>, <code>y</code>

Go to Amazon Lightsail, change Firewall Informations. 
1. Deny SSH default port.  
2. Add Custom(application)-UDP(protocol)-123(port range)  
3. Add Custom(application)-TCP(protocol)-2200(port range)  

## Give grader access
### 6. Create a new user account named <code> grader</code>.
User terminal: <code>sudo adduser grader</code>  
And type the pwd to setup the grader's information.  

### 7. Give  <code>grader</code> the permission to  <code>sudo</code>.
Use terminal: <code>sudo visudo</code>  
And insert <code>grader  ALL=(ALL:ALL) ALL</code> below the <code>root  ALL=(ALL:ALL) ALL</code>  
Use terminal: <code>Command</code> + X and press <code>Y</code>, <code>Enter</code>  

### 8. Create an SSH key pair for <code>grader</code> using the <code>ssh-keygen</code> tool.
Use Local terminal: <code>ssh-keygen</code>
Make a file that will save the key.
Use Local terminal: <code>cd ~/.ssh</code>  
Use Local terminal: <code>touch grader_key</code>  
Enter pwd twice for grader_key and grader_key.pub  
Use Ubuntu terminal: <code>cat ~/.ssh/grader_key.pub</code> and copy the contents.  
Login to the grader.  
Use grader terminal: <code>mkdir ~/.ssh</code>  
Use grader terminal: <code>sudo nano ~/.ssh/authorized_keys</code> and paste the contents.
Use grader terminal: <code>Command</code> + X and press <code>Y</code>, <code>Enter</code>  
Use grader terminal: <code>chmod 700.ssh</code> and <code>chmod 644 .ssh/authorized_keys</code>  
Use grader terminal: <code>sudo service ssh restart</code> for setup  

Use terminal: <code>ssh -i ~/.ssh/grader_key -p 2200 grader@52.79.146.188</code>

## Prepare to deploy a project
### 9. Configure the local timezone to UTC.
Use terminal: <code>sudo dpkg-reconfigure tzdata</code>  
And select the reigion.  

### 10. Install and configure Apache to serve a Python mod_wsgi application
If python is not installed, 
Use terminal: <code>sudo apt-get install python</code>  
Use terminal: <code>sudo apt-get install apache2</code>  
Use terminal: <code>sudo apt-get install libapache2-mod-wsgi</code>  
Use terminal: <code>sudo a2enmod wsgi</code>  

### 11. Install and configure PostgreSQL.
Use terminal: <code>sudo apt-get install postgresql</code>  
Use terminal: <code>sudo su - postgres</code>  

### 12. Install <code>git</code>.
Use terminal: <code>sudo apt-get install git</code>  

## Deploy the Item Catalog project
### 13. Clone and setup Item Catalog project from the Github repo.
Use grader terminal: <code>mkdir /var/www/catalog</code>  
Use grader terminal: <code>sudo git clone https://github.com/westjinne/Build-an-Item-Catalog.git</code>  
Move to upper directory.  
Use grader terminal: <code>cd .. </code>  
Change owner.
Use grader terminal: <code>sudo chown -R grader:grader catalog/</code>  
Move to catalog directory
Use grader terminal: <code>cd /var/www/catalog/catalog</code>
NO NEED FOR WSGI : Rename application.py: <code>my application.py __init__.py</code>  

### 14. Set it up in the server so that it functions correctly when visiting the server's IP address in a browser.
Install pip.  
Use grader terminal: <code>sudo apt-get install python-pip</code>  
Use grader terminal: <code>sudo apt-get install python-virtualenv</code>  
Use grader terminal: <code>cd /var/www/catalog/catalog</code>  
Use grader terminal: <code>sudo virtualenv -p python venv</code>  
Use grader terminal: <code>sudo chown -R grader:grader venv</code>  
Use grader terminal: <code>source ./venv/bin/activate</code>  

After go in to the venv, install all the things.  
Use venv terminal: <code>pip install httplib2</code>  
Use venv terminal: <code>pip install requests</code>  
Use venv terminal: <code>pip install --upgrade oauth2client</code>  
Use venv terminal: <code>pip install sqlalchemy</code>  
Use venv terminal: <code>pip install flask</code>  
Use venv terminal: <code>sudo apt-get install libpq-dev</code>  
Use venv terminal: <code>pip install psycopg2</code>
Use venv terminal: <code>deactivate</code>  

And then, run <code>__init__.py</code>
Use grader terminal: <code>python __init__.py</code>

❗️I thought there might be something when I go to 127.0.0.1, but it doesn't work. I'm a bit puzzled.  

Create <code>catalog.conf</code>file.  
Use grader terminal: <code>sudo nano /etc/apache2/sites-available/catalog.conf</code>
Set ServerName and WSGIScriptAlias
<pre><code>
ServerName 52.79.167.127
WSGIScriptAlias / /var/www/catalog/catalog.wsgi
WSGIDaemonProcess catalog user=www-data group=www-data

<Directory /var/www/catalog/catalog>
WSGIProcessGroup catalog
WSGIApplicationGroup %{GLOBAL}
Order deny,allow
Allow from all
</Directory>
</code></pre>  

And add <code>WSGIPythonPath</code> in <code>/etc/apache2/mods-enabled/wsgi.conf</code>  
<code>WSGIPythonPath /var/www/catalog/catalog/venv/lib/python2.7/site-packages</code>  

Use grader terminal: <code>sudo a2ensite catalog</code>  
Use grader terminal: <code>sudo service apache2 reload</code>  

And set up <code>catalog.wsgi</code> to make flask application work.  
Use grader terminal: <code>/var/www/catalog/catalog.wsgi</code>  
<pre><code>
activate_this = '/var/www/catalog/catalog/venv/bin/activate_this.py'
with open(activate_this) as file_:
    exec(file_.read(), dict(__file__=activate_this))

import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.append("/var/www/catalog/catalog/")

from application import app as application

application.secret_key = "..."
</code></pre>

And <code>sudo service apache2 restart</code> to restart apache.  
