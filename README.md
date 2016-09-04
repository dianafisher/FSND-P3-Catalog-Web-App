# FSND-P5-Linux-Server-Configuration

The catalog web application from FSND-P3-Catalog-Web-App has been deployed to a baseline installation of Ubunu Linux on a virutal server in Amazon's Elastic Compute Cloud (EC2).  

## IP Address and URL

The server can be accessed via SSH at 52.88.231.187 port 2200

The hosted application can be reached at [http://ec2-52-88-231-187.us-west-2.compute.amazonaws.com](http://ec2-52-88-231-187.us-west-2.compute.amazonaws.com)

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
  3. Require password at least once when using sudo  
  Add the line ```Defaults  timestamp_timeout=5```
4. Set the password for the new user  
```$ sudo password <new-user>```
### Update Packages
1.  Get the list of available updates  
 ```$ sudo apt-get update```
2.  Install the updates  
 ```$ sudo apt-get upgrade```

### Configure SSH

Source: [Digital Ocean](https://www.digitalocean.com/community/tutorials/how-to-configure-ssh-key-based-authentication-on-a-linux-server)  

1. Open the ssh config file  
```$ sudo nano /etc/ssh/sshd_config```
2. Change the ssh port from 22 to 2200  
```Port 2200```
3. Disable remote login of root user:  
Change ```PermitRootLogin``` from ```without-password``` to ```no```
4. Require users to authenticate using RSA keys:  
Set ```PasswordAuthentication``` to ```no```
5. Append ```AllowUsers <new-user>```
6. Generate a SSH key-pair on your local machine  
```$ ssh-keygen```
7. Put the public key on the server (manual method): 
   1. Create a directory on the server named ```.ssh```  
   ```$ mkdir .ssh```
   2. Create a new file named ```authorized_keys``` in the ```.ssh``` directory  
   ```$ touch .ssh/authorized_keys```
   3. Copy the generated key data into the .ssh/authorized_keys file  
   ```$ nano .ssh/authorized_keys```
   4. Change the permissions on the ```.ssh``` directory and the ```authorized_keys``` file  
   ```$ chmod 700 .ssh```    
   ```$ chmod 644 .ssh/authorized_keys```
8. Restart the ssh service  
```$ sudo service ssh restart``` 
9.  Login with the new user  
```$ ssh -v <new-user>@52.88.231.187 -p2200```

### Configure Uncomplicated Firewall (UFW) to only allow SSH, HTTP, and NTP

Source: [Ubunu Documentation](https://help.ubuntu.com/community/UFW)  

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
 ```$ sudo ufw allow 123/udp```
7. Enable the UFW with the new settings  
```$ sudo ufw enable```

### Configure Local Timezone to UTC

 Source: [Ask Ubunu](http://askubuntu.com/questions/323131/setting-timezone-from-terminal)

1.  ```$ sudo dpkg-reconfigure tzdata```
2. Select ```None of the above``` from the list shown and then select ```UTC```.
3.  Install ntp (Network Time Protocol) to automatically keep the server's time accurate. 
Source: [Ubuntu Documentation](https://help.ubuntu.com/community/UbuntuTime#Using_the_Command_Line_.28terminal.29)
  1.  ```$ sudo apt-get install ntp```
  2. Create the cron file to run ntpdate daily   
   ```$ sudo nano /etc/cron.daily/ntpdate```  
    Add the following text:  
    ```#!/bin/sh  
       ntpupdate ntp.ubuntu.com```
   3.  Make sure the file is executable:  
    ```$ sudo chmod 755 /etc/cron.daily/ntpdate```


### Install and Configure Apache to serve a Python mod_wsgi application
1. Install Apache web server:  
```$ sudo apt-get install apache2```   
2.  Install the WSGI module for serving Python apps from Apache  
```$ sudo apt-get install libapache2-mod-wsgi```  
3.  Configure Apache to handle requests using the WSGI module.  
```$ sudo nano /etc/apache2/sites-enabled/000-default.conf```  
4.  Restart the Apache server  
```$ sudo service apache2 restart```  

### Install git, clone and setup Catalog App project

      

### Resolve Warnings
#####For warning, ```sudo: unable to resolve host ...```  
Source:[Ask Ubuntu](http://askubuntu.com/questions/59458/error-message-when-i-run-sudo-unable-to-resolve-host-none)  

1. Open ```$ nano /etc/hostname```  
2. Copy the hostname  
3. Append the hostname to the first line of /etc/hosts   
 ```$ sudo nano /etc/hosts```

##### For warning, ```System restart required``` after login
1.  List all packages which cause reboot  
```$ cat /var/run/reboot-required.pkgs```  
2.  List everything with high urgency   
```$ xargs aptitude changelog < /var/run/reboot-required.pkgs | grep urgency=high```  
3.  Reboot the system  
```sudo reboot```  