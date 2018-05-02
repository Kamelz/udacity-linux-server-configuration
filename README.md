# Linux Server Configuration
- IP http://18.216.248.66/
- port 2200

- First we update all the server packages
 ```bash
 sudo apt-get update
 ```
## Security
 #### change ssh port from 22 to 2200
 - ```sudo nano /etc/ssh/sshd_config ``` 
 - locate the port and change it to 2200 and don't forget to remove the `#`
 - ``` sudo /etc/init.d/sshd restart ```
 
 #### Configure the Uncomplicated Firewall (UFW) 
 ```bash
sudo ufw allow 2200/tcp
sudo ufw allow 80/tcp
sudo ufw allow 123/udp
sudo ufw enable 
 ```
 #### Configure the local timezone to UTC
 - there is 2 ways to do this:
  - `sudo dpkg-reconfigure tzdata` and choose UTC from the list
  - `sudo rm -f /etc/localtime` then `sudo ln -s /usr/share/zoneinfo/UTC /etc/localtime`
## User Management
- add new user grader
- `sudo adduser grader`
- `sudo nano /etc/sudoers.d/grader` and paste  `grader ALL=(ALL) NOPASSWD:ALL`

 #### ssh login
 -  move downloaded key to ~/.ssh/
 - `chmod 600 ~/.ssh/id_rsa`
 - `ssh -i ~/.ssh/id_rsa grader@18.216.248.66 -p 2200
 
## Application Functionality
#### Install apache2 
 - `sudo apt-get install apache2`
#### Install mod_wsgi and python tools
- `sudo apt-get install python-setuptools libapache2-mod-wsgi`
- `sudo apt-get install python-pip`
#### Install and configure postgres
- `sudo apt-get install postgresql`
- `sudo nano /etc/postgresql/9.5/main/pg_hba.conf`
- make sure that remote connection is not allowed check this [link](https://www.digitalocean.com/community/tutorials/how-to-secure-postgresql-on-an-ubuntu-vps) for more information.
- login as postgres user `sudo su - postgres`
- connect to postgres database `psql`
- create user catalog `CREATE DATABASE catalog;`
- give `catalog` password ` ALTER ROLE catalog WITH PASSWORD '123';`
- grant `catalog` permissions to `postgres` database ` GRANT ALL PRIVILEGES ON DATABASE catalog TO catalog;`
- exit
#### Install GIT
- `sudo apt-get install git`
#### clone and configure catalog app
- `cd /var/www/`
- `mkdir catalogApp`
- `git clone https://github.com/Kamelz/Catalog-App.git catalogApp` and follow installation guide
- `cd catalogApp`
- `mv project.py __init__.py`
- `cd ..`
- `sudo pip install virtualenv`
- `sudo virtualenv venv`
- `source venv/bin/activate`
- `sudo chmod -R 777 venv`
- `pip install Flask`
- `pip install psycopg2-binary`
- `pip install oauth2client`
- `pip install requests`
- ` sudo nano /etc/apache2/sites-available/000-default.conf` and paste the following
```
<VirtualHost *:80>
    ServerName http://18.216.248.66/
    WSGIDaemonProcess catalogApp python-path=/var/www/catalogApp:/var/www/catalogApp/venv/lib/python2.7/site-packages
    WSGIProcessGroup catalogApp
    WSGIScriptAlias / /var/www/catalogApp/flask.wsgi
    <Directory /var/www/catalogApp/catalogApp/>
        Order allow,deny
        Allow from all
    </Directory>
    Alias /static /var/www/catalogApp/catalogApp/static
    <Directory /var/www/catalogApp/catalogApp/static/>
        Order allow,deny
        Allow from all
    </Directory>
    ErrorLog /var/log/web/error.log
    LogLevel warn
    CustomLog /var/log/web/access.log combined
</VirtualHost>
```
