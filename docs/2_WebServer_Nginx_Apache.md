# Linux - Web Server - Nginx & Apache


|               |                                                   |
| ------------- | ------------------------------------------------- |
| Copyright:    | © 2022 VEXIT , Tomorow is today ® , www.vexit.com |
| Author:       | Vex Tatarevic                                     |
| Date Created: | 2022-10-24                                        |
|               |                                                   |
 

<definition>

> **Web Server** - is a computer software and underlying hardware that accepts requests via HTTP or HTTPS. A user agent, (web browser or web crawler) makes a request for a web page or other resource using HTTP, and the server responds with the content of that resource or an error message. A web server can also accept and store resources sent from the user agent if configured to do so.
</definition>

<definition>

>**HTTP** - is the network protocol created to distribute web content. HTTPS is a secure version of HTTP which transfers data in encrypted form.
</definition>

## Web Server - Nginx

Ref: [Nginx Docs](https://nginx.org/en/docs/)

### 1. Nginx File Structure

Before we install Nginx we will display here a table explaining a short summary of its files and directories and their purpose. You can use this as a reference throughout this course.

| File                       | Purpose                                           |
| -------------------------- | ------------------------------------------------- |
| /var/www/html              | Website content as seen by visitors               |
| /etc/nginx                 | Location of the main Nginx application files      |
| /etc/nginx/nginx.conf      | The main Nginx configuration file                 |
| /etc/nginx/sites-available | List of all websites configured through Nginx     |
| /etc/nginx/sites-enabled   | List of websites actively being served by Nginx   |
| /var/log/nginx/access.log  | Access logs tracking every request to your server |
| /var/log/ngins/error.log   | A log of any errors generated in Nginx            |
|                            |                                                   |



### 2. Install Nginx

#### Install NGINX

- Install Nginx

      sudo apt update
      sudo apt install nginx

 - This will create dir **/www** inside **/var** and add default html page inside **/var/www/html/index.nginx-debian.html**

- NOTE: On Linux **/var/www/** is a default directory for web applications/websites

- Verify Nginx version

      sudo nginx -v

- Check to see if the Nginx service is running.

      sudo service nginx status

- You should see

      ● nginx.service - A high performance web server and a reverse proxy server
           Loaded: loaded (/lib/systemd/system/nginx.service; enabled; vendor preset: enabled)
           Active: active (running) since Wed 2023-08-30 01:13:46 UTC; 3min 51s ago
             Docs: man:nginx(8)
          Process: 395149 ExecStartPre=/usr/sbin/nginx -t -q -g daemon on; master_process on; (code=exited, status=0/SUCCESS)
          Process: 395150 ExecStart=/usr/sbin/nginx -g daemon on; master_process on; (code=exited, status=0/SUCCESS)
         Main PID: 395151 (nginx)
           CGroup: /system.slice/nginx.service
                   ├─395151 "nginx: master process /usr/sbin/nginx -g daemon on; master_process on;"
                   └─395152 "nginx: worker process" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" ""

      Aug 30 01:13:46 mysite1.com systemd[1]: Starting A high performance web server and a reverse proxy server...
      Aug 30 01:13:46 mysite1.com systemd[1]: Started A high performance web server and a reverse proxy server.



- You may get error like this


      × nginx.service - A high performance web server and a reverse proxy server
           Loaded: loaded (/lib/systemd/system/nginx.service; enabled; vendor preset: enabled)
           Active: failed (Result: exit-code) since Wed 2023-08-30 00:39:29 UTC; 4min 18s ago
             Docs: man:nginx(8)
          Process: 386794 ExecStartPre=/usr/sbin/nginx -t -q -g daemon on; master_process on; (code=exited, status=0/SUCCESS)
          Process: 386795 ExecStart=/usr/sbin/nginx -g daemon on; master_process on; (code=exited, status=1/FAILURE)

      Aug 30 00:39:28 mysite1.com nginx[386795]: nginx: [emerg] bind() to 0.0.0.0:80 failed (98: Unknown error)
      ...
      Aug 30 00:39:29 mysite1.com nginx[386795]: nginx: [emerg] still could not bind()
      Aug 30 00:39:29 mysite1.com systemd[1]: nginx.service: Control process exited, code=exited, status=1/FAILURE
      Aug 30 00:39:29 mysite1.com systemd[1]: nginx.service: Failed with result 'exit-code'.
      Aug 30 00:39:29 mysite1.com systemd[1]: Failed to start A high performance web server and a reverse proxy server.

In this case you need to check if there is another web server running on port 80. You can do this by running the following command:

    sudo netstat -tulpn | grep :80

Most likely you have Apache server installed and it's running on port 80. 
You should :
1. [Uninstall Apache](#uninstall-apache)
2. [Uninstall Nginx](#uninstall-nginx)
3. [Reinstall Nginx](#install-nginx)



#### Uninstall Apache 


- **SKIP THIS** if you don't have Apache installed.



Most VPS linux servers come preinstalled with Apache web server software.

This may cause a clas and a problem when you try to install and run nginx web server instead.

This is why we need to uninstall Apache first. We can reinstall Apache later if we need it.

- Check Apache info

      apt info apache2

- Stop Apache server

       sudo service apache2 stop

- Uninstall Apache

      sudo apt remove apache2

- Purge Apache

      sudo apt purge apache2*

- Confirm Apache is uninstalled

      sudo service apache2 status

  - You should get

        Unit apache2.service could not be found.

- Confirm there are no left over files from apache

      which apache2

  - You should get a blank line


#### Uninstall Nginx

- **SKIP THIS** if NGINX installation was successful.

- Stop Nginx server

      sudo service nginx stop

- Uninstall Nginx

      sudo apt remove nginx

- Purge Nginx

      sudo apt purge nginx

- Remove all the remaining dependencies that were used by NGINX but are not required anymore:

      sudo apt-get autoremove

- In some occurrences, running the above commands may not wipe out NGINX completely. Runthis command for extra cleanup:

      sudo apt-get remove --purge nginx*

      sudo apt-get autoremove --purge

- Confirm Nginx is uninstalled

      sudo service nginx status

  - You should get

        Unit nginx.service could not be found.

- Reinstall Nginx - go to [Install Nginx](#install-nginx) section

### 3. Install UFW Firewall

<definition>

> **Packets** - In networking, a packet is a small segment of a larger message. Data sent over Internet, is divided into packets. These packets are then recombined by the receiving device.
</definition>


<definition>

> **Kernel** - is the main program of a computer's operating system (like Linux) that has complete control over everything in the system. This part of operating system code is always active in memory and facilitates interactions between hardware and software.
</definition>


<definition>

> **Firewall** - is a generic name for a network security software or hardware that monitors and controls incoming and outgoing network traffic (**packets**) based on predefined security rules. A firewall creates a barrier between a trusted network and an untrusted network, such as the Internet.
</definition>

<definition>

> **Netfilter** - is a networking framework used by **Linux kernel** that provides customized handlers required for directing packets through a network and prohibiting packets from reaching sensitive locations within a network.
</definition>


<definition>

> **Iptables** - is a firewall program for Linux. It monitors traffic from and to Linux server machine using tables. These tables contain sets of rules, called chains, that will filter incoming and outgoing data packets. The iptables firewall interacts with the **netfilter**'s packet filtering hooks to filter both incoming and outgoing packets as well as route network **packets**.
</definition>

**Iptables** and **Netfilter** are complex to learn and use. 

This is why  **UFW** has been developed.

<definition>

> **UFW** (Uncomplicated Firewall) - is a program for managing **netfilter**, designed to be easy to use. It uses a command-line interface consisting of a small number of simple commands, and uses **iptables** for configuration.
</definition>



- Check if UFW is installed

      sudo ufw --help

  - If not installed you will get:

        sudo: ufw: command not found
    
If ufw is not installed follow the below steps to install it, otherwise skip to [Configure Firewall for Nginx](#configure-firewall-for-nginx):

- Update and upgrade apt packages

      sudo apt update
      sudo apt full-upgrade

- Install UFW

      sudo apt install ufw

- Check to see if the UFW service is running.

      sudo service ufw status


### 4. Configure Firewall for Nginx

- Before enabling ufw firewall, make sure you add a rule for SSH, otherwise you may get locked out of your server if you’re connected remotely.

      sudo ufw allow OpenSSH


- We want to open HTTP (80) and HTTPS (443) ports by allowing the ‘Nginx Full’ profile.​​​​​​​


- List the profiles available

      sudo ufw app list

  - You should see

        Available applications:
            Nginx Full
            Nginx HTTP
            Nginx HTTPS
            OpenSSH
 
- Allow **Nginx Full**

      sudo ufw allow 'Nginx Full'

- Enable ufw firewall.

      sudo ufw enable

  - Press y when asked to proceed.
  - You should get


        Command may disrupt existing ssh connections. Proceed with operation (y|n)? y
        Firewall is active and enabled on system startup


- Verify firewall status

      sudo ufw status

  - You should see

        Status: active

        To                         Action      From
        --                         ------      ----
        Nginx Full                 ALLOW       Anywhere
        OpenSSH                    ALLOW       Anywhere
        Nginx Full (v6)            ALLOW       Anywhere (v6)
        OpenSSH (v6)               ALLOW       Anywhere (v6)


- Reboot

      sudo reboot


### 5. Configure Default Site

- Verify the Nginx service is running

      sudo systemctl status nginx
      

#### Get Server IP

Your server is hosted on either **Home Server** or **VPS (Virtual Private Server)**

  1. On **VPS Server**:

      - Run `hostname -I`

      OR

      - Run `ifconfig`. Get IP address after the word **inet** in the section **eth0**. It should look like:

              eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
                    inet 123.234.345.45  netmask 255.255.255.0  broadcast 123.234.345.255

      OR
      
       - Run curl to ipinfo.io website `curl https://ipinfo.io/ip`

       - You should get IP like **123.234.345.45**


  2. On **Home Server** - Internal IP - Get local (LAN) IP address (you can access this address only on your home network but not from outside internet)
  
          hostname -I

       - You should get address like **192.168.1.105**

    
  3. **Home Server** - External IP - Get Internet (WAN) IP address that you can access from outside internet. NOTE: This works  only if you have been assigned static IP address from your internet provider. Otherwise you will have to use a dynamic DNS service like [noip.com](https://www.noip.com/) to get a domain name that will point to your dynamic IP address.

       3.1 Log into your router admin website in browser  ( e.g. http://192.168.1.1) and you should see your Internet IP address on the first page of the router admin website.

        OR

       3.2 Run curl to ipinfo.io website `curl https://ipinfo.io/ip` in your server's terminal

        OR

        3.3 Open URL in browser - https://www.whatismyip.com/

        NOTE: 3.2 and 3.3 will give you wrong IP if you have VPN running 

      - You should see your Internet IP address like **123.234.345.45**

#### Open URL in Browser

If you are using VPS Server, you can open URL in browser like: http://{{IP}} where {{IP}} is your server's IP address like: http://123.234.345.45

If you are using Home Server, you can only test your server on your local network - for now - until we set up port forwarding in the next section. You can open URL in browser like: http://{{IP}} where {{IP}} is your server's local IP address like: http://192.168.1.105

- Open URL in browser - http://{{IP}} - You should see default page that says

      Welcome to nginx!
      If you see this page, the nginx web server is successfully installed and working. Further configuration is required.
      
      For online documentation and support please refer to nginx.org.
      Commercial support is available at nginx.com.
      
      Thank you for using nginx.

The default page is called **index.nginx-debian.html** and is located in the web sites root directory of your server: **/var/www/html**. 

- Edit default page in nano text editor:

      sudo nano /var/www/html/index.nginx-debian.html

- Change line **"Welcome to nginx!"** to **"Welcome to nginx on {{MY_SITE_NAME}}!"** - Replace {{MY_SITE_NAME}} with your site name e.g. mysite1.com

- Save and exit: **CTRL + S** , **CTRL + X**

- Refresh page in browser - You should see your changes

#### Home Server - Set up Port Forwarding

**_SKIP this section if you are using VPS Server._**

<hr/>

In order to expose your Home Server to the internet, you must create a new **Virtual Server** with a new **External Port** that maps to **Internal IP** address of your Server machine and the **Internal Port 80**. This is known as **Port Forwarding**. Port 80 is a default port which NGINX server listens on.
   
    
- **Open router admin website** in browser  (**http://192.168.1.1/** or **http://192.168.0.1/**)
     
- **Copy External IP** - IP address should be visible on the first page of the router admin website.
     
- **Add new Virtual Server**. To find Virtual Servers it may be differend on different routers. On tplink router you go to: **Advanced > NAT Forwarding > Virtual Servers > + Add**.  Enter the following configuration (0)
 
    | Field         | Value         | Description                                                                     |
    | ------------- | ------------- | ------------------------------------------------------------------------------- |
    | Service Type  | HTTP          |                                                                                 |
    | External Port | 1234          | Pick any number 1024 - 49151 that is not already used for other Virtual Servers |
    | Internal IP   | 192.168.1.105 | This should be the Internal IP we copied previously                             |
    | Internal Port | 80            | NGNX default site is configured to listen port 80                               |
    | Protocol      | TCP           |                                                                                 |


- **Open URL in browser** - Enter URL and port http://{{IP}}:{{PORT}} in your browser like: http://123.234.345.45:1234

- You should see default Nginx page
- If you don't see the page, reboot your server and try again

You can also check if your port is open by using a tool like : [IsMyPortOpen](https://ismyportopen.com/)


#### Server Blocks Structure Explained

In Apache web server we have **Virtual Hosts** for hosting multiple websites on the same IP. 

In **NGINX web server**, we have a concept called **Server Blocks** for hosting multiple virtual websites on the same IP.

Server Blocks should be configured inside the file **named same as the website's domain name** and they contain the configuration for that website inside the **server** code block like:

    server {
          ...
    }

Server Block file must be located inside **/etc/nginx/sites-available/** directory.

After creating the Server Block file, we will create a symbolic link to it inside **/etc/nginx/sites-enabled/** directory, for the sites we want to be active.

Finally all the enabled Server Block files are included in the main Nginx configuration file **/etc/nginx/nginx.conf** using the **include** directive like:

    include /etc/nginx/sites-enabled/*;

> **nginx.conf** file is the main configuration file for Nginx web server. It contains directives that affect the overall operation of Nginx web server.

**NOTE:** In some examples online, authors will demonstrate using **server block** configuration directly inside the **nginx.conf** file for the simplicity like:

    /etc/nginx/nginx.conf
    --------------------
      ...
      http {
        index index.html;

        server {
            server_name www.domain1.com;
            access_log logs/domain1.access.log main;

            root /var/www/domain1.com/htdocs;
        }

        server {
            server_name www.domain2.com;
            access_log  logs/domain2.access.log main;

            root /var/www/domain2.com/htdocs;
        }
      }

 **YOU SHOULD NOT** place server blocks for sites directly inside nginx.conf in your real server setup. Above snippet is only for demonstration.
 
Instead you should configure each Server Block inside its respective **sites_available** file e.g. /etc/nginx/sites-available/mysite1.com and then sym link only the sites we want to be active to **/etc/nginx/sites-enabled/** directory.

#### Change Default Site Mapping

- Go inside **/etc/nginx/sites-available/** directory 

      cd /etc/nginx/sites-available/

  - You should see a file named **default** which is the default site configuration file for Nginx

- Backup the default site file

  - Create directory inside our user home directory to store backups of NGINX server block configuration files 

        mkdir -p ~/backups/etc_nginx_sites-available

  - Copy default site file to backups directory, naming the backup file as default_{{current_date_stamp}} like this

        cp default ~/backups/etc_nginx_sites-available/default_$(date +%Y-%m-%d_%H:%M:%S)

- Open the default site file for edit

      sudo nano default

- Change line **"root /var/www/html;"** to **"root /var/www/default;"** . Save and exit `CTRL + S` , `CTRL + X`

- Rename directory **/var/www/html** to **/var/www/default**

      sudo mv /var/www/html /var/www/default

  - **NOTE:** /var/www/html is the directory where the html and other code files for the default site are stored. We have renamed it to /var/www/default to make it the same as the name of the default site configuration file inside **sites-available** directory. We will follow this convention for all other sites we create.

- Restart Nginx

      sudo service nginx restart

- Open URL in browser - http://{{IP}} - You should see default page that says

      Welcome to nginx!
      If you see this page, the nginx web server is successfully installed and working. Further configuration is required.
      
      For online documentation and support please refer to nginx.org.
      Commercial support is available at nginx.com.
      
      Thank you for using nginx.

In summary we have renamed directory "html" to "default" because we want the name of every site's configuration file inside **sites-available** to match the name of the site's source directory inside **/var/www/**.

Directory name **html** was too vague, so we renamed it to **default**, which makes sense because it's a folder for our default site.



### 6. Create a New Site on Nginx

#### 6.1. Create Directories and Set Permissions

Now we will setup a site for a sample domain name **"mysite1.com"** and its 2 subdomains **"dev.mysite1.com"** and **"test.mysite1.com"** that will be used for development and testing environments of the site mysite1.com.

mysite1.com is just an example for the purpose of demonstration. You should use your own domain name.

- Create three new directories inside the **/var/www/** for our three sites - copy and paste the following command in your terminal and hit Enter. _**(Replace mysite1.com with your domain name)**_

      sudo mkdir -p /var/www/mysite1.com/htdocs && \
      sudo mkdir -p /var/www/dev.mysite1.com/htdocs && \
      sudo mkdir -p /var/www/test.mysite1.com/htdocs

  - **-p** is used to create nested directories - in our case we are creating htdocs directory inside each site's directory.
  - **Double Ampersand** `&&` is used to separate multiple commands. It executes the next command only if the previous command was successful. This is a good practice to avoid errors in case the previous command fails.
  - **Backslash** `\` at the end of each line is used to continue the command on the next line. This is done for readability and to avoid long lines of code. Also it is used when we are executing multiple commands in one line


We have created a folder named **htdocs** inside each site's directory.

 > **htdocs** is traditionally the directory for the compiled files of your clientside application (like images, html files, javascript and css files) that are downloaded to visitors' device for viewing via browser. This directory name can be changed to whatever value you want. However this is the traditional naming convention for this directory which is also used in other web servers like Apache.

- View created directories

      ll /var/www/

  - You should see something like

        drwxr-xr-x 2 root root 4096 Sep  1 12:00 dev.mysite1.com
        drwxr-xr-x 2 root root 4096 Sep  1 12:00 mysite1.com
        drwxr-xr-x 2 root root 4096 Sep  1 12:00 test.mysite1.com

- Allow current admin user to modify files in all www directories

      sudo chown -R $(whoami):$(whoami) /var/www/

    - The $(whoami) variable will take the value of the user you are currently logged in as.

- Set permissions to **/var/www** and its contents to 755 so that pages can be served correctly.

      sudo chmod -R 755 /var/www

#### 6.2. Create Sample Web Page

- Create index.html for each site using echo command - copy and paste the following command in your terminal and hit Enter. _**(Replace mysite1.com with your domain name)**_

      sudo echo "Welcome to mysite1.com!" > /var/www/mysite1.com/htdocs/index.html && \
      sudo echo "Welcome to dev.mysite1.com!" > /var/www/dev.mysite1.com/htdocs/index.html && \
      sudo echo "Welcome to test.mysite1.com!" > /var/www/test.mysite1.com/htdocs/index.html


#### 6.3. Create Server Blocks

Server blocks are configuration files for each site that contain the configuration for that site. They are stored in **/etc/nginx/sites-available/** directory.

We can create server block for mysite1.com in one of 2 ways:
1. [Option 1 - Create server block by duplicating the default server block file and modifying it to suit our needs](#option-1---create-server-block-from-default-block)
2. [Option 2 - Create server block from scratch](#option-2---create-server-block-from-scratch)


##### Option 1 - Create Server Block From Default Block

- **Use default block as a template** - Copy default server block **"/etc/nginx/sites-available/default"** to use as a template for our new server blocks

      sudo cp /etc/nginx/sites-available/default /etc/nginx/sites-available/mysite1.com

**IMPORTANT:** Your server block file should be named after your domain name. In this example we are using mysite1.com and mysite2.com as domain names. You should replace these with your own domain names.


- Open for edit mysite1.com

      sudo nano /etc/nginx/sites-available/mysite1.com

- **Set site's port on which it should listen** - Search for listen directive : **Ctrl + W** , Type **"listen 80"** , hit **Enter** . You should see

      listen 80 default_server;
      listen [::]:80 default_server;

  - Delete the word **"default_server"** from lines above.

  - **NOTE:** Only one server block can be default_server. This keyword defines which block nginx should navigate to if the server_name requested does not match any server blocks.

- **Set site's root location** - Search **CTRL + W** , type **"root /var/www/default;"** , hit Enter  :

  - Replace **"root /var/www/default;"** with

        root /var/www/mysite1.com/htdocs;

- Set site's default start page - Search **CTRL + W** , type **"index index.html index.htm index.nginx-debian.html;"** :

  - Remove **"index.nginx-debian.html;"**
   
- **Set site's server_name** - Search **CTRL + W** , type **"server_name _;"**  :

  - Replace **"server_name _;"** with

        server_name mysite1.com www.mysite1.com;      

- Remove all the comments in the file by deleting lines that start with **#**.
  - Select comment block - Set Mark at the start of the comment  `ALT + A` , 
  - Press arrow down till you select the last line of the comment
  - Delete `CTRL + K`

The file should look like this:

    server {
        listen 80;
        listen [::]:80;

        root /var/www/mysite1.com/htdocs;

        index index.html index.htm;

        server_name mysite1.com www.mysite1.com;

        location / {
            try_files $uri $uri/ =404;
        }
    }

- Save and close file : **CTRL + S** , **CTRL + X**

##### Option 2 - Create Server Block From Scratch

- Create new server block file for mysite1.com. _**(Replace mysite1.com with your domain name)**_

      sudo nano /etc/nginx/sites-available/mysite1.com

- Add the following configuration to the file. _**(Replace mysite1.com with your domain name)**_

      server {
          listen 80;
          listen [::]:80;
  
          root /var/www/mysite1.com/htdocs;
  
          index index.html index.htm;
  
          server_name mysite1.com www.mysite1.com;
  
          location / {
              try_files $uri $uri/ =404;
          }
      }

- Save and close file : **CTRL + S** , **CTRL + X**


##### Create Dev and Test Server Blocks
 
- Go to **/etc/nginx/sites-available/** directory
 
      cd /etc/nginx/sites-available/

- Copy mysite1.com file to dev.mysite1.com and test.mysite1.com files. _**(Replace mysite1.com with your domain name)**_

      sudo cp mysite1.com dev.mysite1.com && \
      sudo cp mysite1.com test.mysite1.com

- Edit dev.mysite1.com. _**(Replace mysite1.com with your domain name)**_

      sudo nano dev.mysite1.com

  - Replace all occurences of word mysite1.com with dev.mysite1.com in the file

  - Save and close the file : **CTRL + S** , **CTRL + X**

- Edit test.mysite1.com file. _**(Replace mysite1.com with your domain name)**_ 

      sudo nano test.mysite1.com

  - Replace all occurences of word mysite1.com with test.mysite1.com in the file

  - Save and close the file : **CTRL + S** , **CTRL + X**

##### Verify Nginx config

- **Verify Nginx config**

      sudo nginx -t

  - Should produce

        nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
        nginx: configuration file /etc/nginx/nginx.conf test is successful 

  - Above command checks the syntax of the configuration files and tests the configuration files for errors.


#### 6.4 Create Symbolic Links

Nginx reads Symbolic Links during the startup. Only sites inside sites-enabled directory will be accessible via the internet.

We will create 3 symbolic links for our 3 sites: mysite1.com, dev.mysite1.com and test.mysite1.com

- Create symbolic links from sites-available to the sites-enabled. _**(Replace mysite1.com with your domain name)**_

      sudo ln -s /etc/nginx/sites-available/mysite1.com /etc/nginx/sites-enabled/ && \
      sudo ln -s /etc/nginx/sites-available/dev.mysite1.com /etc/nginx/sites-enabled/ && \
      sudo ln -s /etc/nginx/sites-available/test.mysite1.com /etc/nginx/sites-enabled/

- If later you want to deactivate the site without deleting it, you can remove the symbolic link like

      sudo rm /etc/nginx/sites-enabled/{{SITE_NAME}}

- Verify sym links - list files in /etc/nginx/sites-enabled/ directory and confirm that desired active sites are visible

      ll /etc/nginx/sites-enabled/



#### 6.5 Verify & Reload

Every time you edit or create nginx server block files you MUST verify the config and then reload nginx in order for the changes to take effect.

- Verify and Reload Nginx

      sudo nginx -t && \
      sudo nginx -s reload

### 7. Point Domain Name to Server

In order for site on Nginx to be accessible via internet by typing our custom domain name, we need to point the domain name to our server.

We need to edit DNS (Domain Name System) settings of our domain name, on the website of the domain name host where the domain name is registered.

In this example we will use Godaddy.com, as an example domain name server, to explain how to do this, but the process should be very simillar with other domain name hosts.

Ref: https://www.godaddy.com/garage/configuring-and-working-with-domains-dns/

Let's assume we have registered domain name mysite1.com on godaddy.com

- Go to domain management page with list of your domain names : https://dcc.godaddy.com/domains

- Select your desired domain name and navigate to DNS management page for that domain

- You should see a section for **DNS Records** and another section for **Nameservers**

**IMPORTANT:** If You are not using the nameservers of the domain name host, you will not be able to edit DNS records. You will **have to change the nameservers to the default nameservers of the domain name host first.** For example if you are using Godaddy.com domain name host, you will have to change the nameservers to the default Godaddy.com nameservers e.g. ns21.domaincontrol.com and ns22.domaincontrol.com. After that you will be able to edit DNS records.

- You should see a table of default **DNS Records** for the domain like

| Type  | Name           | Data                                        | TTL         | Delete | Edit |
| ----- | -------------- | ------------------------------------------- | ----------- | ------ | ---- |
| A     | @              | Parked                                      | 600 Seconds |        |      |
| NS    | @              | ns21.domaincontrol.com.                     | 1 Hour      |        |      |
| NS    | @              | ns22.domaincontrol.com.                     | 1 Hour      |        |      |
| CNAME | www            | mysite1.com                                 | 1 Hour      |        |      |
| CNAME | _domainconnect | _domainconnect.gd.domaincontrol.com.        | 1 Hour      |        |      |
| SOA   | @              | Primary nameserver: ns21.domaincontrol.com. | 1 Hour      |        |      |
|       |                |                                             |             |        |      |

#### DNS Records explained

Ref: https://phoenixnap.com/kb/dns-record-types 

|           |                                               |                                                                                                                                                                    |
| --------- | --------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **A**     | (Address) record                              | Uses an IP address to connect your domain to a website. It is also used to create subdomain such as www, that points to an IP address.                             |
| **AAAA**  | (Address) record                              | Similar to A record, but it maps IPv6 address to your domain.                                                                                                      |
| **@**     | Shorthand symbol for ORIGIN                   | It points to your base domain, e.g. mysite1.com                                                                                                                    |
| **CNAME** | (Canonical Name) record - is also a subdomain | The difference between a A record and a CName record is that a A record points to the IP address of a server, while CName points to a domain name e.g. mysite1.com |
| **TTL**   | (Time to Live)                                | the average time it will take for your changes to take into effect                                                                                                 |

#### Edit DNS Records

- First get your **IPv4 and IPv6** addresses - in your server terminal run:

      hostname -I

  - You should see something like
      
        127.0.0.1 123.234.345.45 12ab:1234:12:cd56:0:0:0:7

  - These are your {{LOCALHOST}} {{IPv4}} {{IPv6}} addresses separated by space

In our Domain name host control panel DNS Records table 
- Delete all records that are allowed to be deleted - which should be just the A records and CNAME records 
- Add 3 new A records (@, www, *) and set TTL to Default which should be 600 seconds = 1/2 Hour
- Add 1 AAAA record and set TTL to Default which should be 600 seconds = 1/2 Hour. 

It should look like this:

| Type | Name | Data                      | TTL         |
| ---- | ---- | ------------------------- | ----------- |
| A    | @    | 123.234.345.45            | 600 Seconds |
| A    | www  | 123.234.345.45            | 600 Seconds |
| A    | *    | 123.234.345.45            | 600 Seconds |
| AAAA | @    | 12ab:1234:12:cd56:0:0:0:7 | 600 Seconds |

  - **NOTE:** 123.234.345.45 is the IPv4 address of our web server machine and 12ab:1234:12:cd56:0:0:0:7 is IPv6 address.
  - **@** - is a shorthand symbol for ORIGIN. It points to your base domain, e.g. mysite1.com
  - **www** - is a subdomain that points to your base domain, e.g. www.mysite1.com
  - **\*** - wildcard symbol - It means that any subdomain that is not explicitly defined will point to your base domain's IP address. For example subdomain dev.mysite1.com will point to the same IP address as mysite1.com

**IMPORTANT:** We MUST add AAAA record if we are planning to use our own SMTP mail server. Otherwise it's not necessary, but its a good practice to add it. 


#### Navigate to Website URL

You may have to wait for 1 hour after domain name server changes before the changes take effect.

- Open browser

- Type in your main domain name: mysite1.com. _**(Replace mysite1.com with your domain name)**_

You will almost certainly not see your page in any of the browsers **because you have not set up SSL certificate** yet. Browsers will block your site because it is not secure.

Here are the typical error messages you will see in different browsers:

|         |                                           |
| ------- | ----------------------------------------- |
| Chrome  | Your connection is not private            |
| Firefox | Did Not Connect: Potential Security Issue |
| Edge    | Your connection isn't private             |
| Brave   | Your connection is not private            |
| Safari  | Safari Can't Open the Page                |



In the next steps we will set up SSL certificate and enable secure HTTPS protocol.

### 8. Set Up SSL 


<definition>

>**SSL (Secure Sockets Layer)** - is a security protocol that provides privacy, authentication, and integrity to Internet communications.
</definition>

<definition>

>**SSL certificate** - is a file installed on a web server that digitally engraves an encrypted key. It enables encrypted exchange of data between a server and users’ browsers.
</definition>

<definition>

>**CA (Certificate Authority)** - is the organization that provides the SSL certificate.
</definition>


<definition>

>**CSR (Certificate Signing Request)** - is the first step in setting up an SSL Certificate on your website. SSL certificates are provided by Certificate Authorities (CA), which require a Certificate Signing Request (CSR).
</definition>


#### 8.1 Get SSL Certificate

To skip explanation and get straight to the Final Solution, [click here](#final-solution--set-up-multi-domain-ssl-with-auto-renewal) .

----------------------


Normally SSL Certificate costs money to set up with CA. The process involves generating a private key and CSR, submitting the CSR through CA's website and paying them for the certificate.

However you can set up a free SSL Certificate using CA organization called  [Let's Encrypt](https://letsencrypt.org/).

- Install Lets Encrypt CLI program

      sudo apt update && \
      sudo apt upgrade && \
      sudo apt install letsencrypt

**NOTE:** The above will also install **certbot** program which we will use later on to manage our certificates.

**NOTE:** In the next 3 sections we show you progression of learning for how to set up the most flexible SSL certificate.  If you are in a rush and just want to set up SSL certificate that works for all subdomains and can be auto renewed, then skip the next 3 progression sections and go directly to **Final Solution section** [Set up Multi-Domain SSL with Auto Renewal](#final-solution--set-up-multi-domain-ssl-with-auto-renewal)


##### Progression 1 : Set up Specific SSL Certificate

In this section we will learn how to set up a specific SSL certificate for our domain name and then how to uninstall it. This certificate will not cater for subdomains like dev.mysite1.com and test.mysite1.com and that's why we will uninstall it afterwards and install a wildcard certificate. 

- Get certificate from LetsEncrypt

      sudo letsencrypt certonly 
        -a webroot \
        --webroot-path=/var/www/mysite1.com/htdocs/ \
        -d mysite1.com \
        -d www.mysite1.com \

  - The letsencrypt client will now ask you to enter your e-mail address and to accept the terms of usage. It looks like this:

        Saving debug log to /var/log/letsencrypt/letsencrypt.log
        Enter email address (used for urgent renewal and security notices)
         (Enter 'c' to cancel): contact@mysite1.com

        - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
        Please read the Terms of Service at
        https://letsencrypt.org/documents/LE-SA-v1.3-September-21-2022.pdf. You must
        agree in order to register with the ACME server. Do you agree?
        - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
        (Y)es/(N)o: y

        - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
        Would you be willing, once your first certificate is successfully issued, to
        share your email address with the Electronic Frontier Foundation, a founding
        partner of the Let's Encrypt project and the non-profit organization that
        develops Certbot? We'd like to send you email about our work encrypting the web,
        EFF news, campaigns, and ways to support digital freedom.
        - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
        (Y)es/(N)o: y
        Account registered.
        Requesting a certificate for mysite1.com and www.mysite1.com

        Successfully received certificate.
        Certificate is saved at: /etc/letsencrypt/live/mysite1.com/fullchain.pem
        Key is saved at:         /etc/letsencrypt/live/mysite1.com/privkey.pem
        This certificate expires on 2023-05-22.
        These files will be updated when the certificate renews.
        Certbot has set up a scheduled task to automatically renew this certificate in the background.

        - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
        If you like Certbot, please consider supporting our work by:
         * Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
         * Donating to EFF:                    https://eff.org/donate-le
        - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -


  - The following files will be placed on your server instance:

|                                                 |                                                                                                                                                                                                                      |
| ----------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| /etc/letsencrypt/live/mysite1.com/cert.pem      | The signed certificate                                                                                                                                                                                               |
| /etc/letsencrypt/live/mysite1.com/chain.pem     | The certificate chain (root certificate and all intermediate certificates) needed to validate the certificate is signed by a trusted root certificate.                                                               |
| /etc/letsencrypt/live/mysite1.com/fullchain.pem | A combination of both cert.pem and fullchain.pem.                                                                                                                                                                    |
| /etc/letsencrypt/live/mysite1.com/privkey.pem   | The private key. Keep this file private and secure at all times. Compromise of this file (for example, emailing this file, downloaded by a third party etc.) can allow third parties to hijack your SSL certificate. |


- **PROBLEM** - This certificate set-up will not allow you to have https on subdomains other than **www**. This is a problem because later on we want to have other subdomains for other environments where we will deploy our application like **dev.mysite1.com** and **test.mysite1.com**.

In the next 2 sections we will uninstall this certificate and set up a wildcard certificate that will allow us to have https on any subdomain as well as the main domain of our website.

##### Progression 2 : Uninstall SSL Certificate

- Show list of installed certificate folders

      sudo ls -l /etc/letsencrypt/live/

  - You should see folder with same name as your domain e.g. mysite1.com


- **List certificates** using certbot command

      sudo certbot certificates

  - You should see something like

        Saving debug log to /var/log/letsencrypt/letsencrypt.log

        - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
        Found the following certs:
          Certificate Name: mysite1.com
            Serial Number: 5e1d70f5ae264525a75e2654c64677ae
            Key Type: RSA
            Domains: mysite1.com www.mysite1.com
            Expiry Date: 2023-12-12 21:18:38+00:00 (VALID: 70 days)
            Certificate Path: /etc/letsencrypt/live/mysite1.com/fullchain.pem
            Private Key Path: /etc/letsencrypt/live/mysite1.com/privkey.pem
        - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

- Take a note of the **Certificate Name** above

- **Revoke certificate**

      sudo certbot revoke --cert-name mysite1.com

  - You should get
      
        Saving debug log to /var/log/letsencrypt/letsencrypt.log  
        - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
        Would you like to delete the certificate(s) you just revoked, along with all
        earlier and later versions of the certificate?
        - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
        (Y)es (recommended)/(N)o: Y  
        - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
        The following certificate(s) are selected for deletion:  
          * mysite1.com  
        WARNING: Before continuing, ensure that the listed certificates are not being
        used by any installed server software (e.g. Apache, nginx, mail servers).
        Deleting a certificate that is still being used will cause the server software
        to stop working. See https://certbot.org/deleting-certs for information on
        deleting certificates safely.  
        Are you sure you want to delete the above certificate(s)?
        - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
        (Y)es/(N)o: Y
        Deleted all files relating to certificate mysite1.com.
        Congratulations! You have successfully revoked the certificate that was located at /etc/letsencrypt/live/mysite1.com/cert.pem.

- Show list of installed certificate folders again and you will see that the folder with same name as your domain e.g. mysite1.com is gone

      sudo ls -l /etc/letsencrypt/live/


##### Progression 3 : Set up Wildcard SSL Certificate with Manual DNS Verification

In the previous section we learnt how to set up SSL certificate for a specific domain. This certificate is enough if we just want to cover our domain name e.g. mysite1.com and www.mysite1.com. However if we want to have https on subdomains like something.mysite1.com, then we need to set up a wildcard certificate.


We will use certbot program to generate the wildcard certificate.


- Show all certbot functions

      sudo certbot --help

- Generate wildcard ssl certificate with certbot

      sudo certbot certonly \
            --manual \
            --preferred-challenges=dns \
            --email=my-email@gmail.com \
            --agree-tos \
            -d mysite1.com \
            -d *.mysite1.com

  - Replace my-email@gmail.com with your email address
  - Replace mysite1.com with your domain name
  - The meaning of the arguments in the above command is:
      |                             |                                                   |
      | --------------------------- | ------------------------------------------------- |
      | certonly                    | Obtain or renew a certificate, but do not install |
      | --manual                    | Obtain certificates interactively                 |
      | --preferred-challenges=dns: | Use dns to authenticate domain ownership          |
      | --server                    | Specify the endpoint to use to generate           |
      | --agree-tos                 | Agree to the ACME server’s subscriber terms       |
      | -d                          | Domain name to provide certificates for           |

  - **NOTE:** The wild card specification in the above command is **-d *.mysite1.com** . The actual wild card part of the domain name is specified with the asterisk character (*). The asterisk must be the first character of the domain name, and it must be a valid domain name component. For example, *.mysite1.com will match mysite1.com, www.mysite1.com, dev.mysite1.com, test.mysite1.com, but not mysite1.com.uk or www.mysite1.com.uk.

- After you run above command you will get this prompt:

      Saving debug log to /var/log/letsencrypt/letsencrypt.log
      Requesting a certificate for mysite1.com and *.mysite1.com

      - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
      Please deploy a DNS TXT record under the name:

      _acme-challenge.mysite1.com.

      with the following value:

      c8171dd5-a761-4311-b44f-c7fe9175abe3

      Before continuing, verify the TXT record has been deployed. Depending on the DNS
      provider, this may take some time, from a few seconds to multiple minutes. You can
      check if it has finished deploying with aid of online tools, such as the Google
      Admin Toolbox: https://toolbox.googleapps.com/apps/dig/#TXT/_acme-challenge.mysite1.com.
      Look for one or more bolded line(s) below the line ';ANSWER'. It should show the
      value(s) you've just added.

      - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
      Press Enter to Continue

**DO NOT PRESS ENTER YET.** We need to do the following steps first.

- **Go to your DNS management page** on your domain name host website and add a new TXT record with the name _acme-challenge and value that was given to you in the command response above e.g. c8171dd5-a761-4311-b44f-c7fe9175abe3. It should look like this:

  | Type | Name            | Data                                 | TTL  |
  | ---- | --------------- | ------------------------------------ | ---- |
  | TXT  | _acme-challenge | c8171dd5-a761-4311-b44f-c7fe9175abe3 | Auto |

- **Verify TXT record deployed** - Open a second terminal and connect to your server and use dig to check if the TXT record has been deployed

      dig TXT _acme-challenge.mysite1.com +short

  - You should get as a response the value that you entered in the DNS management page e.g. c8171dd5-a761-4311-b44f-c7fe9175abe3

  - If not, try google toolbox to check if the TXT record has been deployed

      https://toolbox.googleapps.com/apps/dig/#TXT/_acme-challenge.mysite1.com

  - If not, wait for a few minutes and try again

- **Continue certificate set-up** - in the first terminal where your certificate setup is waiting on you to set DNS record, hit **Enter** . You will get this response

      Successfully received certificate.
      Certificate is saved at: /etc/letsencrypt/live/mysite1.com/fullchain.pem
      Key is saved at:         /etc/letsencrypt/live/mysite1.com/privkey.pem
      This certificate expires on 2024-01-01.
      These files will be updated when the certificate renews.
      
      NEXT STEPS:
      - This certificate will not be renewed automatically. Autorenewal of --manual certificates requires the use of an authentication hook script (--manual-auth-hook) but one     was not provided. To renew this certificate, repeat this same certbot command before the certificate's expiry date.
      
      - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
      If you like Certbot, please consider supporting our work by:
       * Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
       * Donating to EFF:                    https://eff.org/donate-le
      - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

Your wildcard certificate is now installed inside **/etc/letsencrypt/live/mysite1.com/** folder.


- **PROBLEM** - The above certificate set-up is manual and will not be renewed automatically. To renew this certificate, you will have to repeat the same certbot command before the certificate's expiry date. This is not practical for a production website.

In the next section we will learn how to set up a wildcard certificate that will be renewed automatically.

##### Final Solution : Set up Multi-Domain SSL with Auto Renewal 

- Refs: 
  - [Certbot Docs - Nginx Plugin](https://eff-certbot.readthedocs.io/en/stable/using.html#nginx)
  - [NGINX.com - Using Letsencrypt SSL Certificate on NGINX web server](https://www.nginx.com/blog/using-free-ssltls-certificates-from-lets-encrypt-with-nginx/)

- If you have installed SSL certificate in the previous sections, then first go and  [Uninstall SSL Certificate](#progression-2--uninstall-ssl-certificate)



- If you have installed Letsencrypt in previous sections, Uninstall it first

  - Remove certbot packages

        sudo apt remove --purge certbot

  - If you installed Certbot using certbot-auto, remove the script

        sudo rm /usr/local/bin/certbot-auto

  - Remove the Letsencrypt directories

        sudo rm -rf /etc/letsencrypt && \
        sudo rm -rf /var/lib/letsencrypt

  - Remove unused dependencies and packages

        sudo apt autoremove

- Install **certbot**

      sudo apt install certbot


- Install certbot **plugin for nginx**

      sudo apt install python3-certbot-nginx

- **Generate certificates with certbot** and nginx plugin, for your domain mysite1.com and all its subdomains: www.mysite1.com, dev.mysite1.com, test.mysite1.com. _**(Replace mysite1.com with your domain name)**_

      sudo certbot --nginx -d mysite1.com -d www.mysite1.com -d dev.mysite1.com -d test.mysite1.com

  - Type `Y` to agree with all the prompts
  
- You should see output like this:

      Saving debug log to /var/log/letsencrypt/letsencrypt.log
      Enter email address (used for urgent renewal and security notices)
       (Enter 'c' to cancel): my-email@my-website.com
      
      - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
      Please read the Terms of Service at
      https://letsencrypt.org/documents/LE-SA-v1.3-September-21-2022.pdf. You must
      agree in order to register with the ACME server. Do you agree?
      - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
      (Y)es/(N)o: y
      
      - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
      Would you be willing, once your first certificate is successfully issued, to
      share your email address with the Electronic Frontier Foundation, a founding
      partner of the Let's Encrypt project and the non-profit organization that
      develops Certbot? We'd like to send you email about our work encrypting the web,
      EFF news, campaigns, and ways to support digital freedom.
      - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
      (Y)es/(N)o: y
      Account registered.
      Requesting a certificate for mysite1.com and 3 more domains
      
      Successfully received certificate.
      Certificate is saved at: /etc/letsencrypt/live/mysite1.com/fullchain.pem
      Key is saved at:         /etc/letsencrypt/live/mysite1.com/privkey.pem
      This certificate expires on 2024-06-23.
      These files will be updated when the certificate renews.
      Certbot has set up a scheduled task to automatically renew this certificate in the background.
      
      Deploying certificate
      Successfully deployed certificate for mysite1.com to /etc/nginx/sites-enabled/mysite1.com
      Successfully deployed certificate for www.mysite1.com to /etc/nginx/sites-enabled/mysite1.com
      Successfully deployed certificate for dev.mysite1.com to /etc/nginx/sites-enabled/dev.mysite1.com
      Successfully deployed certificate for test.mysite1.com to /etc/nginx/sites-enabled/test.mysite1.com
      Congratulations! You have successfully enabled HTTPS on https://mysite1.com, https://www.mysite1.com, https://dev.mysite1.com, and https://test.mysite1.com
      We were unable to subscribe you the EFF mailing list because your e-mail address appears to be invalid. You can try again later by visiting https://act.eff.org.
      
      - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
      If you like Certbot, please consider supporting our work by:
       * Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
       * Donating to EFF:                    https://eff.org/donate-le
      - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -




**IMPORTANT:** In the above command (for SSL certificate creation) you CANNOT use a wild card domain like -d *.mysite1.com. You have to specify all the subdomains that you want to secure. If you try using a wilc card domain you will get an error.

**NOTE:** The certificate is valid for 90 days. Certbot should automatically renew it. Pay attention to the text above that says:

    These files will be updated when the certificate renews.
    Certbot has set up a scheduled task to automatically renew this certificate in the background.

**NOTE:** If you now open the Server block config files for the domains we created an SSL certificate for, you will see that certbot has added a configuration code for SSL certificate. You can check this by opening the file with nano editor like this:

    sudo nano /etc/nginx/sites-available/mysite1.com

- You should see something like this:

      server {
      
          root /var/www/mysite1.com/htdocs;
      
          index index.html index.htm;
      
          server_name mysite1.com www.mysite1.com;
      
          location / {
              try_files $uri $uri/ =404;
          }
      
          listen [::]:443 ssl ipv6only=on; # managed by Certbot
          listen 443 ssl; # managed by Certbot
          ssl_certificate /etc/letsencrypt/live/mysite1.com/fullchain.pem; # managed by Certbot
          ssl_certificate_key /etc/letsencrypt/live/mysite1.com/privkey.pem; # managed by Certbot
          include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
          ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot
      }
      server {
          if ($host = www.mysite1.com) {
              return 301 https://$host$request_uri;
          } # managed by Certbot
      
      
          if ($host = mysite1.com) {
              return 301 https://$host$request_uri;
          } # managed by Certbot
      
      
          listen 80;
          listen [::]:80;
      
          server_name mysite1.com www.mysite1.com;
          return 404; # managed by Certbot
      }

#### 8.1 Navigate to Website URL
 
- Open a browser

- Type in your main domain name: mysite1.com
  - You should now see the content of your index.html file in your browser

        Welcome to mysite1.com!

  - **NOTE:** You should also see the lock icon (or similar) in the address bar indicating that your site is secure. 

- Repeat the same for subdomain dev.mysite1.com 
    - You should see
    
          Welcome to dev.mysite1.com!

- Repeat the same for subdomain test.mysite1.com
    - You should see

          Welcome to test.mysite1.com!





## Web Server - Apache

-Ref: [Ubuntu - Install and Configure Apache](https://ubuntu.com/tutorials/install-and-configure-apache)

Why do we need Apache server when we already have Nginx web server ?

The answer is: Because there are a lot of useful web based administartion tools that are built to work with Apache server. For example:
- pgAdmin4 - web interface for administration of PostgreSQL database is built to work with Apache server
- Webmin - a web-based interface for system administration for Unix is built to work with Apache server

However, because we have Nginx running on default port 80 (same as Apache's default port), we cannot run Apache server on the same port. 

Furthermore, because we already use Nginx to receive all the incoming requests from internet, we will use Apache just as a local server and forward all the requests that require Apache server to it. For example if we want to use Webmin application that runs on Apache server on port 10000, we will create Nginx sever block that listens on port 10000 and redirects all the requests to Apache server running on localhost:10000.

What we nwe will do next is:
1. Install Apache
2. Change default port 80 that Apache listens on to 8080 - because Nginx is already listening on port 80
3. Proxy Nginx to Apache - so that when we call a specific url that requires Apache server, Nginx will listen for that url and redirect the request to Apache server locally.


### Install Apache

      sudo apt update
      sudo apt install apache2

### Change Default Port

By default Apache listens on the same port as Nginx. We need to change the port so they don't clash.

- Open ports.conf - Change default Apache port 80 to 8080

      sudo nano /etc/apache2/ports.conf

  - Look for **Listen 80** and change it to **Listen 8080**

- Edit default virtual host file

      sudo nano /etc/apache2/sites-available/000-default.conf

  - Look for 

        <VirtualHost *:80>
  - Change to

        <VirtualHost *:8080>

- Check Apache Status

       sudo service apache2 status

  - If not started then start it

        sudo service apache2 start

- Restart Apache

      systemctl restart apache2

  - You will be prompeted to enter your password

- Check Apache Status

      sudo service apache2 status




### Verify Apache is working

- Verify that Apache is now listening on 8080

      sudo netstat -tlpn

  - You should see a list of tcp process listening on different ports. You should see Apache listening on port 8080, like:

        tcp6       0      0 :::8080                 :::*                    LISTEN      595571/apache2

- Verify default page working in browser. Enter your server IP in browser address bar e.g. 123.234.345.45 
- You should see now apche default page titled: **Apache2 Default Page**

### Nginx Proxy to Apache

Because Apache is only listening on localhost as we have not exposed port 8080 in our firewall, we will still receive all the outside requests on port 80 which will be handled by Nginx.

We can then proxy those requests to the requested resource on Apache.

For example we will later install pgAdmin4 web interface for administration of PostgreSQL database. The installation is configured to work on Apache Server at url http://localhost/pgadmin4 

If we call this from outside address like : http://123.234.345.45/pgadmin4 it will run Nginx default Server block which will not be able to resolve the address and therefore it will show page **404 Not Found**

To fix this we can implement a proxy inside default server block that redirects to Apache on localhost:8080 like:
 
    location /pgadmin4 {
            # Redirect to Apache Web Server we configured to listen on port 8080
            proxy_pass http://localhost:8080/pgadmin4;
    }

For full implementation of above example check [pgAdmin Installation](#install-pgadmin)






# >>>>>>>>>>>>>> PROOF-READ <<<<<<<<<<<<<<<<

#### 8.2 Auto Renew Certificate

You can skip this section. It is no longer required because the certbot program has already set up a scheduled task to automatically renew the certificate in the background.

<hr />
The certificate issued by letsencrypt is valid for 90 days.

We need to set up auto renewal so that our certificate does not expire.

- Create renewal shell script inside /opt/renewSSLCerts.sh

      sudo nano /opt/renewSSLCerts.sh

- Paste this code inside renewSSLCerts.sh

      #!/bin/sh
      # This script renews all the Let's Encrypt certificates with a validity < 30 days
      if ! letsencrypt renew > /var/log/letsencrypt/renew.log 2>&1 ; then
          echo Automated renewal failed:
          cat /var/log/letsencrypt/renew.log
          exit 1
      fi
      nginx -t && nginx -s reload

- Save and exit: **CTRL + S**, **CTRL + X**

- Give own and execute permissions to root user on file renewSSLCerts.sh

      sudo chown root.root /opt/renewSSLCerts.sh
      sudo chmod u+x /opt/renewSSLCerts.sh

- Add a cron job for automatic weekly execution of our SSL renewal script

      sudo crontab -u root -e

    - **IMPORTANT:**  We are adding the job to root user's crontab (not our current user's crontab) because the renewal script needs root privileges to run and also the log file is written to /var/log/letsencrypt/renew.log which is a directory that only root user can write to.

  - You may get this if it's a first time you are running crontab

        no crontab for root - using an empty one

        Select an editor.  To change later, run 'select-editor'.
          1. /bin/nano        <---- easiest
          2. /usr/bin/vim.basic
          3. /usr/bin/vim.tiny
          4. /bin/ed

        Choose 1-4 [1]:

  - Just type 1 to select nano as your crontab editor and hit Enter
  - The cron tasks file will then open in nano

- Add the following line at the end of the cron file

      @weekly /opt/renewSSLCerts.sh

- Save and exit: **CTRL + S**, **CTRL + X**

To learn more about Cron jobs, please read our book **Linux Cron Jobs**.

#### 8.3 Update Main Site Server Block

- Refs: 
  - [Nginx - Configuring HTTPS servers](https://nginx.org/en/docs/http/configuring_https_servers.html)
  - [Nginx - Configuring resolver](https://nginx.org/en/docs/http/ngx_http_core_module.html#resolver)

- In your terminal connected to remote server - Open your sites's configuration file which should be **/etc/nginx/sites-available/{{YOUR_DOMAIN_NAME}}** e.g. mysite1.com

      sudo nano /etc/nginx/sites-available/mysite1.com

- Delete all the text inside the file 
  - Hit **ALT + A** - to start selecting
  - Hit **Page Down** key repeatedly or press and hold **Arrow Down** key until you reach the end of the file
  - Hit **CTRL + K** to cut out the selected text
 
- On your local computer - Open text editor like Notepad and paste the following text inside it
       
      server {

              # Listen for http requests
              listen          80;
              listen          [::]:80;

              # Listen for https requests
              listen          443 ssl;
              listen          [::]:443 ssl;

              # Standard config
              root /var/www/mysite1.com/htdocs;
              index index.html index.htm;
              server_name mysite1.com www.mysite1.com;

              location / {
                      # First attempt to serve request as file, then
                      # as directory, then fall back to displaying a 404.
                      try_files $uri $uri/ =404;
              }

              # deny access to .htaccess files, if Apache's document root
              # concurs with nginx's one
              location ~ /\.ht {
                     deny all;
              }

              # If a user tries to come through http, redirect them through https
              if ($scheme != "https") {
                      return 301 https://$host$request_uri;
              }

              # SSL config
              ssl_certificate     /etc/letsencrypt/live/mysite1.com/fullchain.pem;
              ssl_certificate_key /etc/letsencrypt/live/mysite1.com/privkey.pem;
              ssl_protocols       TLSv1 TLSv1.1 TLSv1.2;
              ssl_ciphers         HIGH:!aNULL:!MD5:EECDH+AES128:RSA+AES128:EECDH+AES256:RSA+AES256:EECDH+3DES:RSA+3DES;
              ssl_prefer_server_ciphers On;
              ssl_stapling on;
              ssl_stapling_verify on;

              # Your favorite resolver may be used instead of the Google one below
              resolver 8.8.8.8;

              # HTTPS server optimization
              keepalive_timeout   70;

              # Logging
              access_log /var/log/nginx/mysite1.com_access.log;
              error_log /var/log/nginx/mysite1.com_error.log;

              # Security Headers
              add_header Strict-Transport-Security "max-age=31557600; includeSubDomains";
              add_header X-Content-Type-Options "nosniff" always;
              add_header X-Frame-Options "SAMEORIGIN" always;
              add_header X-Xss-Protection "1";
              add_header Content-Security-Policy "default-src 'self'; font-src *;img-src * data:; script-src *; style-src *";
      }


- **Replace** all ocurrences of the word **mysite1.com** with **your own domain name**.

- **Copy-Paste** the edited text into configuration file you have opened in the terminal conected to your remote server.

- Save and exit: **CTRL + S**, **CTRL + X**


**NOTE:** In the header **add_header Content-Security-Policy ...** - the config that we have provided, **does not allow** for **inline styles and scripts** in order to prevent malicious script injection. To find more about this setting and how to configure it for your needs, read the following articles: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy and here: https://content-security-policy.com/ ... 



- Add optimization configuration to **nginx.conf**
    - Open nginx.conf

          sudo nano /etc/nginx/nginx.conf

    - Add the following lines above and below **"http {"**. It should look like this

          # Automatically adjust the number of Nginx worker processes based on available cores.
          worker_processes auto;

          http {

              # Optimize SSL handshake for a multi-core system with 10 megabyte shared session cache, and increase cache timeout from default 5 minutes to 10.
              ssl_session_cache   shared:SSL:10m;
              ssl_session_timeout 10m;

**NOTE:** **worker_processes auto;** may already be written inside your nginx.conf. Search for it : CTRL + W , type "worker_processes auto;" , hit Enter. If it's already there, then don't add it again.

- Explanation of the **optimization** parameters:

  - **worker_processes auto;** - Nginx is an event-based web server. This means that instead of creating a new process for each simultaneous connection (as Apache does), Nginx uses a **master process** and **worker processes**. The **master process** reads and evaluates configuration, and **worker processes** do the actual processing. Since Nginx is event-based, it is able to handle multiple connections in a single thread. This means that the number of worker processes should be equal to the number of CPU cores. The **auto** parameter allows Nginx to automatically detect the number of CPU cores available and set the number of worker processes accordingly.
SSL operations consume extra CPU resources. On multi-processor systems several [worker processes](https://nginx.org/en/docs/ngx_core_module.html#worker_processes) should be run, no less than the number of available CPU cores. This is why we set **worker_process auto;** - to automatically adjust the number of Nginx worker processes based on available cores.


  - **ssl_session_cache   shared:SSL:10m;  ssl_session_timeout 10m;** -
The most CPU-intensive operation is the SSL handshake. There are two ways to minimize the number of these operations per client:
  1. Enabling [keepalive](https://nginx.org/en/docs/http/ngx_http_core_module.html#keepalive_timeout) connections to send several requests via one connection and the second is to reuse SSL session parameters to avoid SSL handshakes for parallel and subsequent connections. We have added this configuration inside our site configuration file above : **keepalive_timeout   70;**
  2. Increasing session cache - The sessions are stored in an SSL session cache shared between workers and configured by the [ssl_session_cache](https://nginx.org/en/docs/http/ngx_http_ssl_module.html#ssl_session_cache) directive. One megabyte of the cache contains about 4000 sessions. The default cache timeout is 5 minutes. It can be increased by using the [ssl_session_timeout](https://nginx.org/en/docs/http/ngx_http_ssl_module.html#ssl_session_timeout) directive.   Configuration that we implemented, is optimized for a multi-core system with 10 megabyte shared session cache, and session timeout is increased from default 5 minutes to 10. We have added this configuration inside nginx.conf above : **ssl_session_cache   shared:SSL:10m;  ssl_session_timeout 10m;**


- Save and exit nginx.conf: **CTRL + S**, **CTRL + X**

- Verify config and reload nginx server

      sudo nginx -t && \
      sudo nginx -s reload     


- Open your website in the browser - type the domain name into address bar and hit Enter e.g. mysite1.com

  - **IMPORTANT:** You should always open the site inside **Incognito/Private window** of the browser when testing if the new configuration has been applied. Otherwise you may get old cached version of the site.

  - To open **Incognito/Private window** these are the keyboard shortcuts for different browsers:

      |         |                     |
      | ------- | ------------------- |
      | Chrome  | CTRL + SHIFT + N    |
      | Firefox | CTRL + SHIFT + P    |
      | Edge    | CTRL + SHIFT + P    |
      | Safari  | COMMAND + SHIFT + N |
      | Brave   | CTRL + SHIFT + N    |

- You should see the page we configured on our server that says : **Welcome to mysite1.com!**

#### 8.4 Update Default Site Server Block

- Create index.html page for default site

      nano /var/www/default/index.html

- Paste the following text inside index.html
      
	  <h2 style="text-align:center;margin-top:3rem;">
              404 : The page you are trying to access does not exist.
        </h2>

  - You can also add your website branding into this page as well
  - This page will be displayed every time someone tries to access a subdomain that does not exist on your server

- Open default server block

      sudo nano /etc/nginx/sites-available/default

- Delete all the text inside the file: **ALT + A**, **PAGE-DOWN**, **CTRL + K**

- Paste the following configuration

      server {

              listen 80 default_server;
              listen [::]:80 default_server;

              listen 443 ssl default_server;
              listen [::]:443 ssl default_server;

              root /var/www/default;

              index index.html index.htm;

              server_name _;

              location / {
                      # First attempt to serve request as file, then
                      # as directory, then fall back to displaying a 404.
                      try_files $uri $uri/ =404;
              }

              # SSL Certificate
              ssl_certificate     /etc/letsencrypt/live/mysite1.com/fullchain.pem;
              ssl_certificate_key /etc/letsencrypt/live/mysite1.com/privkey.pem;
      }

  - We added listen directives for 443 ssl - to handle https requests
  - We added ssl_certificate and ssl_certificate_key directives to point to our wildcard certificate

- Save and exit: **CTRL + S**, **CTRL + X**
- Verify nginx configuration and restart nginx server

      sudo nginx -t && \
      sudo nginx -s reload

- Open browser and type in subdomain that does not exist for example: **something.mysite1.com**

  - You should see the page that says "404 : The page you are trying to access does not exist."


#### 8.5 Test your SSL

- Test your ssl at https://www.ssllabs.com/ssltest/

  - Type your domain name e.g. mysite1.com and hit Enter
      
  - You should get **A+** rating for both IPv4 and IPv6

- It should look like this

|     | Server                                                | Test time                                                | Grade |
| --- | ----------------------------------------------------- | -------------------------------------------------------- | ----- |
| 1   | 123.234.345.45 <br> mysite1.com <br> Ready            | Wed, 04 Oct 2023 03:52:27 UTC <br> Duration: 146.87 sec  | A+    |
| 2   | 12ab:1234:12:cd56:0:0:0:7 <br> mysite1.com <br> Ready | Wed, 04 Oct 2023 03:54:53 UTC <br> Duration: 147.929 sec | A+    |


**NOTE:** You may get a warning message **"Warning: Inconsistent server configuration"** and under your IPv6 instead of Ready it will say **Unable to connect**, while your IPv4 will look good and have a Grade A+.

This happens because in our domain name server DNS management panel we only set up one record for AAAA with Name @ and IPv6 address as a value, while for the IPv4 we added 3 A records with names @, * and www and IPv4 address as a value.

To solve the problem :

- Log into your DNS Management panel
- Add AAAA record with Name field containing **"www"** and Value/Data field containing your IPv6 address
- Add AAAA record with Name field containing **"*"** and Value/Data field containing your IPv6 address

Your DNS records should look like this now:

| Type | Name | Data                      | TTL  |
| ---- | ---- | ------------------------- | ---- |
| A    | @    | 123.234.345.45            | Auto |
| A    | www  | 123.234.345.45            | Auto |
| A    | *    | 123.234.345.45            | Auto |
| AAAA | @    | 12ab:1234:12:cd56:0:0:0:7 | Auto |
| AAAA | www  | 12ab:1234:12:cd56:0:0:0:7 | Auto |
| AAAA | *    | 12ab:1234:12:cd56:0:0:0:7 | Auto |

- Wait one hour for DNS propagation

- Test your ssl at https://www.ssllabs.com/ssltest/ again

- This time you should get **A+** rating for both IPv4 and IPv6


### 9. Set Up Subdomains


There are many reasons why you would want to have a subdomain. 

Our specific scenario is that we want to have a subdomain for each environment where we will deploy our application. 

In serious software development we normally have 3 environments: **development, testing and production**. We want to have a subdomain for development and testing environments, whereas production environment will be on our main domain name.

| **Environment** | **Domain URL**   |
| --------------- | ---------------- |
| Development     | dev.mysite1.com  |
| Testing         | test.mysite1.com |
| Production      | mysite1.com      |

**NOTE:** Test environment is also referred to as **UAT** (user acceptance testing) environment or **Staging** environment. These are all different names for the same concept.

We will create subdomains:

- **dev.mysite1.com** - This domain will have the latest copy of our website that has been only tested on developer's local machine. The dev environment website will be used by our developers to do some more developer testing.

- **test.mysite1.com** - This domain will have the latest copy of our website that has been already tested on development environment by developers. The test environment website will be used by our quality assurance testers to do **UAT** (user acceptance testing). 

**NOTE:** Our DNS is already configured to point all subdomains to our server IP address. So we don't need to do anything in our domain name host. Its the A record with wildcard asterisk symbol * that we have configured in DNS setup dashboard. This asterisk symbol * means that all subdomains will point to our server IP address. You can revise this in the section [DNS Records explained](#dns-records-explained)


#### Set up Subdomain for Dev Environment

- Create directory for subdomain

      mkdir -p /var/www/dev.mysite1.com/htdocs

- Create page index.html for subdomain

      echo "Welcome to dev.mysite1.com!" > /var/www/dev.mysite1.com/htdocs/index.html

- Create server block for subdomain - by coping the server block for main domain

      sudo cp /etc/nginx/sites-available/mysite1.com /etc/nginx/sites-available/dev.mysite1.com

- Open server block for edit

      sudo nano /etc/nginx/sites-available/dev.mysite1.com

- Search for all occurences of **mysite1.com** and replace with **dev.mysite1.com**, except for the SSL config part. **NOTE:** DO NOT replace wildcard certificate with specific certificate for dev.mysite1.com. We will use the wildcard certificate for all subdomains. SSL Config should look like this:

      # SSL config
      ssl_certificate     /etc/letsencrypt/live/mysite1.com/fullchain.pem;
      ssl_certificate_key /etc/letsencrypt/live/mysite1.com/privkey.pem;


- Save and exit: **CTRL + S**, **CTRL + X**

- Create symbolic link for subdomain

      sudo ln -s /etc/nginx/sites-available/dev.mysite1.com /etc/nginx/sites-enabled/

- Verify config and reload nginx
      
      sudo nginx -t && sudo nginx -s reload

- Open your subdomain in browser e.g. dev.mysite1.com

  - You should see the page that says "Welcome to dev.mysite1.com!" 
  - Url should be https://dev.mysite1.com as the server block configuration has automatically redirected http to https

#### Set up Subdomain for Test Environment


- Create directory for subdomain

      mkdir -p /var/www/test.mysite1.com/htdocs

- Create page index.html for subdomain

      echo "Welcome to test.mysite1.com!" > /var/www/test.mysite1.com/htdocs/index.html

- Create server block for subdomain - by coping the server block for main domain

      sudo cp /etc/nginx/sites-available/mysite1.com /etc/nginx/sites-available/test.mysite1.com

- Open server block for edit

      sudo nano /etc/nginx/sites-available/test.mysite1.com

- Search for all occurences of **mysite1.com** and replace with **test.mysite1.com**, except for the SSL config part. **NOTE:** DO NOT replace wildcard certificate with specific certificate for test.mysite1.com. We will use the wildcard certificate for all subdomains. SSL Config should look like this:

      # SSL config
      ssl_certificate     /etc/letsencrypt/live/mysite1.com/fullchain.pem;
      ssl_certificate_key /etc/letsencrypt/live/mysite1.com/privkey.pem;


- Save and exit: **CTRL + S**, **CTRL + X**

- Create symbolic link for subdomain

      sudo ln -s /etc/nginx/sites-available/test.mysite1.com /etc/nginx/sites-enabled/

- Verify config and reload nginx
      
      sudo nginx -t && sudo nginx -s reload

- Open your subdomain in browser e.g. test.mysite1.com

  - You should see the page that says "Welcome to test.mysite1.com!" 
  - Url should be https://test.mysite1.com as the server block configuration has automatically redirected http to https


#### Warning - WWW and Subdomains

**IMPORTANT:** If you try including "www." before your subdomain in the url you put in address bar (e.g. **www.test.mysite1.com**), the browser will show you something like:

      Your connection is not private
      Attackers might be trying to steal your information from www.test.mysite1.com (for example, passwords, messages, or credit cards). Learn more
      NET::ERR_CERT_COMMON_NAME_INVALID

This happens because our wildcard certificate covers only one level.

In other words **a wildcard (*) will only match a single label in a domain and you can only have one wildcard as the very left leftmost label in a domain name**.

Therefore if our wildcard certificate was created for *.mysite1.com, then it will match mysite1.com and test.mysite1.com but it will not match www.test.mysite1.com.

**NOTE:** In our specific scenario - using subdomains for dev and test environments - we do not require www. subdomain because these subdomains will not be used by the public. They will be used only by our developers and testers. Therefore we will not set up www. subdomain for our dev and test environments.

However, if you want to allow ssl for www.test.mysite1.com you can do that by creating a new **specific certificate** for www.test.mysite1.com using the process in section [Set up Specific SSL Certificate](#set-up-specific-ssl-certificate).

- Create SSL certificate for **www.test.mysite1.com**

      sudo letsencrypt certonly 
        -a webroot \
        --webroot-path=/var/www/test.mysite1.com/htdocs/ \
        -d test.mysite1.com \
        -d www.test.mysite1.com \

You will also have to change your server block configuration for **test.mysite1.com** to use the new certificate for **"test.mysite1.com"** instead of the wildcard certificate for **"\*.mysite1.com"**.

- Open server block for edit

      sudo nano /etc/nginx/sites-available/test.mysite1.com

- Change SSL config to use the new certificate for **"test.mysite1.com"** instead of the wildcard certificate for **"\*.mysite1.com"**

      # SSL config
      ssl_certificate     /etc/letsencrypt/live/test.mysite1.com/fullchain.pem;
      ssl_certificate_key /etc/letsencrypt/live/test.mysite1.com/privkey.pem;



### Optional - Use Cloudflare.com as Proxy to point Domain Name

If you do not want to host your own certificate but you still want to use free SSL certificate, then you can use Cloudflare.com as a proxy to point your domain name to your server.

If this is your preferred option continue reading, otherwise skip this section.

Easiest way to point your domain name to your hosting, get the free SSL certificate and have your domain url automatically display as safe https:// in every browser, is to use Cloudflare.com as proxy.

- Open account with Cloudflare.com

- Go to Dashboard > Click **Add site** at the top menu > Enter your domain name e.g. mysite2.com > Select **Free plan** > Click **Continue** > Keep clicking continue until wizard finishes

- Click **DNS** on the left menu. You should arrive to DNS page and see :
  - **DNS Records** table with 3 rows
  - **Cloudflare Nameserves** table with 2 nameservers
    - annabel.ns.cloudflare.com
    - dean.ns.cloudflare.com


- Configure DNS Records tables.
  - Delete all the existing rows. In Actions column: Click **Edit** > Click **Delete**
  - Add 3 new records by clicking on button **+ Add Record**
    - First record > Type: A, Name: *, Content: {{SERVER_IP}}
    - Second record > Type: A, Name: {{SITE_DOMAIN}}, Content: {{SERVER_IP}}
    - Third record > Type: A, Name: www, Content: {{SERVER_IP}}
  - Your DNS records table should now look like this

    | Type | Name        | Content        | Proxy status | TTL  | Actions |
    | ---- | ----------- | -------------- | ------------ | ---- | ------- |
    | A    | *           | 123.234.345.45 | Proxied      | Auto | Edit    |
    | A    | mysite2.com | 123.234.345.45 | Proxied      | Auto | Edit    |
    | A    | www         | 123.234.345.45 | Proxied      | Auto | Edit    |
    |      |             |                |              |      |         |

- Point your domain name host to Cloudflare
  - Log into your domain name host where you registered your domain mysite2.com , e.g. Godaddy.com
  - Go inside your domain name management page and look for **Manage DNS** link. It may be called something different on your domain name provider but it should have word DNS or Domain Name Servers. 
  - **Change Nameservers**. There should be a section Nameservers which allows you to change name servers. Click button to change nameservers and replace them with Cloudflare nameservers you obtained on Cloudflaire.com e.g. annabel.ns.cloudflare.com, dean.ns.cloudflare.com

- Wait to receive email from Cloudflare
  - Cloudflare will keep trying to connect to your domain name host and check that it points to Cloudflare nameservers.
  - Once that change has synced you will receive the email from Cloudflare to let you know that your domain name now points to Cloudflare
  
- Verify that domain name has propagated
  - Type your domain name e.g. mysite2.com into any browser 
  - It should display your web page that says "Welcome to mysite2.com!"
  - The url should have automatically redirect to https:// version of domain e.g. https://mysite2.com and you should see a secure url icon like padlock on the left of your url in browser address bar.


