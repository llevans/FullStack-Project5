README
======

Catalog CRUD project - AWS-EC2, WSGI
------------------------------------

This application is a Python web module used to manage a catalog of classical music composers. It is running on an Apache 2 instance that serves Python scripts via WSGI. The Composer's catalog is maintained in PostgreSQL persistent storage.

The application has been deployed to the AWS EC2 cloud on server 54.200.65.136.
A Udacity user named 'grader' has been setup on the AWS EC2 server, SSH public key information is include below.

Steps to login to the remote EC2 server:
  * Copy the SSH private key for 'grader' into ~/.ssh/udacity_grader
  * SSH to the development server using command : 
    * `ssh -i ~/.ssh/udacity_grader grader@54.200.65.136  -p 2200`

The Python Catalog application is available at url http://ec2-54-200-65-136.us-west-2.compute.amazonaws.com/

The appplication authenticates using Google+ OAuth services.
The user can view Composers listed in the Catalog, see details such as
name, description and era. Once logged in, the user can edit, add and
delete Composers from the catalog.

AWS server configuration:
=========================
1. Update all Ubuntu packages:
   1. sudo apt-get update
   2. sudo apt-get upgrade
2. Add Udacity users and give sudo privileges:
   1. sudo adduser grader
   2. sudo adduser student
   3. cd /etc/sudoers.d/
   4. Add line "**** ALL=(ALL) NOPASSWD:ALL" to individual new sudoer files
   5. chmod 440 grader
   6. chmod 440 student
3. SSH keys were generated for the Udacity grader:
   1. ssh-keygen -f ~/.ssh/udacity_grader
   2. Copy public key from ~/.ssh/udacity_grader.pub into /home/grader/authorized_keys on ec2 server
4. Set default time zone to UTC:
   1. dpkg-reconfigure tzdat
   2. more /etc/timezone
   3. hwclock
5. Set Ubuntu uncomplicated firewall:
   1. ufw status
   2. ufw default deny incoming
   3. ufw default allow outgoing
   4. ufw allow 2200/tcp
   5. ufw allow www
   6. ufw allow ntp
   7. ufw status
6. Set SSH port tp 2200:
   1. netstat -an
   2. lsof -i TCP:22
   3. lsof -i TCP:2200
   4. more /etc/services
   5. vi /etc/services
   6. ps aux | grep sshd
   7. lsof -i | grep sshd
   8. service ssh restart
   9. ps aux | grep sshd
   10. lsof -i | grep sshd
7. Install Apache2 and PostgreSQL:
   1. apt-get install apache2
   2. apt-get install libapache2-mod-wsgi
   3. apt-get install postgresql
   4. apt-get install git


Prep Python and PostgreSQL
==========================
1. Install necessary Python packages:
   1. apt-get install python-sqlalchemy
   2. apt-get install python-psycopg2
   3. apt-get install python-httplib2
   4. apt-get install python-flask
   5. apt-get install python-oauth2client
2. Run python scripts to setup and populate the database:
   1. python db_setup.py
   2. python db_populate.py

Prep Catalog App in Apache WSGI
================================
1. Enable WSGI for Apache in /etc/apache2/sites-enabled/000-default.conf:
   1. Define WSGI to run as "student": 
       1. `WSGIDaemonProcess catalog python-path=/var/www/html user=student group=student`
	2. Add WSGI Virtual Host directive:
	   1. `WSGIScriptAlias / /var/www/html/catalog.wsgi
        <Directory /var/www/html>
            WSGIProcessGroup catalog
            WSGIApplicationGroup %{GLOBAL}
            Order deny,allow
            Allow from all
        </Directory>`
       
2. Restart Apache 2 server once WSGI is configured:
   1. apache2ctl restart

3. Hide ".git|.svn" subdirectories:
    1. Enable Apache Rewrite :  
       1. `sudo a2enmod rewrite`
    2. Add to VirutalHost (000-default.conf): 
	   1. `RewriteEngine on`
	   2. `RedirectMatch 404 /\.(svn|git)(/|$)`

4. Adapt project.py to run as WSGI script:
    1. Create catalog.wsgi to import Flask  "project" module
    2. Prep Flask 'App' in `if __name__ == 'project'` project.py main section
    3. Fully qualify the filesystem location of `client_secrets.json`


Allow AWS server Google+ OAuth:
===============================
    1. Add AWS-EC2 URL as allowed URL for ComposersCatalogApp at
    https://console.developers.google.com/project

Resources
=========
   1. I utilized these sites to troubleshoot my Apache2/WSGI web server configuration:
      1. http://flask.pocoo.org/docs/0.10/deploying/mod_wsgi/
      2. http://stackoverflow.com/questions/8967216/flask-with-a-webserver-breaks-all-sessions
      3. http://stackoverflow.com/questions/10861260/how-to-create-user-for-a-db-in-postgresql

