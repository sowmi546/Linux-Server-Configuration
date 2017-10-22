## Linux Server Configuration Project

In this final project, a virtual linux machine is configured for Item Catalog Website

The website can be reached at http://13.58.254.25

### SSH Key details :

The private key is added in the notes (grader_sowmya.key is the file that has the private key).
Passphrase provided is ‘grader'
Grader user can get the access using the command below :
ssh -i grader_sowmya.key grader@13.58.254.25 -p 2200

### Changing the SSH port :

Changing ssh port from 22 to 2200 :
In ssh_config file under /etc/ssh path change the port from 22 to 2200
Save and quit using the command :wq!
Restart SSH using ssh service ssh restart


### Configure UFW

Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123)

```sudo ufw allow 2200/tcp
sudo ufw allow 80/tcp
sudo ufw allow 123/udp
sudo ufw enable
```

### Configuring a new virtual host :
create the configuration file as below

sudo vi /etc/apache2/sites-available/ItemCatalogProject.conf


The conf file contains the following content :
```
<VirtualHost *:80>
    ServerName 172.26.3.85
    WSGIDaemonProcess application user=grader group=grader threads=5
    WSGIScriptAlias / /var/www/ItemCatalogProject/application.wsgi

    <Directory /var/www/ItemCatalogProject/ItemCatalogProject/>
        Order deny,allow
        Allow from all
    </Directory>
</VirtualHost>
```


The virtual host is enabled by the command

sudo a2ensite ItemCatalogProject


Create the .wsgi file :
cd /var/www/ItemCatalogProject/

vi application.wsgi

It has the following content :
```import sys
sys.path.insert(0, '/var/www/ItemCatalogProject/ItemCatalogProject')
from application import app as application
```



Apache server can be restarted using the below command

```sudo service apache2 restart```

Update all the packages using the below command :

```sudo apt-get update```

Installing and confusing PostgreSQL :
PostgreSQL can be installed using the command :
```sudo apt-get install postgresql```

Install sqlite3 using
```sudo apt install sqlite3```


### Create a database and also user with the name catalog and give the password :

Steps :
Go to the corresponding folder :

cd /var/www/ItemCatalogProject/ItemCatalogProject
```
sudo -u postgres psql
postgres=# CREATE DATABASE catalog OWNER catalog;
CREATE DATABASE
postgres=# alter role catalog with password 'catalog';
ALTER ROLE
postgres=# \quit
```


### Installing git, cloning the project and setting up :

Install Git using the command

```sudo apt-get install git```

cd /var/www
clone the git repository for the CatalogItem project and set the tree structure as below :
(Run the command tree when in /var/www to see the structure )
```
└── ItemCatalogProject
    ├── application.wsgi
    └── ItemCatalogProject
        ├── application.py
        ├── application.pyc
        ├── catalog.db
        ├── client_secret.json
        ├── db_setup.py
        ├── db_setup.pyc
        ├── lotofcatitems.py
        ├── pg_config.sh
        ├── README.md
        ├── static
        │   ├── background.jpeg
        │   ├── loginstyle.css
        │   ├── styles.css
        │   └── udacity.png
        └── templates
            ├── categoriesrepeat.html
            ├── categorydetails.html
            ├── createuser.html
            ├── deletecatalogitem.html
            ├── editcatalogitem.html
            ├── header.html
            ├── itemdetails.html
            ├── login.html
            ├── main.html
            ├── mainpage.html
            ├── newcatalogitem.html
            └── publicmainpage.html

```

Make changes to db_setup.py, application.py and lotofcatitems.py and change to 

```engine =create_engine('postgresql://catalog:catalog@localhost/catalog')```

and client secrets path in application.py file when using gconnect.

Also make changes as needed in https://console.developers.google.com for the corresponding project under javascript origins

Add

http://13.58.254.25



Reference link from online :

https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps

