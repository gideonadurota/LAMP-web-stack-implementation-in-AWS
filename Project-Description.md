# LAMP Stack Implementation #

Prerequisites:
* VS Code
* AWS Account
* GitHub Personal Access Token

## 1. Launch EC2 Instance ##

* I logged into my AWS console
* I navigated to EC2 and clicked on create instance
* I created a free tier instance adding security rules for ssh (port 22) and http (port 80)
* I downloaded the ssh key 

## 2. Install Apache server ##

* I launched terminal and ran the command below to give the necessary permissions to the pem file

`sudo chmod 0400 lampwebstack.pem`

* ssh into the ec2 instance 

`ssh -i lampwebstack.pem user@ip-address`

* In order to install apache server, first I updated the packages on the ubuntu server

`sudo apt update -y`
`sudo apt install apache2 -y`

* I enabled firewall on on the server and allowed apache traffic using uncomplicated firewall

`sudo ufw enable`
`sudo ufw allow "Apache"`

* I confirmed that I could reach the apache website on localhost 

`curl http://localhost:80`

* I got the public ip address of the server using the command `curl -s http://169.254.169.254/latest/meta-data/public-ipv4` and navigated to the address on my browser.

## 3. Install MySql DB ##

The next step was to install and configure MySQL on the server

* use ‘apt’ to acquire and install this software:

`sudo apt install mysql-server -y`

* Set the mysql root password by first logging in as root user using `sudo mysql` then run the script

`ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'rootpassword'`

* I ran a security script that comes pre-installed with MySQL. This script will remove some insecure default settings and lock down access to your database system. Start the interactive script by running `sudo mysql_secure_installation`. You can accept the remaining prompts to finish mysql configuration


**Note:** At the time of this writing, the native MySQL PHP library mysqlnd doesn’t support caching_sha2_authentication, the default authentication method for MySQL 8. For that reason, when creating database users for PHP applications on MySQL 8, you’ll need to make sure they’re configured to use mysql_native_password instead.

## 4. Install PHP ##

We have Apache installed to serve the content and MySQL installed to store and manage the data. PHP is the component of our setup that will process code to display dynamic content to the end user. In addition to the *php* package, we’ll need *php-mysql*, a PHP module that allows PHP to communicate with MySQL-based databases. we’ll also need *libapache2-mod-php* to enable Apache to handle PHP files. Core PHP packages will automatically be installed as dependencies.

* Install the packages

`sudo apt install -y php php-mysql libapache2-mod-php`

* Confirm PHP version using `php -v`

## Create Apache Virtual Host ##

To test the setup with a PHP script, it’s best to set up a proper Apache Virtual Host to hold the website’s files and folders. Virtual host allows you to have multiple websites located on a single machine and users of the websites will not even notice it

* Create the directory for projectlamp using ‘mkdir’ command as follows:

`sudo mkdir /var/www/mylampproject`

* Next, assign ownership of the directory with the *$USER* environment variable, which will reference your current system user:

`sudo chown -R $USER:$USER /var/www/mylampproject`

* Create and open a new configuration file in Apache’s *sites-available* directory using vim

`sudo vi /etc/apache2/sites-available/mylampproject.conf`

* Paste the following configurations inside the config file and save

```
<VirtualHost *:80>
    ServerName mylampproject
    ServerAlias www.mylampproject 
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/mylampproject
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

* With this VirtualHost configuration, we’re telling Apache to serve projectlamp using /var/www/mylampproject as its web root directory. If you would like to test Apache without a domain name, you can remove or comment out the options ServerName and ServerAlias by adding a # character in the beginning of each option’s lines. Adding the # character there will tell the program to skip processing the instructions on those lines.

* Enable the new virtual host

`sudo a2ensite mylampproject`

* If you are not using custom doamin, use `a2dissite` command to disable the default website. If not the default will overwrite your own website

`sudo a2dissite 000-default`

* Confirm your syntax for any errors using `sudo apache2ctl configtest`

* Reload apache so the current changes take effect 

`sudo systemctl reload apache2`

* The new website is now active, but the web root /var/www/projectlamp is still empty. Create an index.html file in that location so that we can test that the virtual host works as expected:

`sudo echo 'Hello LAMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/mylampproject/index.html`

## 6. Enable PHP on the Website ##

With the default DirectoryIndex settings on Apache, a file named index.html will always take precedence over an index.php file. This is useful for setting up maintenance pages in PHP applications, by creating a temporary index.html file containing an informative message to visitors. Because this page will take precedence over the index.php page, it will then become the landing page for the application. Once maintenance is over, the index.html is renamed or removed from the document root, bringing back the regular application page




