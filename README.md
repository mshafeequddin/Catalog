# Project - Linux Server Configuration

##### Introduction
This project includes the modification of the previously done catalog application to use Postgresql database and is hosted  on the ubuntu instance using Amazon's lightsail.

##### Technologies used
* Ubuntu
* Postgresql
* Amazon Lightsail
* Apache Web Server
* Flask
* Sqlalchemy
* Python3 etc.

#### Instructions to set up something similar.
#### Required:
* Amazon lightsail account
    *  Need an amazon account where it will ask for the address, phone and credit card details (only be 
     charged if we exceed the pre-defined limitations).
###### Steps:
* Create an ubuntu instance i.e. plain OS option without any pre-installed applications.
* Check updates for packages:  `sudo apt-get update`
* Upgrade the packages using `sudo apt-get upgrade`
* Secure the server by setting up firewalls:
    *  ufw is not active by default, check status using ` sudo ufw status`
    *  Now create the rules for firewall, initally block everything coming in i.e. run `sudo ufw default deny incoming`
    *  Allow outgoing using `sudo ufw default allow outgoing`
    *  Now configure the ports using commands `sudo ufw allow ssh` , `sudo ufw allow 2200/tcp`, etc
    *  Finally enable the firewall using `sudo ufw enable`.
* Create an account for 'grader'
    * Add new user using `sudo adduser grader` , it will prompt for input, enter details you want to.
    * Now copy the existing file in /etc/sudoers.d/90-cloud-init-users to /etc/sudoers.d/grader and change the username from       ubuntu to grader.
* Key Based Authentication
    * I have downloaded and used the third party tool putty as suggested on amazon ligtsail's website to create the               key-pair.
    * Now we need to upload the public key on the ubuntu instance and save private at a secret location.
        * mkdir /home/grader/.ssh and touch /home/grader/.ssh/authorized_keys
        * Use the cat command to read the key, copy and paste it in the above authorized_keys file.
        * Now run `chmod 700 .ssh` and `chmod 644 .ssh/authorized_keys` respectively to change the access on directory and            file level.
        * Everything is set now, finally use the command to login without password `ssh grader@18.220.62.60 -p 2200 -i                ~/.ssh/key` Note.~/.ssh is the          key location in this instance could be different as well.
        * Now to secure this authentication process to password less, edit the file `sudo vi /etc/ssh/sshd_config` to change           the password authentication option to no.
        * Now restart the ssh service.. `sudo service ssh restart`
* Tip: At this point when the initial ubuntu setup is ready with user and all, I have used the 'AWS Lightsails option to create the **Snapshot** which could be useful if we run into any issues as we progress. I have used the iterative approach and after each step in the project used the snapshot option.

#### Project related dependencies and tools setup
*  I have spent decent amount of time atleast 3-4 times playing around with sample apps, catalog with sqllite, etc using all the possible research online and finally was able to host the **Catalog with postgresql**
*  Run into issues python version issues as well. But the ubuntu snapshot feature helped me continue after every fail.
*  Installations and Others Required steps:
    *  Apache : `sudo apt-get install apache2`
    *  Postgresql: `sudo apt-get install postgresql postgresql-contrib` 
        * After installation, we need to set up the catalog user.Follow the below steps
        * `sudo su - postgres` to get into postgres
        * `psql` and hit enter.
        * CREATE DATABASE catalogdb.
        * `CREATE USER catalog WITH PASSWORD 'password'` to create the role and then grant select privileges to this user             for catalogdb.
        * Since my application is built in python3, to install wsgi I had to use `sudo apt-get install             libapache2-mod-wsgi-py3` 
        * wsgi is enabled by default.
    * I have created the directory '/var/www/Catalog' and then get all the code from git.
    * The directory structure should be as follows
    * `__init__.py` in the root directory which is the first thing to run.
    * `models.py` and `client_secret_catalog.json` resides in the same directory as well
    * See the directory structure below:
    ```
        Catalog
        │   __init__.py   
        │   client_secret_catalog.json
        │   models.py
        └─── static/css/.css files reside here
        └─── templates/*.html files reside here
    ``` 
    * I have had numerous issues setting up the environment so finally I was able to accomplish it using the virtualenv
     option which gives us the ability to setup the project environment at the local level.
    * `sudo apt-get install python3-pip` for python3
    * `sudo pip3 install virtualenv` to setup virtualenv withiin project
    * `sudo virtualenv venv` to rename it.
    * source /var/www/Catalog/Catalog/venv/bin/activate to activate the env.
    * `sudo pip3 install Flask`
    * `sudo pip3 install psycopg2` for postgresql
    * `sudo pip3 install requests`, similarly install all other required modules oauth2client for project
    * deactivate
    * Configure and enable the virtual host.
        * **WSGIScriptAlias / /var/www/Catalog/catalog.wsgi**   add this line in the file  /etc/apache2/sites-available/000-default.conf
        * Change the directory in the file to /var/www/Catalog/Catalog
    * Now create the .wsgi file in /var/www/Catalog/catalog.wsgi
    * Restart Apache using `sudo service apache2 restart` command.
#### Run the application in the browser using the url `http://18.220.62.60` or `http://18.220.62.60:80` 

* Credentials and Key for grader:
* postgresql login details username : **catalog** , password : **password**
* ssh username **grader** 
* I have uploaded the key file with the submission.
*Tips: 
* Look for apache2 logs at **/var/log/apache2/error.log** for information if you run into issues. 
* Don't forget to update the redirect urls in the project setup at google's OAuth authentication using google.It requires a domain name to be fully functional and I don't currently have a domain to use.
* **Snapshot option at lightsail was very helpful to me, would suggest until we learn it**
##### Scope for improvement
* Would like to buy a domain name and finish the complete setup with OAuth.