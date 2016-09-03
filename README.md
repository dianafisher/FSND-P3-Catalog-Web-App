# FSND-P5-Linux-Server-Configuration

The catalog web application from FSND-P3-Catalog-Web-App has been deployed to a baseline installation of Ubunu Linux on a virutal server in Amazon's Elastic Compute Cloud (EC2).  

## IP Address and URL

The server can be accessed via SSH at 52.88.231.187 port 2200

The hosted application can be reached at http://ec2-52-88-231-187.us-west-2.compute.amazonaws.com/

## Summary of Software Installed and Configurations Made

### Create Development Environment

1. Download private key provided by Udacity
2. ssh into instance using ```$ ssh ~/.ssh/udacity_key.rsa root@52.88.231.187```


### User Management

Source: [DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-add-and-delete-users-on-an-ubuntu-14-04-vps)

1. Create new user (replace ```<new-user>``` with the username):  
 ```$ adduser <new-user>```

2. Grant the new user sudo permissions:
    1. Open the sudo configuration file  
 ```$ visudo```
    2. Below the line:  
```root ALL=(ALL:ALL) ALL```  
add  
```<new-user> ALL=(ALL:ALL) ALL```

3. Disable login of root user
4. Prompt users for password when using sudo
5. Require users to authenticate using RSA keys

### Update Packages
1.  Get the list of available updates  
 ```$ sudo apt-get update```
2.  Install the updates  
 ```$ sudo apt-get upgrade```

### Change SSH Port to 2200

### Configure Uncomplicated Firewall (UFW) to only allow SSH, HTTP, and NTP
1. Check the status of the UFW   
```$ sudo ufw status```
2. Temporarily deny all incoming connections   
```$ sudo ufw default deny incoming```
3. Allow outgoing connections  
```$ sudo ufw default allow outgoing```
4. Allow incoming TCP packets on port 2200 for SSH  
```$ sudo ufw allow 2200/tcp```
5. Allow incoming TCP packets on port 80 for HTTP  
 ```$ sudo ufw allow 80/tcp```
6. Allow incoming TCP packets on port 123 for NTP  
 ```$ sudo ufw allow 123/tcp```
7. Enable the UFW with the new settings  
```$ sudo ufw enable```

### Configure Local Timezone to UTC

 Source: [Ask Ubunu](http://askubuntu.com/questions/323131/setting-timezone-from-terminal)

1.  ```$ sudo dpkg-reconfigure tzdata```
2. Select 'None of the above' from the list shown and then select UTC.

### Install and Configure Apache to serve a Python mod_wsgi application

### Install git, clone and setup Catalog App project
1. Configure Catalog App as a wsgi app
      