Grader SSH private key (udacity_grader):
========================================
-----BEGIN RSA PRIVATE KEY-----
MIIEogIBAAKCAQEAq/xO2rXclKUcKeoEIDdq1t4mQN/nVLftJbayqvsCkjfuRqmr
vS8z//B1xrXQ7V6hXdiR2W6Q+S5+cYZQYYjd2rSjGWgeBKHbj+dTqbw66fn4ISTj
XirUO5jWktV3fITXVV2LlabniQ+60fXsfOQThf4PygVjEDox2WwKGBQE6eJDcKjq
4ERXNk66PIsqHfIyvpGg0KAooc/5xVTGbo9a8bUQClmyAhSNbafR5LyQKP4D7ul8
eOhPcKMRYJtuRWF7tuoGr7d7Gdr3FrVYxD+tv4Kw9L3lHZlQhKoG3rs+5L6BurCG
++jKv16YqxpNrLpkAxl30Q0Yli5WzXfwypi8YQIDAQABAoIBABDPms2aFTOaaARY
0YpsoE7cbBTPTgdj6xRSpWg9HOU5/lp930GgyY5s7LY9s2xgYZmQv8DnI9iXiNiD
7nt6K2VjXsXu/XAqehG45N6kfYW9X1muB2N/ADvLFr5Hi2Rqf7niaPB53gvxs6su
RK14hcgFK3ntyN8fMRw1iU8NNeW+hQDzHuktzFDjAzcxvatKd+a/2KlwjhjX1vTF
v6c7nx/nFpnRw7gB7hwXB062zXYMTI9E/C59MaupMjgkWyXiyPmE4plSl5yF7C0q
TcFPS9taHPFmt9H3qm0ZR5IV18/ZpaKuVS1rSKMqas+FlGIIo189x42hWjuOqylS
z1W8AXkCgYEA4ogWUehpUimwXmZhPZ1rhLA6msjUU07CRsOnUHYYDi2UVXG2BVOp
GCHOuxg3Ds2T9B+2eCMXS3VGsWly7TNuwu8xBQiEJAPKzKUasWciCEFJEGrjRFVG
ITQnUubjTz829+W1fcK3l/jiMd+VPvNx6nmRooN7YyRC1LwRs0ZagwsCgYEAwlu9
XO63O8VFt738+LB5w6rDbOwsZrtwLMmzrY8nXEpkuZnS2UnjV1LV/v3UtDxAy891
DpYuXJWpWPVgeuf9ei/KbOd+HNstT3xLfbz73xfXoI7Sqqp0GSULB0aDYScHmVMG
EkPNxPC5YqQuV7X3IoDuvrUyH7WAI9YtEUvXocMCgYBbiUYLnvBABRyBaLlOXnf9
zABCX3h8mUyjr5fCSZX61Kwlwqcci+u+Fpskuuo0jxmEAfSHoxZcpW0Fb2jDNI9D
McgLEp/ita2S9/xLRzNXRpoxih9/kz2dWSeyth2P/ilGDopB4Ray0B0GriPupgyY
BLu2gY6wRwzCTaSRXpJgdQKBgH+ojjqKOSQzMNW9HnmiQHg6o13Zylg36Tghy7jF
/E5sNriJxqoeFAr5R8HSq3Eu2kiEHRjVn+IEE1Vw5kwUMLOezFC3xGfjtmEs/NC7
3pvsJx84cDrU9qjFYfcHY6feYOleVPCORIuEqd+WgzHtPUQWd7offarCJkZjmPpx
XFLNAoGAa4YwZMlcGxCLq30IXKHRKVepOm2LIJpHzwvMEJhIB3H+TZoTe9q3wCPH
DLWgalTXvwqUTT8sqBjciZTY5UlAgpBbk5ErLep94lnELXcCK3l6yjQbWLIXa74R
4sDRFqLSsIPjSIKbtEUbfqlvYxTZI6vFyqG8nbZyLU7EYQ/mDtI=
-----END RSA PRIVATE KEY-----

Grader SSH public key (udacity_grader.pub):
===========================================
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCr/E7atdyUpRwp6gQgN2rW3iZA3+dUt+0ltrKq+wKSN+5Gqau9LzP/8HXGtdDtXqFd2JHZbpD5Ln5xhlBhiN3atKMZaB4EoduP51OpvDrp+fghJONeKtQ7mNaS1Xd8hNdVXYuVpueJD7rR9ex85BOF/g/KBWMQOjHZbAoYFATp4kNwqOrgRFc2Tro8iyod8jK+kaDQoCihz/nFVMZuj1rxtRAKWbICFI1tp9HkvJAo/gPu6Xx46E9woxFgm25FYXu26gavt3sZ2vcWtVjEP62/grD0veUdmVCEqgbeuz7kvoG6sIb76Mq/XpirGk2sumQDGXfRDRiWLlbNd/DKmLxh lynevans@C02MJ78FFD57

Catalog Application Description
===============================
Functions are available to manage the Composers' catalog:
  1. List all Composers
        - http://ec2-54-200-65-136.us-west-2.compute.amazonaws.com/
  2. List Composers in a specific era 
        - http://ec2-54-200-65-136.us-west-2.compute.amazonaws.com/era/Classical/composers
  3. View Composer's detail 
        - http://ec2-54-200-65-136.us-west-2.compute.amazonaws.com/era/Classical/Wolfgang%2520Amadeus%2520Mozart
  4. Add a new Composer
        - http://ec2-54-200-65-136.us-west-2.compute.amazonaws.com/era/composer/new
  5. Edit Composer's name, description and era
        - http://ec2-54-200-65-136.us-west-2.compute.amazonaws.com/era/Contemporary/Conrad%20Tao/edit
  6. Delete a Composer
        - http://ec2-54-200-65-136.us-west-2.compute.amazonaws.com/era/Contemporary/Conrad%20Tao/delete


Endpoints are availble to aquire the Composers' catalog:
  1. Atom Composers List 
        - http://ec2-54-200-65-136.us-west-2.compute.amazonaws.com/composers.atom
  2. Json Era Full List 
        - http://ec2-54-200-65-136.us-west-2.compute.amazonaws.com/era/list/JSON
  3. Json Composers per Era List 
        - http://ec2-54-200-65-136.us-west-2.compute.amazonaws.com/era/1/list/JSON
  4. Json Composer Detail
        - http://ec2-54-200-65-136.us-west-2.compute.amazonaws.com/era/1/composer/7/JSON

