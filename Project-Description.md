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



