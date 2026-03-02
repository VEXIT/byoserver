# Linux - Mail Server - Postfix, Dovecot, Thunderbird & Roundcube


|            |                                                                 |
| ---------- | --------------------------------------------------------------- |
| Copyright: | © 2022 VEXIT , Tomorow is today ® , www.vexit.com               |
| Author:    | Vex Tatarevic                                                   |
| Date:      | 2022-10-18                                                      |
|            |                                                                 |


 

## Overview

In this book I will explain how to set up a mail server on a Linux machine or VPS. 

On the server we will use:
- **Postfix** as the Mail Transfer Agent (MTA)
- **Dovecot** as the Mail Delivery Agent (MDA). 

Then we will explore three different types of client interfaces to access the mail server.
We will use:
- **Thunderbird** as the desktop email client 
- **Edison Mail** as the mobile email client
- **Roundcube** as the webmail client

We will learn how to programatically send emails using .Net.

We will also set up email authentication using **SPF, DKIM and DMARC** protocols to ensure best chances for our emails to reach the recipient's inbox.

We will set up **TLS** encryption for secure email communication.

We will learn how to use Brevo as a reliable fallback email service to relay through it our legitimate emails that are bounced back by strict email providers like Microsoft Outlook, which block whole domain ranges if they were misused by spammers in the past. 


## Example Data Used

|             |                      |
| ----------- | -------------------- |
| Domain name | yourdomain.com       |
| Username    | admin                |
| Server IPv4 | 123.234.345.45       |
| Server IPv6 | 12ab:1234:12:cd56::7 |
|             |                      |



## Prepare Server

### 1. Find Host Provider that Unblocks Port 25 Outbound

- In order to run our own SMTP server we need to have either a Virtual Private Server **(VPS) or a Dedicated Server**. We cannot use a shared hosting account because shared hosting providers do not allow you to run your own SMTP server.

- **The most important question** when choosing a hosting provider is: **Do they unblock port 25 outbound for you ?** Outbound port 25 is required for sending emails from your server. If they don't unblock port 25 outbound, then you cannot host your own mail server with them.

In order to host your own email server, you have to find the VPS Hosting Provider that will let you use port 25 outbound.

The problem is that big VPS hosting providers are abused by spammers who set up mail servers to send a lot of spam emails, phishing emails etc.

Therefore a lot of VPS server providers block the port 25 which is required for running the mail server.

But some of the providers **will unblock the port 25 if you ask them** and explain what you want to use your email server for.

As of time of writing this course, the following VPS providers unblock port 25 outbound:

- **Vultr.com** - they unblock port 25 outbound if you ask them
- **Hostinger.com** - they unblock port 25 outbound by default

I have personally used both of these providers and they are both great and very afordable.

### 2. Register Domain Name

In order for your mail to reach the target server, you must have a registered domain name.

- Go to any domain name provider and register a domain name you can use for your server.

For the purpose of this course we will use the domain name **yourdomain.com**. Replace this with your own domain name in all the commands.

We will use **GoDaddy.com** as an example domain name provider.



### 3. Change Your Hostname to Domain Name

After you have purchased your domain name and and server hosting, you need to change your server hostname to your domain name.

> **FQDN (Fully Qualified Domain Name)** - is the complete domain name for a host, which is your physical computer or VPS (virtual private server) on the internet. For example **yourdomain.com** is a FQDN of your host.

**NOTE:** In most other tutorials they will teach you to set up your mail server FQDN to mail.yourdomain.com. However we will set our FQDN to yourdomain.com (without prefix mail). This is because we will be using the same server to run web server, mail server, database server and everything else we need, **to do it in the cheapest way possible**. If you are using a separate server machine or VPS for mail server, then you can set your FQDN to mail.yourdomain.com.

**NOTE:** The reason that FQDN of mail server must be the same as your host server's FQDN is because most mail servers to which your server sends emails, will reject your emails if your mail server FQDN does not match your machine/VPS server's FQDN.

- Check current host name

      hostname -f

- Set hostname to your domain name yourdomain.com

      sudo hostnamectl set-hostname yourdomain.com

- Make sure /etc/hostname file is changed (hostname replaced with domain name yourdomain.com)

      sudo nano /etc/hostname

- Make sure /etc/hosts file is changed (hostname replaced with domain name yourdomain.com)

      sudo nano /etc/hosts

- Reboot server

      sudo reboot

- Verify FQDN (Fully Qualified Domain Name)

      hostname --fqdn

  - It should say **yourdomain.com**

- Verify hostname status

      hostnamectl status

- Verify DNS domain name. This actually prints out **TLD (top-level domain)**  - everything that follows the final dot of a domain name e.g. com, org, net ... 

      dnsdomainname

  - In our case for yourdomain.com this will print out: **com**





### 4. Set DNS Records

We will add the following DNS records
    
|          |                                                                                               |
| -------- | --------------------------------------------------------------------------------------------- |
| **MX**   | tells other MTAs that your mail server is responsible for email delivery for your domain name |
| **A**    | maps your domain name to your server's IPv4 address                                           |
| **AAAA** | maps your domain name to your server's IPv6 address                                           |

- **Get IPv4 and IPv6** addresses of your server - in your server terminal run:

      hostname -I

  - You should see something like
      
        127.0.0.1 123.234.345.45 12ab:1234:12:cd56::7

  - These are your {{LOCALHOST}} {{IPv4}} {{IPv6}} addresses separated by spaces

- **Get FQDN** (Fully Qualified Domain Name) of your server - in your server terminal run:

      hostname --fqdn

  - You should see something like

        yourdomain.com

- Go to your domain name provider website e.g. GoDaddy.com
- Go inside management panel for your domain name
- You should see a link **DNS**, that takes you to control panel for DNS records

The following steps should have already been completed by you in the previous course about NGINX server setup, in the section about setting up DNS records. If you have not done that yet, please do it now.

- **Add 3 A records**
  - Click **Add New Record** button
  - Select **A** from the dropdown
  - Add 3 A records. Enter **IPv4** address inside Value field of each record. It should look like this:

      | Type | Name | Value          | TTL         |
      | ---- | ---- | -------------- | ----------- |
      | A    | @    | 123.234.345.45 | 600 seconds |
      | A    | *    | 123.234.345.45 | 600 seconds |
      | A    | www  | 123.234.345.45 | 600 seconds |

- **Add 1 AAAA record**
  - Click **Add New Record** button
  - Select **AAAA** from the dropdown
  - Enter **IPv6** address inside Value field of the record. It should look like this:

      | Type | Name | Value                | TTL         |
      | ---- | ---- | -------------------- | ----------- |
      | AAAA | @    | 12ab:1234:12:cd56::7 | 600 seconds |

- **Add 1 MX record**
  - Click **Add New Record** button
  - Select **MX** from the dropdown
  - Enter **FQDN** inside Value field of the record. It should look like this:

      | Type | Name | Priority | Value          | TTL    |
      | ---- | ---- | -------- | -------------- | ------ |
      | MX   | @    | 0        | yourdomain.com | 1 Hour |
 

- If you are using Cloudflare.com proxy server, your DNS records should look like this

| Type | Name | Content              | Priority | Proxy status | TTL  |
| ---- | ---- | -------------------- | -------- | ------------ | ---- |
| A    | *    | 123.234.345.45       |          | Proxied      | Auto |
| A    | @    | 123.234.345.45       |          | Proxied      | Auto |
| A    | www  | 123.234.345.45       |          | Proxied      | Auto |
| AAAA | @    | 12ab:1234:12:cd56::7 |          | DNS only     | Auto |
| MX   | @    | yourdomain.com       | 0        | DNS only     | Auto |
|      |      |                      |          |              |      |


>IMPORTANT: If you are using Cloudflare DNS service, do not enable the Proxy feature when creating AAAA record. Cloudflare does not support SMTP or IMAP proxy. In column **Proxy status** switch off "**Proxied**" so it says DNS only.



### 5. Set PTR Record / Reverse DNS

<definition>

> Reverse DNS, rDNS or PTR is a pointer record, that maps an IP address to an FQDN (Fully Qualified Domain Name)
</definition>

Major email providers such as Yahoo, Gmail, etc. use RDNS as part of their anti-spam measures to block emails.

You need to set **Reverse DNS** / **PTR Record** setting on your hosting provider control panel - it's called one of these 2 names on different servers.
We need to do this so that our emails have a better chance of landing in the recipient’s inbox instead of the spam folder.  

First we will check what the current PTR record is for your server IP address.

- Check the PTR record for an IP address

      dig -x {{IP}} +short

- OR

      host {{IP}}

  - It may say something like

        123.234.345.45.hosting-provider.com.

  - This is a placeholder PTR record that your hosting provider sets by default. But we want to change the PTR so that the output of above command shows your domain name like this:

        yourdomain.com.

- Go to your web host control panel
- Select your VPS server instance and go to **Settings**
- Look for **IP Address** or **IPv4** and **IPv6** 
- You should see a button or a link **Reverse DNS** or **PTR Record** next to each IPv4 and IPv6 address
- Click on it and enter your domain name e.g. **yourdomain.com** and save the changes 
  
Here are examples of how to access this setting in 2 popular hosting providers Vultr.com and Hostinger.com:

Example 1 - Vultr.com:

  - In **Vultr.com** control panel go to **Server Instance > Settings**
  
    - **IPv4** - edit field ReverseDNS and set it to your domain e.g. **yourdomain.com**
    - **IPv6** - add **Reverse DNS** record using your existing IPv6 address as value for IP field and your site's domain name (e.g. yourdomain.com) as value for a Reverse DNS field 
  
Example 2 - Hostinger.com:

  - in **Hostinger.com** control panel click on: **Manage** (on your server instance) **> Settings > IP Address** 
    - You will see **IPv4** and **IPv6** records with a button  **Set PTR record** next to it.
    - Click on **Set PTR record** button next to each IP record and enter your domain name e.g. **yourdomain.com**

The table will look something like this in Hostinger.com

| Type | IP address           | Reverse DNS    | Action         |        |
| ---- | -------------------- | -------------- | -------------- | ------ |
| IPv4 | 123.234.345.45       | yourdomain.com | Set PTR record | Delete |
| IPv6 | 12ab:1234:12:cd56::7 | yourdomain.com | Set PTR record | Delete |

In above table you just need to click on **Set PTR record** button next to each IP address and enter your domain name e.g. **yourdomain.com** and save the changes.
  
If you cannot find the PTR setting in your VPS control panel, contact your VPS provider and ask them to set the PTR record for you, or to explain where you can change it yourself.

## 1. MTA - Postfix

<definition>

> **Postfix** - is a message transport agent (MTA), aka SMTP server, which serves two purposes
>- transporting email messages from a mail client/mail user agent (MUA) to a remote SMTP server.
>- accept emails from other SMTP servers.
</definition>

### Install Postfix

- Run install command

      sudo apt install postfix

  - This should install postfix and then open up a purple or blue screen **Postfix Configuration** or **Package configuration**

- **Postfix Configuration** home screen (NOTE: Depending on the version of the installer you may not even see this screen and be sent automatically to next screen)
  - Hit **Tab** key to select **< Ok >** , then hit **Enter** to go to next screen

- **General mail configuration type** screen - You will see the list of configuration types with **Internet Site** as a selected option:

    |                           |
    | ------------------------- |
    | - No configuration        |
    | v/ Internet Site          |
    | - Internet with smarthost |
    | - Satellite system        |
    | - Local only              |
    | **< Ok > < Cancel >**     |

  - Select **Internet Site** from above options using arrow up/down to select the option. 
  - Use Tab to go to **< Ok >**
  - Hit **Enter** to go to next screen

- **System mail name** screen - will be prefilled with server name placeholder like `vps.server.local]\`
  - Replace the placeholder with your mail domain name e.g. **yourdomain.com** . This is called **FQDN** - Fully Qualified Domain Name
  - Hit Enter
  - You should see configuration running in the shell and then the purple screen will appear again
  
- **Which services should be restarted?** screen (You may not see this screen depending on the version of the installer) - will have your default web service selected e.g. nginx.service . Hit Ok
  - The configuration will then complete

### Uninstall Postfix - if you made a mistake

Skip this section if your installation was correct and successful.

If you made a mistake while installing Postfix, like you accidentally typed in the wrong domain name, or your cat jumped on your keyboard and pressed Enter - in that case you should completely uninstall Postfix and then install it again.

- Uninstall postfix

      sudo apt remove postfix && sudo apt purge postfix

Even after running the purge command, some manually edited configuration files may still remain on your system. These are typically located in the /etc/postfix directory. 

- Remove manually edited configuration files

      sudo rm -rf /etc/postfix

- Auto remove any dependencies that are no longer required

      sudo apt autoremove

- Install postfix again - Go to [Install Postfix](#install-postfix) section above

### Read Postfix Config

- Postfix service will be automatically started and config file will be generated. Check the config file.

      sudo nano /etc/postfix/main.cf

- You can also read config without opening the file by using this command

      postconf -n


### Verify Postfix Installation


- Check that service is running
 
      sudo systemctl status postfix

- Check Postfix version

      postconf mail_version

- Postfix ships with many binaries under the /usr/sbin/ directory. To see what they are type this command

      dpkg -L postfix | grep /usr/sbin/

- Check with ss (Socket Statistics) that Postfix master process is listening on TCP port 25 (standard SMTP port)

      sudo ss -lnpt | grep master

- Or use netstat to verify that Postfix is listening on port 25:

      netstat -ant

- Open TCP port 25 in Firewall

      sudo ufw allow 25/tcp

- Verify locally it is open

      sudo ufw status

  - You should see below rows 

        To                         Action      From
        --                         ------      ----
        ...
        25/tcp                     ALLOW       Anywhere
        ...
        25/tcp (v6)                ALLOW       Anywhere (v6)

- Verify from internet that your port is open. 
  - Go to one of these websites:
    - [yougetsignal.com/tools/open-ports](https://www.yougetsignal.com/tools/open-ports/)
    - [ipvoid.com/port-scan](https://www.ipvoid.com/port-scan/)
  - Enter your server IP address and port 25
  - Click button **Check**
  - You should get a result saying that port 25 is open

### Open Port 25 for outbound connections

The outbound TCP port 25 needs to be open, so Postfix can send emails to other SMTP servers. The outbound TCP port 25 is controlled by your hosting provider, we can install the telnet utility to check if it’s open or blocked.

- Install Telnet

      sudo apt install telnet

- Send outbound telnet request to gmail on port 25

      telnet gmail-smtp-in.l.google.com 25

  - If port 25 (outbound) is blocked, you will see something like:
  
         telnet gmail-smtp-in.l.google.com 25
        Trying 2607:f8b0:400c:c02::1b...
        Trying 74.125.28.27...
        telnet: Unable to connect to remote host: Connection timed out

  - If port 25 is blocked for outgoing requests contact your ISP (Internet Service Provider) and ask them to unblock it. If they refuse, then find another ISP that will provide this feature for you.
  - If port 25 is not blocked for outbound requests you will see something like:

    
        Trying 2607:f8b0:400c:c11::1b...
        Connected to gmail-smtp-in.l.google.com.
        Escape character is '^]'.
        220 mx.google.com ESMTP u12-20020a1f2e0c000000b003b3c744be47si1521687vku.95 - gsmtp

  - To escape the telnet session, type **quit** and hit **Enter**
  - If that doesn't work, then hit **CTRL + ]** hit **Enter**, then type **quit** and hit **Enter**

### Set Postfix Hostname

- **Get FQDN** (Fully Qualified Domain Name)

      hostname --fqdn

- Open Postfix config

      sudo nano /etc/postfix/main.cf

- Set myhostname parameter in Postfix config file to your FQDN e.g. yourdomain.com

      myhostname = yourdomain.com

- Restart Postfix

      sudo systemctl restart postfix

**IMPORTANT:** Please note that **myhostname** in /etc/postfix/main.cf, **MX record** Value field in DNS, and **PTR record** in your server IP pannel must all match. If they don't match, your emails will be rejected by some mail servers.

### Create Email Alias

- Create an email alias for the root user

      sudo nano /etc/aliases

  - You should see

        # /etc/aliases
        mailer-daemon: postmaster
        postmaster: root
        nobody: root
        hostmaster: root
        usenet: root
        news: root
        webmaster: root
        www: root
        ftp: root
        abuse: root
        noc: root
        security: root

The left-hand side is the alias name. The right-hand side is the final destination of the email message. So emails for postmaster@yourdomain.com will be delivered to root@yourdomain.com. The postmaster email address is required by RFC 2142.

However the root user's mailbox is unattended because we never use the root user for email. So we will create an alias for the root user which forwards all emails to the admin user's mailbox.


- Add the following line at the end of the file

      root: admin

- Save and close the file: **CTRL + S**, **CTRL + X**

- Apply the changes

      sudo newaliases



### Check Inbox

- Send email from your outside email account to your server email account admin@yourdomain.com


The inbox for each user is located at /var/spool/mail/{{username}} or /var/mail/{{username}} file. 

- Check location of the the inbox

      postconf mail_spool_directory

- Open your mail file and read it. For example if your username is **admin** :

      sudo nano /var/mail/admin

  - NOTE: replace admin with your username

- Scroll to the bottom of the log to see the response for your last unsuccessfull email

### Read Email

- To see a list of emails for current user

      mail

- To see a list of emails for a specific user

      sudo mail -u {{username}}

- To read a specific email from the list, enter the number of the email and hit Enter
- To exit the email list, hit **q** and hit Enter ... OR ... hit **CTRL + D**

### Delete Email

There are many ways to delete an email. Here are some of them:

- Delete email while inside it

  - Show list: `mail`
  - Open email: Enter the number of the email and hit Enter
  - Delete email: **D + Enter** 
    - NOTE: this will not give any confirmation message
  - Exit email list : **CTRL + D**

- Delete multiple emails from the list

  - Show list: `mail`
  - Enter **d** followed by list numbers of the emails you wish to delete. For example: `d 1 2 3 4 5`

- Delete range of emails from the list

  - Show list: `mail`
  - Enter **d** followed by range of numbers of the emails you wish to delete. For example: `d 1-5`

- Delete all emails from the list

  - Show list: `mail`
  - Delete all: `d*`


### Send Test Email

- sendmail binary is located at /usr/sbin/sendmail

- Use sendmail to send test email to your Hotmail, Gmail or other commercial email account

      echo "test email" | sendmail my-account@gmail.com


>NOTE: You should test with GMAIL and HOTMAIL address as recipient because they have the strictest rules. If your email can be received by Gmail and Hotmail, then it should be able to be received by all other email providers.

Most likely sending email at this point will not succeed or you will be able to send it once and the next time after that it will not go through. This is because we haven't finished setting up the mail server authentication yet.



### Check Log

If the email you sent still did not reach your Gmail address, then you need to check the mail.log to understand the reason why.

- Open the mail log

      sudo nano /var/log/mail.log 

- Scroll to the bottom of the log to see the response for your last unsuccessfull email

- If your email has not come through, you will see something like

      Nov 23 20:57:56 mysite postfix/pickup[31501]: 7A1BDFE8A4: uid=1001 from=<admin>
      Nov 23 20:57:56 mysite postfix/cleanup[31752]: 7A1BDFE8A4: message-id=<20221123205756.7A1BDFE8A4@yourdomain.com>
      Nov 23 20:57:56 mysite postfix/qmgr[1540]: 7A1BDFE8A4: from=<admin@yourdomain.com>, size=250, nrcpt=1 (queue active)
      Nov 23 20:57:56 mysite postfix/smtp[31754]: 7A1BDFE8A4: to=<my_other_email@gmail.com>, relay=gmail-smtp-in.l.google.com[2607:f8b0:400c:c02::1a]:25, delay=0.44, delays=0.01/0.01/0.14/0.27, dsn=5.7.1, status=bounced (host gmail-smtp-in.l.google.com[2607:f8b0:400c:c02::1a] sa>
      Nov 23 20:57:56 mysite postfix/cleanup[31752]: E5418FE91D: message-id=<20221123205756.E5418FE91D@yourdomain.com>
      Nov 23 20:57:56 mysite postfix/bounce[31755]: 7A1BDFE8A4: sender non-delivery notification: E5418FE91D
      Nov 23 20:57:56 mysite postfix/qmgr[1540]: E5418FE91D: from=<>, size=2914, nrcpt=1 (queue active)
      Nov 23 20:57:56 mysite postfix/qmgr[1540]: 7A1BDFE8A4: removed
      Nov 23 20:57:56 mysite postfix/local[31756]: E5418FE91D: to=<admin@yourdomain.com>, relay=local, delay=0.01, delays=0/0.01/0/0, dsn=2.0.0, status=sent (delivered to mailbox)
      Nov 23 20:57:56 mysite postfix/qmgr[1540]: E5418FE91D: removed

- To read about details of message delivery failure, you need to open your inbox and read the message returned from Gmail

More details about the log file can be found in the next section [Mail Log](#mail-log).


## Mail Log

### Logs Location

There are 2 main email log files: **mail.log** and **mail.err**.


| Log Type       | Location              | Purpose                                                         |
| -------------- | --------------------- | --------------------------------------------------------------- |
| Mail Log       | **/var/log/mail.log** | logs send and receive operations by the mail server.            |
| Mail Error Log | **/var/log/mail.err** | logs errors that occur during the operation of the mail server. |


When you are just starting out with Postfix, you will be checking the mail log file a lot. It is a good idea to learn how to read the mail log file.

- Open the mail log file

      sudo nano /var/log/mail.log

It can get very big so the beginner way to deal with it is to just delete all the text inside the file and save it. This way you will have a clean log file to start with. Then you can send an email and check the log file to see what happened.

### Delete Mail Log

To delete all text inside the file there are 2 ways:

- Inside the file - open file, select all text and delete it:

  |           |                               |                           |
  | --------- | ----------------------------- | ------------------------- |
  | Open file | `sudo nano /var/log/mail.log` |                           |
  | Set mark  | CTRL + SHIFT + 6              |                           |
  | Page Down | FN + ARROW_DOWN               | (On Windows PageDown key) |
  | Delete    | CTRL + K                      |                           |
  | Save      | CTRL + S                      |                           |
  | Exit      | CTRL + X                      |                           |



- Outside the file - use the truncate command to delete all text inside the file without opening it:

      sudo truncate -s 0 /var/log/mail.log

### Watch Mail Log

- Open a **second terminal** window and connect to your server

      ssh yourdomain.com

- Watch mail log **in real time**
      
      sudo tail -f /var/log/mail.log

  - To exit: **CTRL + C**

- In your **first/main terminal** window - Send a test email

      echo "test email" | sendmail my-account@gmail.com

- In your **second terminal** window - watch the logs being printed in real time. You will see the log of the email being sent

From this point on every time you are sending an email **you should have the mail log open in second terminal window** so you can see what is happening with your email.


## Email Authentication

If you want your emails to reach Gmail recipients, you must set up full MTA email authentication using 3 protocols SPF, DKIM and DMARC - to help prevent spoofing and phishing.

>**SPF** (Sender Policy Framework) - Specifies the servers and domains that are authorized to send email on behalf of your organization.

>**DKIM** (DomainKeys Identified Mail) - Adds a digital signature to every outgoing message, which lets receiving servers verify the message actually came from your organization.

>**DMARC** (Domain-based Message Authentication, Reporting and Conformance) - is an email authentication protocol which lets you tell receiving servers what to do with outgoing messages from your organization that don’t pass SPF or DKIM.

If you are using Cloudflare you can just use a wizard on the DNS page to set up SPF and DMARC easily.


### 1. SPF

Ref: https://www.linuxbabe.com/mail-server/setting-up-dkim-and-spf

> **SPF** (Sender Policy Framework) is an email authentication protocol and part of email cybersecurity used to stop phishing attacks. It allows your company to specify who is allowed to send email on behalf of your domain. This is useful because in a typical phishing attack, the threat actor spoofs the sender address to look like an official business account or someone the victim may know.


#### Outgoing Emails Auth

- In your DNS management panel, add a TXT record with the following value for spf record

      v=spf1 ip4:{{IPv4}} ip6:{{IPv6}} -all

  - Replace {{IPv4}} and {{IPv6}} with your server's IPv4 and IPv6 addresses

  - It should look like this

  | Type | Name | Value                                                   | TTL  |
  | ---- | ---- | ------------------------------------------------------- | ---- |
  | TXT  | @    | v=spf1 ip4:123.234.345.45 ip6:12ab:1234:12:cd56::7 ~all | Auto |

**NOTE:** You could also use spf record without ip like `v=spf1 mx ~all`, where mx means all hosts listed in the MX records. But the approach with ip is more secure.




SPF Record explained:
|            |                                                                                                                                                                                                                                          |
| ---------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **TXT**    | means the record is a text record                                                                                                                                                                                                        |
| **@**      | means the root domain name e.g. yourdomain.com                                                                                                                                                                                           |
| **v=spf1** | indicates this is an SPF record and the SPF record version is SPF1.                                                                                                                                                                      |
| **mx**     | means all hosts listed in the MX records are allowed to send emails for your domain and all other hosts are disallowed.                                                                                                                  |
| **~all**   | indicates that emails from your domain should only come from hosts specified in the SPF record. Emails that are from other hosts will be flagged as untrustworthy. Possible alternatives are +all, -all, ?all, but they are rarely used. |


- Verify SPF record on your Linux server using the dig utility

      dig yourdomain.com txt

  - You should see something like this

        ; <<>> DiG 9.16.1-Ubuntu <<>> yourdomain.com txt
        ;; global options: +cmd
        ;; Got answer:
        ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 53567
        ;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

        ;; OPT PSEUDOSECTION:
        ; EDNS: version: 0, flags:; udp: 65494
        ;; QUESTION SECTION:
        ;yourdomain.com.                    IN      TXT

        ;; ANSWER SECTION:
        yourdomain.com.             3600    IN      TXT     "v=spf1 mx ~all"

        ;; Query time: 0 msec
        ;; SERVER:

- Verify SPF record on your Linux server using the host utility

      host -t txt yourdomain.com

  - You should see something like this

        yourdomain.com descriptive text "v=spf1 mx ~all"

- Verify SPF record on one or both of these websites: 
  - https://mxtoolbox.com/spf.aspx
  - https://www.uriports.com/tools?method=spf



#### Incoming Emails Auth

Set up Postfix SMTP server to check for SPF record of incoming emails. This helps with detecting forged incoming emails.

- Install the postfix-policyd-spf-python package

      sudo apt install postfix-policyd-spf-python

- Open Postfix master.cf file

      sudo nano /etc/postfix/master.cf

- Add the following lines at the end of the file

      # Start SPF policy daemon when starting postfix service
      policyd-spf  unix  -       n       n       -       0       spawn
            user=policyd-spf argv=/usr/bin/policyd-spf

- Save and close the file: **CTRL + S**, **CTRL + X**

- Edit Postfix main.cf file

      sudo nano /etc/postfix/main.cf

- Add the following lines at the end of the file

      # SPF policy daemon timeout
      policyd-spf_time_limit = 3600s

      # SPF - Impose a restriction on incoming emails by rejecting unauthorized email and checking SPF record
      smtpd_recipient_restrictions = 
                permit_mynetworks
                permit_sasl_authenticated
                reject_unauth_destination
                check_policy_service unix:private/policyd-spf

- Save and close the file: **CTRL + S**, **CTRL + X**

- Restart Postfix

      sudo systemctl restart postfix

From now on if you receive an email from a domain that has an SPF record, you can see the SPF check results in the raw email header. The following header indicates the sender sent the email from an authorized host.

      Received-SPF: Pass (sender SPF authorized).

- Test SPF at https://www.learndmarc.com - ONLY complete the first step of the test, which is the SPF test. The other steps are for DKIM and DMARC which we will set up later.

#### Troubleshooting

**PROBLEM:** If your have changed your domain name for example from your-domain.io to yourdomain.com, when you send email the from header will still show your-domain.io.

If you open Postfix configuration file `sudo nano /etc/postfix/main.cf` look for the line `myorigin = /etc/mailname` . This line tells Postfix to use the domain name in the file `/etc/mailname` as the domain name of the sender for outgoing emails.


**SOLUTION**:  

- Update the mailname file: `sudo nano /etc/mailname` - change the domain name to new domain name e.g. `yourdomain.com`.
 
- Restart Postfix `sudo systemctl restart postfix`.
  - This time if you test email sending, the from header will show yourdomain.com. You can check it with https://www.learndmarc.com

Theoretically if you ommit the `myorigin` line from the Postfix configuration file, Postfix will use hostname as the domain name of the sender for outgoing emails.
More information at the [Postfix documentation](http://www.postfix.org/postconf.5.html#myorigin).




### 2. DKIM - THE MOST IMPORTANT STEP

This is the most important step if you want your mail to reach other servers.

> DKIM (DomainKeys Identified Mail) is an email authentication protocol that allows recipients to verify that an email message truly came from the domain that it claims to have come from. Because spam often contains counterfeit headers, this type of authentication is required. DKIM uses public-key cryptography to enable senders to electronically sign valid emails in a way that recipients can verify. DKIM also protects mail from tampering, providing nearly end-to-end integrity from the signature to the validating Mail Transfer Agent (MTA).

- Ref: 
  - [Implementing DKIM](https://dmarcly.com/blog/how-to-implement-dmarc-dkim-spf-to-stop-email-spoofing-phishing-the-definitive-guide#creating-a-dkim-record)
  - [DKIM Generator](https://dmarcly.com/tools/dkim-record-generator)
  - [Configure DKIM With Postfix](https://easydmarc.com/blog/how-to-configure-dkim-opendkim-with-postfix/)
  - [OpenDKIM - Official Docs](http://www.opendkim.org/docs.html)
 
 ##### Install OpenDKIM

    sudo apt install opendkim opendkim-tools

- Add postfix user to opendkim group

      sudo gpasswd -a postfix opendkim

- Edit DKIM configuration file

      sudo nano /etc/opendkim.conf

- At the time of writing this document, opendkim.conf that is generated with installation, looks like this:

      # This is a basic configuration for signing and verifying. It can easily be
      # adapted to suit a basic installation. See opendkim.conf(5) and
      # /usr/share/doc/opendkim/examples/opendkim.conf.sample for complete
      # documentation of available configuration parameters.
      
      Syslog                  yes
      SyslogSuccess           yes
      #LogWhy                 no
      
      # Common signing and verification parameters. In Debian, the "From" header is
      # oversigned, because it is often the identity key used by reputation systems
      # and thus somewhat security sensitive.
      Canonicalization        relaxed/simple
      #Mode                   sv
      #SubDomains             no
      OversignHeaders         From
      
      # Signing domain, selector, and key (required). For example, perform signing
      # for domain "example.com" with selector "2020" (2020._domainkey.example.com),
      # using the private key stored in /etc/dkimkeys/example.private. More granular
      # setup options can be found in /usr/share/doc/opendkim/README.opendkim.
      #Domain                 example.com
      #Selector               2020
      #KeyFile                /etc/dkimkeys/example.private
      
      # In Debian, opendkim runs as user "opendkim". A umask of 007 is required when
      # using a local socket with MTAs that access the socket as a non-privileged
      # user (for example, Postfix). You may need to add user "postfix" to group
      # "opendkim" in that case.
      UserID                  opendkim
      UMask                   007
      
      # Socket for the MTA connection (required). If the MTA is inside a chroot jail,
      # it must be ensured that the socket is accessible. In Debian, Postfix runs in
      # a chroot in /var/spool/postfix, therefore a Unix socket would have to be
      # configured as shown on the last line below.
      Socket                  local:/run/opendkim/opendkim.sock
      #Socket                 inet:8891@localhost
      #Socket                 inet:8891
      #Socket                 local:/var/spool/postfix/opendkim/opendkim.sock
      
      PidFile                 /run/opendkim/opendkim.pid
      
      # Hosts for which to sign rather than verify, default is 127.0.0.1. See the
      # OPERATION section of opendkim(8) for more information.
      #InternalHosts          192.168.0.0/16, 10.0.0.0/8, 172.16.0.0/12
      
      # The trust anchor enables DNSSEC. In Debian, the trust anchor file is provided
      # by the package dns-root-data.
      TrustAnchorFile         /usr/share/dns/root.key
      #Nameservers            127.0.0.1

##### Change Config File

- Edit DKIM configuration file

      sudo nano /etc/opendkim.conf

- Optionally configure more detail logging by uncommenting the following line

      # LogWhy                 yes

- Activate Signing and Verification mode - in **opendkim.conf** file
  
   - Search for line starting with **"Mode"**. Use **CTRL + W** to search.
  
   - Make sure that Mode is uncommented and says **"Mode sv"**
  
     - If Mode is set to **"v"**, change it to **"sv"** 
     - v - means **verification** mode - verifies the DKIM signatures of receiving email messages. 
     - sv - activates the **signing** mode for outgoing emails.

- Uncomment the following lines

      Canonicalization       relaxed/simple
      Mode                   sv
      SubDomains             no

- Add the following lines after **"OversignHeaders    From"**

      AutoRestart         yes
      AutoRestartRate     10/1M
      Background          yes
      DNSTimeout          5
      SignatureAlgorithm  rsa-sha256

- Add the following lines at the end of the config file. Note: KeyFile is already inside config and commented out. It should stay commented out because we are using KeyTable instead.

      # Gives the location of a private key to be used for signing ALL messages. 
      # This directive is ignored if KeyTable is enabled.
      # KeyFile                /etc/dkimkeys/example.private

      # Gives the location of  a file that has mappings of key names to signing keys.
      # Map domains in From addresses to keys used to sign messages.
      # In simple terms this tells OpenDKIM where to find your keys.
      # If present, it overrides the KeyFile setting in configuration file.
      # Requires SigningTable to be enabled.
      KeyTable                  refile:/etc/opendkim/key.table

      # Defines a table used to select one or more signatures to apply to a message 
      # based on the address found in the From: header field.
      # In simple terms, this tells OpenDKIM how to use your keys.
      # Requires KeyTable to be enabled
      SigningTable              refile:/etc/opendkim/signing.table

      # Identifies a set of external hosts that may send mail through the server 
      # as one of the signing domains without credentials as such.
      # Hosts to ignore when verifying signatures.
      ExternalIgnoreList        /etc/opendkim/trusted.hosts
      
      # Identifies a set of internal hosts whose mail should be signed rather than verified.
      InternalHosts             /etc/opendkim/trusted.hosts

- Save and close the file: **CTRL + S**, **CTRL + X**


##### Create file structure for OpenDKIM

- Create a directory structure for OpenDKIM

      sudo mkdir -p /etc/opendkim/keys

- Change the owner from root to opendkim and make sure only opendkim user can read and write to the keys directory.

      sudo chown -R opendkim:opendkim /etc/opendkim

      sudo chmod go-rw /etc/opendkim/keys

##### Create Signing Table


    sudo nano /etc/opendkim/signing.table

- Add these lines to the file. First line tells OpenDKIM that if a sender on your server is using a @yourdomain.com address, then it should be signed with the private key identified by default._domainkey.yourdomain.com. The second line tells that your sub-domains will be signed by the private key as well. **_Replace yourdomain.com with your domain name_**

      *@yourdomain.com    default._domainkey.yourdomain.com
      *@*.yourdomain.com    default._domainkey.yourdomain.com

- Save and close the file: **CTRL + S**, **CTRL + X**


##### Create Key Table

    sudo nano /etc/opendkim/key.table

- Add the following line, which tells the location of the private key. **_Replace yourdomain.com with your domain name_**

      default._domainkey.yourdomain.com     yourdomain.com:default:/etc/opendkim/keys/yourdomain.com/default.private


##### Create Trusted Hosts File

    sudo nano /etc/opendkim/trusted.hosts

- Add the following lines to the newly created file. This tells OpenDKIM that if an email is coming from localhost or from the same domain, then OpenDKIM should not perform DKIM verification on the email. **_Replace yourdomain.com with your domain name_**

      127.0.0.1
      localhost

      .yourdomain.com

**Note:** You should not add an asterisk in the domain name like this: *.yourdomain.com. There should be only a dot before the domain name. 


##### Generate Private/Public Keypair

DKIM is used to sign outgoing messages and verify incoming messages, we need to generate a private key for signing and a public key for remote verifier. Public key will be published in DNS.


- Create dir for the domain. **_Replace yourdomain.com with your domain name_**

      sudo mkdir /etc/opendkim/keys/yourdomain.com


- Generate keys using opendkim-genkey tool. **_Replace yourdomain.com with your domain name_**

      sudo opendkim-genkey -b 2048 -d yourdomain.com -D /etc/opendkim/keys/yourdomain.com -s default -v

   - This will create 2048 bits keys. 
   - -d (domain) specifies the domain. 
   - -D (directory) specifies the directory where the keys will be stored 
   - -s (selector), also known as the name - we make it "default" 
   - The private key is written to default.private file 
   - The public key is written to default.txt file

- Assign user **opendkim** as the owner of the private key.  **_Replace yourdomain.com with your domain name_**

      sudo chown opendkim:opendkim /etc/opendkim/keys/yourdomain.com/default.private

- Change private key file permissions - only **opendkim** user can read and write to the file.  **_Replace yourdomain.com with your domain name_**

      sudo chmod 600 /etc/opendkim/keys/yourdomain.com/default.private

##### Publish Public Key in DNS Records

- Display the public key. **_Replace yourdomain.com with your domain name_**

      sudo cat /etc/opendkim/keys/yourdomain.com/default.txt

  - This will output something like this:

        default._domainkey IN TXT ( "v=DKIM1; h=sha256; k=rsa; "
          "p=MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQC2z1...  ) ; ----- DKIM key default for yourdomain.com

  - The string after the **p=** is the public key

- Copy the text inside the brackets **()**. It should look like 

      ( "v=DKIM1; h=sha256; k=rsa; "
          "p=MIG...AwIf"
          "M265Evcm...RqwIDAQAB" ) ;
          

- Paste it into Notepad or other text editor

- **Remove all spaces and double-quotes** from the copied text

- Go to your DNS management panel

- Add TXT record in your DNS Management Panel:

    | Type    | Name               | Value                                                   | TTL  |
    | ------- | ------------------ | ------------------------------------------------------- | ---- |
    | **TXT** | default._domainkey | v=DKIM1;h=sha256;k=rsa;p=MIG...AwIfM265Evcm...RqwIDAQAB | Auto |
 
- **NOTE**: **yourselector** in our case is called **default**. This is the name we have given to the key and how we named its containing file (e.g. /etc/opendkim/keys/yourdomain.com/**default.txt** )

##### Test DKIM Key Locally

- Enter the following command. **_Replace yourdomain.com with your domain name_**
    
      sudo opendkim-testkey -d yourdomain.com -s default -vvv

  - You should see **key OK** if everything is ok

        opendkim-testkey: using default configfile /etc/opendkim.conf
        opendkim-testkey: checking key 'default._domainkey.yourdomain.com'
        opendkim-testkey: key secure
        opendkim-testkey: key OK

  - If you see **"key not secure"** this is because DNSSEC isn’t enabled on your domain name. DNSSEC is a security standard for secure DNS query - it uses digital signatures to verify the origin of your DNS across the internet and add extra security to your domains. Most domain names don't have DNSSEC enabled. You **don't need to worry about this**. But if you want you can turn it on in your domain name provider website. For example this is how you do it on GoDaddy:
    - Go inside **DNS** Management panel
    - Click on link/tab called **DNSSEC**
    - Click on button **Turn On DNSSEC** - this will popup a window asking for your email where to send the key
    - Enter your email and click **Save**. 
    - You will receive an email with the DNSSEC key.
    - It may take up to 90 minutes to be reflected in your account
    - Ref: https://au.godaddy.com/help/turn-dnssec-on-or-off-6420

  - If you see the **"query timed out"** error, you need to comment out the following line in /etc/opendkim.conf file and restart opendkim.service.

        TrustAnchorFile       /usr/share/dns/root.key


- **NOTE**: DKIM record may need sometime to propagate to the Internet. Depending on the domain registrar you use, your DNS record might be propagated instantly, or it might take up to 24 hours.

##### Test DKIM using online tool

We need to verify that DKIM is properly set-up on your domain name servers

- Go to one of these websites for DKIM check:

  - [EasyDMARC DKIM Lookup](https://easydmarc.com/tools/dkim-lookup)
  - [DMARCAnalyzer DKIM Record Checker](https://www.dmarcanalyzer.com/dkim/dkim-checker/)

- Fill out DKIM check form
  - Domain: yourdomain.com
  - Selector: **default**
- Click **Check DKIM**
  
##### Connect Postfix to OpenDKIM

Ref: [Tech Head UK - Setup DKM with Postfix on Ubuntu](https://tech-head.uk/setup-dkim-with-postfix-on-ubuntu-server/)

Postfix can talk to OpenDKIM via a Unix socket file. The default socket file used by OpenDKIM is /var/run/opendkim/opendkim.sock, as shown in /etc/opendkim.conf file. But the postfix SMTP daemon shipped with Ubuntu runs in a chroot jail, which means the SMTP daemon resolves all filenames relative to the Postfix queue directory (/var/spool/postfix). So we need to change the OpenDKIM Unix socket file.

- Create a directory to hold the OpenDKIM socket file and allow only opendkim user and postfix group to access it

      sudo mkdir /var/spool/postfix/opendkim

      sudo chown opendkim:postfix /var/spool/postfix/opendkim

- Edit **opendkim.conf**

      sudo nano /etc/opendkim.conf

    - Find the following line

          Socket    local:/run/opendkim/opendkim.sock

    - Replace with

          Socket    local:/var/spool/postfix/opendkim/opendkim.sock

- Save and close the file: **CTRL + S**, **CTRL + X**


- Edit **default opendkim** file

      sudo nano /etc/default/opendkim

    - Find the following line

          SOCKET=local:$RUNDIR/opendkim.sock

    - Replace with

          SOCKET="local:/var/spool/postfix/opendkim/opendkim.sock"

- Save and close the file: **CTRL + S**, **CTRL + X**


- Edit Postfix **main.cf** file

      sudo nano /etc/postfix/main.cf

  - Add the following lines at the end of this file, so Postfix will be able to call OpenDKIM via the milter protocol

        # DKIM - Milter configuration
        milter_default_action = accept
        milter_protocol = 6
        smtpd_milters = local:opendkim/opendkim.sock
        non_smtpd_milters = $smtpd_milters

- **Restart** opendkim and postfix service

      sudo systemctl restart opendkim postfix

  - If the opendkim and postfix were started successfully, above command will not produce any output. However check the status of both services to make sure they are running properly

        sudo systemctl status opendkim postfix

- **If you got error** when running **restart** command above

      Job for opendkim.service failed because the control process exited with error code.
      See "systemctl status opendkim.service" and "journalctl -xeu opendkim.service" for details.

  THEN

  - Check the journal of opendkim for errors

        sudo journalctl -xeu opendkim.service

      Most likely you will see something like this

        opendkim[1234]: /etc/opendkim.conf: configuration error at line 1: unrecognized parameter

      which means you have a typo in your opendkim.conf file. So open the file and fix the error at the line number specified in the error message in the journal.


Now you should be able to send emails to Gmail, Hotmail and other commercial email providers without them being blocked automatically by recepient's email provider. 

**NOTE:** You may have to wait **24 hours** before your DNS Server records get propagated, and Gmail starts accepting emails from your server.

##### Test Email Sending with LEARNDMARC.COM

- Go to https://www.learndmarc.com/
- You should see a generated email address like **ld-f056d23dc9@learndmarc.com** and instructions asking you to send test email to that address
- Send an email from your server terminal like

      {
         echo From: admin@yourdomain.com
         echo To: ld-f056d23dc9@learndmarc.com
         echo Subject: Testing email server
         echo 
         echo This is the test message
      } | sendmail -t

  - NOTE: From field should be `{{YOUR_USERNAME}}@{{YOUR_DOMAINNAME}}` . We use `admin` user in above example

- The learndmarc page will start printing out in real time on the screen, a report about your Email authentication strength. It will give you tips on what needs to be fixed/improved.

##### Test Email Sending to Gmail

- Send email to your Gmail account with the command below (**_replace "From:" and "To:" email addresses with your own_**)
      
      {
         echo From: admin@yourdomain.com
         echo To: my-email@gmail.com
         echo Subject: Testing email server
         echo 
         echo This is the test message
      } | sendmail -t

- If you have not received the email check your server inbox for a reply from Gmail that explains the reason why

      sudo nano /var/mail/admin

- If you have made a mistake in your SPF or DKIM set up above, you may get something like

      <my-email@gmail.com>: host
          gmail-smtp-in.l.google.com[12ab:1234:12:cd56::7a] said: 550-5.7.26 This
          mail is unauthenticated, which poses a security risk to the 550-5.7.26
          sender and Gmail users, and has been blocked. The sender must 550-5.7.26
          authenticate with at least one of SPF or DKIM. For this message, 550-5.7.26
          DKIM checks did not pass and SPF check for [yourdomain.com] did not pass
          550-5.7.26 with ip: [12ab:1234:12:cd56::7]. The sender should visit
          550-5.7.26  https://support.google.com/mail/answer/81126#authentication for
          550 5.7.26 instructions on setting up authentication.
          h64-20020a25d043000000b00d74a212c036si12173616ybg.129 - gsmtp (in reply to
          end of DATA command)

### 3. DMARC

Ref: https://www.linuxbabe.com/mail-server/create-dmarc-record

>DMARC (Domain Message Authentication, Reporting & Conformance) - is an email authentication protocol, designed to give email domain owners the ability to protect their domain from unauthorized use, commonly known as email spoofing. It allows you to advertise to mail servers the policies of your domain. The policies are regarding mails that fail SPF or DKIM validations. It also allows you to request reports on failed messages from receiving mail servers.

**NOTE:** Before implementing a DMARC authentication, you must first implement [SPF](#1-spf) and [DKIM](#2-dkim---the-most-important-step) authetnication.

#### Create DMARC DNS Record

- Go to your DNS management panel 
- Add a TXT record with the following value

  | Type | Name   | Value                                                            | TTL  |
  | ---- | ------ | ---------------------------------------------------------------- | ---- |
  | TXT  | _dmarc | v=DMARC1; p=none; pct=100; fo=1; rua=mailto:dmarc@yourdomain.com | Auto |

  - **NOTE:** Replace dmarc@yourdomain.com with your real email address that is used to receive aggregate DMARC report.

DMARC settings explained:

|                                 |                                                                                                                           |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| **v=DMARC1**                    | The protocol version is DMARC1                                                                                            |
| **pct=100**                     | The percentage of emails from your domain DMARC applies to                                                                |
| **mailto:dmarc@yourdomain.com** | Reporting URI for aggregate report. The email address is used to tell receiving email servers where report should be sent |
| **p=none**                      | p means policy. We set value none for our domain. Read below for description of possible 3 values for policy .            |
| **fo=1**                        | setting that configures when the report should be sent. Read below for description of possible 4 values for fo setting.   |

- **p** setting - policy - can contain one of 3 possible values:

   |                |                                                                                                                                                                                                                                                                     |
   | -------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
   | **none**       | tells receiving email servers not to do anything special if DMARC check fails.                                                                                                                                                                                      |
   | **quarantine** | tells receiving email server to put the email into quarantine if DMARC check fails. It must be approved by an admin before it can reach the recipient’s inbox.                                                                                                      |
   | **reject**     | tells receiving email servers to reject the email if DMARC check fails.  Note that not all receiving email servers comply with the reject policy. Gmail and Yahoo Mail will reject the email, but Microsoft Mail (Outlook, Hotmail, Live) doesn’t reject the email. |
   
                                                                                        |
- **fo** setting - can contain one of 4 values

   |       |                                                                                                   |
   | ----- | ------------------------------------------------------------------------------------------------- |
   | **0** | generate reports if all underlying authentication mechanisms fail to produce a DMARC pass result. |
   | **1** | generate reports if any mechanisms fail.                                                          |
   | **d** | generate a report if DKIM signature failed verification.                                          |
   | **s** | generate a report if SPF failed                                                                   |

#### Check DMARC

- Check your DMARC record from Linux terminal
      
      dig txt +short _dmarc.yourdomain.com

  - You should get a response

        v=DMARC1; p=none; pct=100; fo=1; rua=mailto:dmarc@yourdomain.com
   

- Install opendmarc package

      sudo apt install opendmarc

- You will get a prompt `Configure database for opendmarc with dbconfig-common?`. Select **No** and hit Enter     

- Run opendmarc check command

      opendmarc-check yourdomain.com

  - You should get a result like

        DMARC record for yourdomain.com:
                Sample percentage: 100
                DKIM alignment: relaxed
                SPF alignment: relaxed
                Domain policy: none
                Subdomain policy: unspecified
                Aggregate report URIs:
                        mailto:dmarc@yourdomain.com
                Failure report URIs:
                        (none)

#### Test DMARC with MAIL-TESTER.COM

- Go to  https://www.mail-tester.com
- You should see:
  - a generated email address like test-cdx4xj4p2@srv1.mail-tester.com
  - a button **"Then check your score"**

- Leave the page open and go to your server terminal

- Send an email from your server terminal with a sendmail command  (**_replace "From:" email addresses with your own_**)

      {
         echo From: admin@yourdomain.com
         echo To: test-cdx4xj4p2@srv1.mail-tester.com
         echo Subject: Testing email server
         echo 
         echo This is the test message
      } | sendmail -t

- Go back to the mail-tester.com page and click on the button **"Then check your score"**

- You should get a score 10/10



## Send and Read Email with Mail program

- Refs: 
  - https://www.digitalocean.com/community/tutorials/send-email-linux-command-line
  - https://mailutils.org/manual/mailutils.html

- Install a command line MUA (mail user agent)

      sudo apt install mailutils

- Send email

      mail -a FROM:admin@yourdomain.com

  - You should see prompt **To:** . Type recipient email (e.g. john@gmail.com) and hit Enter
  - You should see prompt **CC:** . Type email to forward to or type nothing and hit Enter
  - You should see prompt **Subject:** . Type subject and hit Enter.
  - **After the Subject there will be NO PROMPT**. You just keep typing your message. When you are finished, hit Enter and then **CTRL + D** to send the message.

        To: john@gmail.com
        Cc: 
        Subject: 2nd test email
        I'm sending this email using the mail program.

- Read incoming emails

      mail

  - You will see a numbered list of emails. 
- Read unread email - type the number of the email you want to read and hit Enter. This will show youthe content of the email.
- **q** - to quit reading emails and go back to the prompt. 
- **h** - to see a list of commands you can use to manage your emails.
- Exit mail - type **q** and hit **Enter** to exit out of mail context.

## Enable SMTP Encryption

Ref: [Digital Ocean - Install and Configure Postfix as a Send-Only SMTP Server](https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-postfix-as-a-send-only-smtp-server-on-ubuntu-22-04#step-2-configuring-postfix)


You should have by now installed a valid SSL certificate on your server.

This was explained in the book **"Linux WebServer"**.

We used **Certbot** to install a free SSL certificate from **Let's Encrypt**.

Now we will configure Postfix to use the ssl certificate to encrypt the email content.

Your certificate and private key file were saved under **/etc/letsencrypt/live/{{YOUR_DOMAIN}}**. For example, if your domain name is **yourdomain.com**, then the certificate and private key files are located under **/etc/letsencrypt/live/yourdomain.com**.

- View the certificate files

      sudo ls -l /etc/letsencrypt/live/yourdomain.com

    - You should see 4 files:

          cert.pem
          chain.pem
          fullchain.pem
          privkey.pem

- Open Postfix main.cf file

      sudo nano /etc/postfix/main.cf

- Go to section **TLS parameters** and replace lines

      smtpd_tls_cert_file=/etc/ssl/certs/ssl-cert-snakeoil.pem
      smtpd_tls_key_file=/etc/ssl/private/ssl-cert-snakeoil.key
    
    with
    
      smtpd_tls_cert_file=/etc/letsencrypt/live/mysite.com/fullchain.pem
      smtpd_tls_key_file=/etc/letsencrypt/live/mysite.com/privkey.pem

- Save and close the file: **CTRL + S**, **CTRL + X**

- Restart Postfix

      sudo service postfix restart

- Send email to test SMTP encryption

      echo "This is the body of an encrypted email" | mail -s "This is the subject line" my-email@gmail.com

  - You should receive an email in your inbox and be able to read the content of the email.

## Microsoft IP Blacklist

### Problem

- Try sending email to one of your Microsoft email accounts (e.g. Hotmail, Outlook, Live, MSN) from your server terminal. 

      echo "This is the test message" | mail -aFrom:admin@yourdomain.com -s "Testing email server" your-email@hotmail.com

- Check mail

      mail

- You will most likely see this **Undelivered Mail** email in your inbox

      "/var/mail/admin": 1 message 1 new
      >N   1 Mail Delivery Syst Thu Sep 21 22:58  92/3517  Undelivered Mail   Returned to Sender
      ?

- Open the email above - type email number (1) and hit Enter

      ? 1

- You should see a bounce back email message:

      Return-Path: <>
      X-Original-To: admin@yourdomain.com
      Delivered-To: admin@yourdomain.com
      Received: by yourdomain.com (Postfix)
              id 12345ABCDE; Thu, 21 Sep 2023 22:58:43 +0000 (UTC)
      Date: Thu, 21 Sep 2023 22:58:43 +0000 (UTC)
      From: MAILER-DAEMON@yourdomain.com (Mail Delivery System)
      Subject: Undelivered Mail Returned to Sender
      To: admin@yourdomain.com
      Auto-Submitted: auto-replied
      MIME-Version: 1.0
      Content-Type: multipart/report; report-type=delivery-status;
              boundary="XXXXXXXXXX.1234567890/yourdomain.com"
      Content-Transfer-Encoding: 8bit
      Message-Id: <20230921225843.12345ABCDE@yourdomain.com>

      This is a MIME-encapsulated message.

      --XXXXXXXXXX.1234567890/yourdomain.com
      Content-Description: Notification
      Content-Type: text/plain; charset=utf-8
      Content-Transfer-Encoding: 8bit

      This is the mail system at host yourdomain.com.

      I'm sorry to have to inform you that your message could not
      be delivered to one or more recipients. It's attached below.

      For further assistance, please send mail to postmaster.

      If you do so, please include this problem report. You can
      delete your own text from the attached returned message.

                         The mail system

      <your-email@hotmail.com>: host hotmail-com.olc.protection.outlook.com[104.47.17.161]
          said: 550 5.7.1 Unfortunately, messages from [123.234.345.45] weren't sent.
          Please contact your Internet service provider since part of their network
          is on our block list (S3140). You can also refer your provider to
          http://mail.live.com/mail/troubleshooting.aspx#errors.
          [VI1EUR05FT055.eop-eur05.prod.protection.outlook.com
          2023-09-21T22:58:42.927Z 1234ABCD1234ABCD] (in reply to MAIL FROM command)

      --XXXXXXXXXX.1234567890/yourdomain.com
      Content-Description: Delivery report
      Content-Type: message/delivery-status

      Reporting-MTA: dns; yourdomain.com
      X-Postfix-Queue-ID: 4E945A00EE
      X-Postfix-Sender: rfc822; admin@yourdomain.com
      Arrival-Date: Thu, 21 Sep 2023 22:58:40 +0000 (UTC)

      Final-Recipient: rfc822; your-email@hotmail.com
      Original-Recipient: rfc822;your-email@hotmail.com
      Action: failed
      Status: 5.7.1
      Remote-MTA: dns; hotmail-com.olc.protection.outlook.com
      Diagnostic-Code: smtp; 550 5.7.1 Unfortunately, messages from [123.234.345.45]
          weren't sent. Please contact your Internet service provider since part of
          their network is on our block list (S3140). You can also refer your
          provider to http://mail.live.com/mail/troubleshooting.aspx#errors.
          [VI1EUR05FT055.eop-eur05.prod.protection.outlook.com
          2023-09-21T22:58:42.927Z 1234ABCD1234ABCD]

      --XXXXXXXXXX.1234567890/yourdomain.com
      Content-Description: Undelivered Message
      Content-Type: message/rfc822
      Content-Transfer-Encoding: 8bit

      Return-Path: <admin@yourdomain.com>
      --More--

- The important part of the message is this:

      <your-email@hotmail.com>: host hotmail-com.olc.protection.outlook.com[104.47.17.161]
          said: 550 5.7.1 Unfortunately, messages from [123.234.345.45] weren't sent.
          Please contact your Internet service provider since part of their network
          is on our block list (S3140). You can also refer your provider to
          http://mail.live.com/mail/troubleshooting.aspx#errors.
          [VI1EUR05FT055.eop-eur05.prod.protection.outlook.com
          2023-09-21T22:58:42.927Z 1234ABCD1234ABCD] (in reply to MAIL FROM command)

In summary your server will not be able to send emails to any of the Microsoft's email accounts (e.g. Hotmail, Outlook, Live, MSN) because **the whole IP range is blacklisted by Microsoft**.



### Solutions

1. Ask Microsoft nicely to ublock your IP - maybe it works, maybe it doesn't:
   - You can go to https://sendersupport.olc.protection.outlook.com/snds/index.aspx and follow the steps there

      OR
   - Submit a request to remove your IP from the blacklist at: https://olcsupport.office.com/

2. Get your mail server certified here : https://www.validity.com/everest/sender-certification/ . That will work but it will cost you a certain yearly fee.

3. Use a relay server to send emails from your server. You can use the relay only for microsoft emails and your own SMTP for all others. See [Using Relay Server](#using-relay-server) section below.

We will use the 3rd option - Relay Server.


## Relay Server

> Relay Server - is a server that accepts email from your server and forwards it to the recipient's email server.

In order to authenticate our server to the relay server we need to set up SASL authentication on our server.

> **SASL** Simple Authentication and Security Layer - is a framework for authentication and data security in Internet protocols. It decouples authentication mechanisms from application protocols.


### Create an Account on Relay Server

We will use Brevo.com as a relay server to send emails from our server to any email address that we cannot successfully send emails to from our SMTP server. 

Brevo - formerly known as Sendinblue - is email marketing platform that allows you to send up to 300 emails per day for free.

- Go to [Brevo.com](https://www.brevo.com/) and open account. 


### Get Relay Login

- Login to your Brevo account
- Go to **SMTP Relay** : On the left side menu, click **Transactional > Email > Settings > Configuration**
  - You should see 2 sections: HTTP API and SMTP RELAY
- Inside SMTP RELAY section, click **Get Your SMTP key**
- You should see

      SMTP Server       smtp-relay.brevo.com
      Port              587
      Login             your-email
      Password          your-password

### Install SASL

- You MUST install the libsasl2-modules package, in order to use SASL authentication with Postfix.

      sudo apt install libsasl2-modules


> SASL (Simple Authentication and Security Layer) - is a framework for authentication and data security in Internet protocols.




### Install Dovecot Core

- Refs:
  - [Postfix.org - SASL Dovecot Setup](https://www.postfix.org/SASL_README.html#server_dovecot)
  - [Dovecot.org - SASL Setup on Postfix](https://doc.dovecot.org/configuration_manual/howto/postfix_and_dovecot_sasl/)
  - [Ubuntu.com - Postfix Setup with Dovcot SASL](https://ubuntu.com/server/docs/mail-postfix)
  - [Redhat - Postfix with Dovecot SASL](https://doc.dovecot.org/configuration_manual/howto/postfix_and_dovecot_sasl/)


Postfix supports two SASL implementations: **Cyrus SASL** and **Dovecot SASL**.

We will use Dovecot SASL because at the later stage we have an option to install **Dovecot IMAP/POP3 server** to allow users to read their emails from their email clients (e.g. Thunderbird, Outlook, Apple Mail, etc.)

- Confirm that your Postfix version supports Dovecot SASL

      postconf -a

  - You should see **dovecot** in the list of supported SASL mechanisms

        cyrus
        dovecot

- Install dovecot-core package

      sudo apt install dovecot-core

- Edit dovecot configuration file 10-master.conf

      sudo nano /etc/dovecot/conf.d/10-master.conf

- Find the block of code starting with the comment **"# Postfix smtp-auth
"** and uncomment the lines inside it. The block should look like this after haveing been ucommented:
      
      # Postfix smtp-auth
      unix_listener /var/spool/postfix/private/auth {
        mode = 0660
        user = postfix
        group = postfix
      }

  - Ref: https://www.postfix.org/SASL_README.html#server_dovecot

- Save and close the file: **CTRL + S**, **CTRL + X**

- Edit dovecot configuration file 10-auth.conf

      sudo nano /etc/dovecot/conf.d/10-auth.conf

- Find the line **"auth_mechanisms = plain"** and change it to

      auth_mechanisms = plain login

- Save and close the file: **CTRL + S**, **CTRL + X**

- Show main Dovecot settings

      dovecot -n

### Configure SASL

We need to enable SASL in both Postfix SMTP Client and Server.

To better understand how Postfix handles incoming and outgoing mail we can looka at this [Postfix Architecture Overview](https://www.postfix.org/OVERVIEW.html)

- For our setup we need to understand in a nutshell that:
  - **smtpd** - is SMTP daemon process that handles incoming mail and routes it to the appropriate internal location - SMTP Server.
  - **smtp** - is SMTP daemon process that handles delivery of outgoing mail - SMTP Client
  - All the settings in postfix/main.cf related to **smtpd** will be prefixed with **smtpd_** and all the settings related to **smtp** will be prefixed with **smtp_**

- Edit **/etc/postfix/main.cf** file

      sudo nano /etc/postfix/main.cf

- **Set up SASL inbound config** - Add the following lines, related to **Postfix SMTP Server setup**, at the end of the file

      # SASL - Incoming / Server
      smtpd_sasl_auth_enable = yes
      smtpd_sasl_type = dovecot
      smtpd_sasl_path = private/auth
      smtpd_sasl_security_options = noanonymous, noplaintext
      smtpd_sasl_tls_security_options = noanonymous

  - Ref: https://www.postfix.org/SASL_README.html#server_sasl_enable
  - Ref: https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/security_guide/sect-security_guide-securing_postfix-configuring_postfix_to_use_sasl
  

- Search for the following line inside main.cf file

      smtpd_tls_security_level=may
      
  - It should already be in the file postfix/main.cf. If it is not in there, add it. This setting will allow both TLS and non-TLS connections to the server.

> TLS (Transport Layer Security) - is a cryptographic protocol that provides end-to-end security of data sent between applications over the Internet. It is mostly used for secure communication between web browsers and servers. However, it is also used for email communication.



- **Set up SASL outbound config** - Add the following lines, related to **Postfix SMTP Client setup**, at the end of the file

      # SASL - Outgoing / Client
      smtp_sasl_auth_enable = yes
      smtp_sasl_password_maps = hash:/etc/postfix/sasl_passwd
      smtp_sasl_security_options = noanonymous
      smtp_sasl_mechanism_filter = plain, login
      smtp_tls_security_level = may

  - **NOTE:** Only add `smtp_tls_security_level = may` if it is not already in the file. This setting will allow both TLS and non-TLS connections to the server.
  - Ref: https://www.postfix.org/SASL_README.html#client_sasl_enable

<br><br>

**IMPORTANT:** if your **smtp_sasl_security_options** setting includes **noplaintext** like this `smtp_sasl_security_options = noplaintext, noanonymous` , as suggested in some tutorials online, you will get the following error when trying to send email:

    fatal: no SASL authentication mechanisms

Therefore, make sure it looks like this `smtp_sasl_security_options = noanonymous`

<br><br>

- **Add Transport Map for Microsoft emails** - This tells where to find the transport file for Microsoft emails. We will create this file in the later step. This file will tell Postfix to use the relay server for Microsoft emails so that outbound emails from our server to Microsoft emails are not blocked.

      # TRANSPORT
      transport_maps = regexp:/etc/postfix/transport.microsoft

  - This will allow us to use a relay server only for Microsoft emails and our own SMTP for all others.

- Save and close the file: **CTRL + S**, **CTRL + X**

### Add SASL Passwords File

**NOTE:** Above in the **postfix/main.cf**, we have configured a setting `smtp_sasl_password_maps = hash:/etc/postfix/sasl_passwd` which maps a path to file called **sasl_passwd**. This file will contain our SMTP Relay credentials. We will create this file in the next step.

- Create a file called **sasl_passwd** in /etc/postfix directory

      sudo nano /etc/postfix/sasl_passwd

- Add the following line to the file. **_Replace {{SMTP_EMAIL}} and {{SMTP_PASSWORD}} with your SMTP Relay credentials from Brevo_**

      [smtp-relay.brevo.com]:587     {{SMTP_EMAIL}}:{{SMTP_PASSWORD}}

  - NOTE you can get your credentials from Brevo SMTP Relay page - as described above in the section [Get Relay SMTP Credentials](#get-relay-smtp-credentials)
  
  - Ref: https://developers.brevo.com/docs/using-the-smtp-relay-with-postfix

- Save and close the file: **CTRL + S**, **CTRL + X**

- Create **sasl_passwd.db** - the corresponding hash db file - using postmap

      sudo postmap /etc/postfix/sasl_passwd

- **Change the file permissions** so only root can read and write to files sasl_passwd and sasl_passwd.db

      sudo chmod 600 /etc/postfix/sasl_passwd /etc/postfix/sasl_passwd.db

### Add Transport Map File for Microsoft

**NOTE:** Above in the **postfix/main.cf**, we have configured a setting `transport_maps = regexp:/etc/postfix/transport.microsoft` which maps a path to file called **transport.microsoft**. This file will contain a regular expression that will match all Microsoft emails and will tell Postfix to use the relay server for them.

- Create a file called **transport.microsoft** in /etc/postfix directory

      sudo nano /etc/postfix/transport.microsoft

- Add the following line to the file. **_Replace {{SMTP_PORT}} with your SMTP port from Brevo_**

      /.*@(outlook|hotmail|live|msn)\..*/i      relay:[smtp-relay.brevo.com]:{{SMTP_PORT}}

- Save and close the file: **CTRL + S**, **CTRL + X**

- Create the corresponding db file with postmap

      sudo postmap /etc/postfix/transport.microsoft

### Restart Postfix and Dovecot

- Restart dovecot and postfix service

      sudo service dovecot restart && \
      sudo service postfix restart

### Add Your Domain in Relay Server

- Log into your **Brevo.com** account
- Click on account name - in the top right corner
- Go to **Senders & IP** > **Domains** > **Add a domain**
- Enter your domain name and click **Save**
  - This will take you to **Verify** page
  - You will see 2 DNS records that you need to add to your DNS server: **Brevo code** and **DKIM record**

- **Add Brevo verification DNS Records** in your domain name provider's DNS management panel. It should look like this:

  | Type | Name            | Value                                       | TTL  |
  | ---- | --------------- | ------------------------------------------- | ---- |
  | TXT  | @               | brevo-code:9bd4e38dffec465ca44794e5af42d5e2 | Auto |
  | TXT  | mail._domainkey | k=rsa;p=d48b2e4b4e18...                     | Auto |
 
- Go back to Brevo website Verify page and click **Verify** domain
- You should see message: **"The domain your-domain is successfully authenticated."**

### Watch Mail Log

All the actions that happen on Postfix are logged in the file **/var/log/mail.log**. We can watch this file in real time to see what is happening when we send emails.

- Open second terminal window and run the following command to watch the mail log

      sudo tail -f /var/log/mail.log

### Test Transport Map and Relay

We have set up a transport map that tells Postfix to use the relay server when email recipient is a Microsoft email address. 

Now we need to confirm this mechanism works as expected.

- Send email to your Microsoft email account with the command below (**_replace "From:" and "To:" email addresses with your own_**)
      
      echo "This is the test message" | mail -aFrom:admin@yourdomain.com -s "Test - Wed, 27 Sep, 11:45" my-email@hotmail.com

- As you send this email, watch the mail log in the other terminal window that you have opened in the previous step.
- You should see something like this in the mail log

      Sep 27 11:45:38 your-domain postfix/pickup[12345]: 12345ABCDE: uid=1000 from=<admin@yourdomain.com>
      Sep 27 11:45:38 your-domain postfix/cleanup[67890]: 12345ABCDE: message-id=<12345678901234.12345ABCDE@yourdomain.com>
      Sep 27 11:45:38 your-domain opendkim[15678]: 12345ABCDE: DKIM-Signature field added (s=default, d=yourdomain.com)
      Sep 27 11:45:38 your-domain postfix/qmgr[934567]: 12345ABCDE: from=<admin@yourdomain.com>, size=379, nrcpt=1 (queue active)
      Sep 27 11:45:40 your-domain postfix/relay/smtp[222156]: 12345ABCDE: to=<my-mail@hotmail.com>, relay=smtp-relay.brevo.com[1.123.345.56]:587, delay=2, delays=0.03/0.03/1.7/0.26, dsn=2.0.0, status=sent (250 Message queued as <12345678901234.12345ABCDE@yourdomain.com>)
      Sep 27 11:45:40 your-domain postfix/qmgr[412345]: 12345ABCDE: removed


## Fallback Relay to Bypass All Blacklists

Besides Microsoft there will be other servers that will blacklist your server's IP range.

We must handle this scenario so that our server can still deliver emails to recipients on those servers.

We will configure Postfix to forward mail to SMTP relay if email delivery fails.

### Set up Fallback Relay

- Open main.cf file

      sudo nano /etc/postfix/main.cf

- Add the following lines at the end of the file

      # Fallback Relay
      smtp_fallback_relay = [smtp-relay.brevo.com]:587

**PROBLEM:** smtp_fallback_relay will only work when the email is soft bounced (code 4xx). If the email is hard bounced (code 5xx), then Postfix will not use the fallback relay.  

**SOLUTION:** We must change the SMTP relay code from 5xx to 4xx, so Postfix treats all bounced emails as soft bounced and consequently uses fallback relay. 

- Add the following line at the bottom of the /etc/postfix/main.cf file.

      smtp_reply_filter = pcre:/etc/postfix/smtp_reply_filter

- Save and close the file: **CTRL + S**, **CTRL + X**

**NOTE:** In the above line we are telling Postfix to use a file called **smtp_reply_filter** to filter the SMTP reply codes. We will create this file in the next step. Also note that the path to the file is prefixed with **pcre:**. This means that we will use **PCRE** (Perl Compatible Regular Expressions) to filter the SMTP reply codes.

> PCRE (Perl Compatible Regular Expressions) - is a library written in C that implements regular expression engine which has same capabilities as Perl programming language.


- Create **smtp_reply_filter** file

      sudo nano /etc/postfix/smtp_reply_filter

- Add the following line to the file

      /^5(.*)$/ 4$1

  - **NOTE:** Make sure that you do not have any spaces before the above expression

- Save and close the file: **CTRL + S**, **CTRL + X**

- Install **postfix-pcre** package

      sudo apt install postfix-pcre

### Test Fallback Relay

In order to test the fallback relay we need to send an email to a server that will reject our email.

Since we can reliably get the rejection from Microsoft, we will use Microsoft email address as a recipient email for testing.

But before we do that, we must first temporarily disable the transport map that we have set up for Microsoft emails, so that Postfix does not use the mechanism we have set up specifically to bypass Microsoft blacklist.

- Open main.cf file

      sudo nano /etc/postfix/main.cf

- Comment out the following line by adding **#** at the beginning of the line

      # transport_maps = regexp:/etc/postfix/transport.microsoft

- Save and close the file: **CTRL + S**, **CTRL + X**

- Restart Postfix

      sudo systemctl restart postfix

- Open second terminal window to watch the mail log, if you haven't done so already. Type this command to watch the mail log

      sudo tail -f /var/log/mail.log

- In the first terminal, send email to your Microsoft email account with the command below (**_replace "From:" and "To:" email addresses with your own_**)
      
      echo "This is the test message" | mail -aFrom:admin@yourdomain.com -s "Test - Thu, 28 Sep, 07:25 AM" my-email@hotmail.com


- As you send this email, watch the mail log in the second terminal window that you have opened previously.

- You should see something like this in the mail log


      Sep 27 21:25:27 your-domain postfix/pickup[123456]: 123ABC456D: uid=1000 from=<admin@yourdomain.com>
      Sep 27 21:25:27 your-domain postfix/cleanup[234567]: 123ABC456D: message-id=<23456789012345.123ABC456D@yourdomain.com>
      Sep 27 21:25:27 your-domain opendkim[345678]: 123ABC456D: DKIM-Signature field added (s=default, d=yourdomain.com)
      Sep 27 21:25:27 your-domain postfix/qmgr[456789]: 123ABC456D: from=<admin@yourdomain.com>, size=382, nrcpt=1 (queue active)
      Sep 27 21:25:31 your-domain postfix/smtp[567890]: hotmail-com.olc.protection.outlook.com[104.47.22.161]:25: replacing server reply "550 5.7.1 Unfortunately, messages from [123.123.123.12] weren't sent. Please contact your Internet service provider since part of their network is on our block list (S3140). You can also refer your provider to http://mail.live.com/mail/troubleshooting.aspx#errors. [DB8EUR06FT056.eop-eur06.prod.protection.outlook.com 2023-09-27T21:25:31.391Z 08DBBF3EF18BB3A3]" with "450 5.7.1 Unfortunately, messages from [123.123.123.12] weren't sent. Please contact your Internet service provider since part of their network is on our block list (S3140). You can also refer your provider to http://mail.live.com/mail/troubleshooting.aspx#errors. [DB8EUR06FT056.eop-eur06.prod.protection.outlook.com 2023-09-27T21:25:31.391Z 08DBBF3EF18BB3A3]"
      Sep 27 21:25:31 your-domain postfix/smtp[567890]: 123ABC456D: host hotmail-com.olc.protection.outlook.com[104.47.22.161] said: 450 5.7.1 Unfortunately, messages from [123.123.123.12] weren't sent. Please contact your Internet service provider since part of their network is on our block list (S3140). You can also refer your provider to http://mail.live.com/mail/troubleshooting.aspx#errors. [DB8EUR06FT056.eop-eur06.prod.protection.outlook.com 2023-09-27T21:25:31.391Z 08DBBF3EF18BB3A3] (in reply to MAIL FROM command)
      Sep 27 21:25:31 your-domain postfix/smtp[567890]: 123ABC456D: lost connection with hotmail-com.olc.protection.outlook.com[104.47.22.161] while sending RCPT TO
      Sep 27 21:25:33 your-domain postfix/smtp[567890]: hotmail-com.olc.protection.outlook.com[104.47.18.225]:25: replacing server reply "550 5.7.1 Unfortunately, messages from [123.123.123.12] weren't sent. Please contact your Internet service provider since part of their network is on our block list (S3140). You can also refer your provider to http://mail.live.com/mail/troubleshooting.aspx#errors. [VI1EUR06FT061.eop-eur06.prod.protection.outlook.com 2023-09-27T21:25:33.099Z 08DBBF7EE71A034F]" with "450 5.7.1 Unfortunately, messages from [123.123.123.12] weren't sent. Please contact your Internet service provider since part of their network is on our block list (S3140). You can also refer your provider to http://mail.live.com/mail/troubleshooting.aspx#errors. [VI1EUR06FT061.eop-eur06.prod.protection.outlook.com 2023-09-27T21:25:33.099Z 08DBBF7EE71A034F]"
      Sep 27 21:25:33 your-domain postfix/smtp[567890]: 123ABC456D: host hotmail-com.olc.protection.outlook.com[104.47.18.225] said: 450 5.7.1 Unfortunately, messages from [123.123.123.12] weren't sent. Please contact your Internet service provider since part of their network is on our block list (S3140). You can also refer your provider to http://mail.live.com/mail/troubleshooting.aspx#errors. [VI1EUR06FT061.eop-eur06.prod.protection.outlook.com 2023-09-27T21:25:33.099Z 08DBBF7EE71A034F] (in reply to MAIL FROM command)
      Sep 27 21:25:33 your-domain postfix/smtp[567890]: 123ABC456D: lost connection with hotmail-com.olc.protection.outlook.com[104.47.18.225] while sending RCPT TO
      Sep 27 21:25:35 your-domain postfix/smtp[567890]: 123ABC456D: to=<my-email@hotmail.com>, relay=smtp-relay.brevo.com[1.765.321.09]:587, delay=7.6, delays=0.02/0.03/7.3/0.24, dsn=2.0.0, status=sent (250 Message queued as <23456789012345.123ABC456D@yourdomain.com>)
      Sep 27 21:25:35 your-domain postfix/qmgr[456789]: 123ABC456D: removed

  - Notice in the line 6 of the log, that Postfix is trying to send email to Microsoft server and it is getting rejected with the code 450. 
  
        host hotmail-com.olc.protection.outlook.com[104.47.22.161] said: 450
  
  - Notice in the line 11 of the log, that Postfix has changed over to the fallback relay and is trying to send email to the relay server.
  
        relay=smtp-relay.brevo.com[1.765.321.09]:587
  
  - Also in the line 11 of the log, notice that the email has been successfully sent to the relay server.
  
        status=sent (250 Message queued as <
  
  - In the last line 12 of the log, notice that Postfix has removed the email from the queue.
  
        removed

- Now check your **Microsoft email account** inbox. You should find your email there. 

- If the email you sent is not in the INbox,cCheck the **spam folder**. You should find your email there.

### Re-enable Transport Map

Now that we have tested successfully that the fallback relay works, we can re-enable the transport map for Microsoft emails.

- Open main.cf file

      sudo nano /etc/postfix/main.cf

- Uncomment the following line by removing **#** at the beginning of the line

      transport_maps = regexp:/etc/postfix/transport.microsoft

- Save and close the file: **CTRL + S**, **CTRL + X**

- Restart Postfix

      sudo systemctl restart postfix


## Exercise - Build Email Sender API Service


### Create Web API App

We will create a Web API app which will serve as an email sender service on our server.

- Open a terminal window

- Create project folder inside your user folder. We will call our project folder **email-sender**

      mkdir -p ~/dev/email-sender

  - **-p** flag is used to create parent directories if they do not exist. In this case it will create folder **dev** if it does not exist.
 
- Go inside project folder

      cd ~/dev/email-sender


- Create .Net project of type ASP.NET Core Web API, called Server - in your open terminal run command

      dotnet new webapi --use-program-main --use-controllers -o Server

  - **--use-program-main** - If specified, an explicit Program class and Main method will be used (which is a traditional structure) instead of top-level statements which is new feature used by default since .Net 6. For more info on **top-level statements** [click here](https://learn.microsoft.com/en-us/dotnet/csharp/whats-new/tutorials/top-level-statements). We do not recommend using top-level-statements if you don't have much experience with .Net.
  - **--use-controllers** - to use controllers instead of minimal APIs. Since .Net 8 minimal APIs are the default. We do not recommend using minimal APIs if you don't have much experience with .Net. For more info on **minimal APIs** [click here](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/minimal-apis/overview).
  - **-o** - means output folder path/name. 
  - For full reference of the **dotnet new webapi** command [click here](https://learn.microsoft.com/en-us/dotnet/core/tools/dotnet-new-sdk-templates#webapi).


This will create a folder **Server** and inside it project file **Server.csproj** with all other application files.

We will refer to this project as Server App.


- Open folder **Server** in VS Codium

      codium Server

- You should see a popup in the bottom-right that says "Required assets to build and debug are missing from 'Server'. Add them?"

  - Click **Yes**

- Open Terminal window in VS Codium:  hit **CTRL + `**
  - You should see terminal panel appear at the bottom of VS Codium

- Run the app in the terminal window:

      dotnet run

- You should see output starting like this:

      Building...
      info: Microsoft.Hosting.Lifetime[14]
            Now listening on: http://localhost:5167

- Open a browser and navigate to http://localhost:5167/WeatherForecast . NOTE: We are using example port 5167, but it could be any different number generated by app at creation time. Replace the port number with the one you see in the output of the previous command.

- You should see a JSON response with weather forecast data like this:

      [{"date":"2023-12-20","temperatureC":-17,"temperatureF":2,"summary":"Mild"},{"date":"2023-12-21","temperatureC":4,"temperatureF":39,"summary":"Hot"},{"date":"2023-12-22","temperatureC":41,"temperatureF":105,"summary":"Scorching"},{"date":"2023-12-23","temperatureC":9,"temperatureF":48,"summary":"Freezing"},{"date":"2023-12-24","temperatureC":-12,"temperatureF":11,"summary":"Mild"}]

This is coming from the **WeatherForecastController** class in the **Controllers** folder.

- Stop the app in the terminal window: hit **CTRL + C**


### Add Version Setting

- Add setting **"Version"** to appsettings.json file

  - Open **appsettings.json** file

  - Add the line `"Version": "1.0.0"` to the bottom. It should now look like this: 

        {
          "Logging": {
            "LogLevel": {
              "Default": "Information",
              "Microsoft.AspNetCore": "Warning"
            }
          },
          "AllowedHosts": "*",
          "Version": "1.0.0"
        }

**IMPORTANT:** You should increment the **third number** in the Version by one **every time before you deploy to server**. For example if the version is 1.0.0, you should change it to 1.0.1.
 
We use this variable to show what is the version of code we are using in http response.
This way we can be sure that the code we have updated is actually the code being used.

This is useful if we forget to restart the web service after we have deployed changes and therefore we think that web api is using new changes but in fact the old web api is still being used.

By printing out the version in response of api, we can confirm that the right version of code is running on server.




### Create Email Sender Controller

We will now add a new controller called **EmailSenderController** which will serve as an email sender service.

- In VS Codium, in the terminal window, run the following command:

      dotnet new apicontroller -o Controllers -n EmailSenderController

  - **-o** - means output folder path/name
  - **-n** - means name of the controller class

This should create a new file **EmailSenderController.cs** in the **Controllers** folder with a following content:


```csharp
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;

namespace MyApp.Namespace
{
    [Route("api/[controller]")]
    [ApiController]
    public class EmailSenderController : ControllerBase
    {
    }
}
```


- Replace **MyApp.Namespace** with **Server.Controllers**


We will add the code that will send an email using our SMTP server.

- Replace the code inside EmailSenderController.cs with the following code:

```csharp
/******************************************
 *  Author:         Vex Tatarevic
 *  Date Created:   2024-01-08
 ******************************************/

using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using System.Net.Mail;
using System.Net;

namespace Server.Controllers;

[ApiController]
public class EmailSenderController : ControllerBase
{
    private readonly string _version;

    public EmailSenderController(IConfiguration config)
    {
        // Get "Version" setting from appsettings.json
        _version = config.GetValue<string>("Version") ?? "";
    }

    [HttpGet(
        "api/mail/{from}/{to}/{subject}/{message}/{host?}/{port?}/{ssl?}/{username?}/{password?}"
    )]
    public IActionResult SendMail(
        string from,
        string to,
        string subject,
        string message,
        string host = "localhost",
        int port = 25,
        bool ssl = true,
        string username = "admin",
        string password = "****"
    )
    {
        var mailMessage = new MailMessage(from, to, subject, message);

        var smtpClient = new SmtpClient(host, port) { EnableSsl = ssl };

        if (host != "localhost")
        {
            smtpClient.Credentials = new NetworkCredential(username, password);
        }

        try
        {
            smtpClient.Send(mailMessage);

            return Ok($"Version: {_version} ... Success! Message Sent.");
        }
        catch (Exception ex)
        {
            var errorMessage = ex.ToString();
            
            return BadRequest($"Version: {_version} ... Error: {errorMessage}");
        }
    }
}
```


### Set Up Environments

In the book **"Linux WebApp"** we have learnt how to set up custom environments. We will do that here.

#### Add/Edit Settings Files

- Set up **Local** environment

  - Change environment name to **"Local"** inside **launchSettings.json** file by changing the environment variable line `"ASPNETCORE_ENVIRONMENT": "Development"` to `"ASPNETCORE_ENVIRONMENT": "Local"`

        sed -i 's/"ASPNETCORE_ENVIRONMENT": "Development"/"ASPNETCORE_ENVIRONMENT": "Local"/g' Properties/launchSettings.json

  - Rename the file **appsettings.Development.json** to **appsettings.Local.json**

        mv appsettings.Development.json appsettings.Local.json

  - Copy the urls from **launchSettings.json** file line that looks like this `"applicationUrl": "https://localhost:7041;http://localhost:5140"`

  - Add **Urls** setting to the bottom of **appsettings.Local.json** file. Paste into it urls you copied in previous step.

    ```json
    {
      "Logging": {
        "LogLevel": {
          "Default": "Information",
          "Microsoft.AspNetCore": "Warning"
        }
      },
      "Urls": "https://localhost:7041;http://localhost:5140"
    }    
    ```


- Set up **Dev** environment

  - Create a new file **appsettings.Dev.json** 
  
        touch appsettings.Dev.json

  - Add **Urls** setting to **appsettings.Dev.json** file

    ```json
    {
      "Urls": "http://localhost:6379"
    }
    ```

- Set up **Test** environment

  - Create a new file **appsettings.Test.json** 
  
        touch appsettings.Test.json

  - Add **Urls** setting to **appsettings.Test.json** file

    ```json
    {
      "Urls": "http://localhost:7327"
    }
    ```

- Set up **Prod** environment

  - Add **Urls** setting to the bottom of **appsettings.json** file

    ```json
    {
      "Logging": {
        "LogLevel": {
          "Default": "Information",
          "Microsoft.AspNetCore": "Warning"
        }
      },
      "AllowedHosts": "*",
      "Urls": "http://localhost:9406"
    }
    ```

#### Create Env Class

In the book **"Linux WebApp"** we have learnt how to create a class called **Env** which will read the environment name from **web.config** file and will add the corresponding appsettings file to the WebApplicationBuilder at runtime on application startup.

We will use the same class here.

- Create  **Env.cs** in the root folder of the project

      touch Env.cs

- Replace the code inside **Env.cs** file with the following code:

```csharp
/******************************************
 *  Author:         Vex Tatarevic
 *  Date Created:   2023-10-24
 ******************************************/

namespace Server;
public class Env
{
    //--------------------------------------------
    //      Fields
    //--------------------------------------------

    // Constants

    public const string Local = "Local";
    public const string Dev = "Dev";
    public const string Test = "Test";
    public const string Prod = "Prod";

    // Properties

    public string Name { get; set; } = Local;

    public bool IsLocal
    {
        get { return Name == Local; }
    }
    public bool IsDev
    {
        get { return Name == Dev; }
    }
    public bool IsTest
    {
        get { return Name == Test; }
    }
    public bool IsProd
    {
        get { return Name == Prod; }
    }

    //--------------------------------------------
    //      Constructor
    //--------------------------------------------

    /// <summary>
    /// Reads current environment name from : <br />
    /// - xml file named <strong> web.config </strong>  <br />
    /// - inside the field : <strong> &lt; environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Test" /&gt; </strong> <br /><br />
    /// Sets Env objects' property Name to name that was read from web.config file. <br /> <br />
    /// NOTE:  <br />
    /// - web.config is only created if the code was compiled with Release configuration <br />
    /// - web.config will only contain &lt;environmentVariable &gt; field if the app was compiled with passing parameter EnvironmentName <br /> <br />
    /// <strong>Example</strong> of running publish commmand with Release config and EnvironmentName: <br /> <br />
    ///   <strong> dotnet publish -c Release -p:EnvironmentName=Test </strong>
    /// </summary>
    public Env()
    {
        var ASPNETCORE_ENVIRONMENT = "ASPNETCORE_ENVIRONMENT";
        var webConfigFileName = "web.config";
        var currentExecutingBaseDirPath = AppDomain.CurrentDomain.BaseDirectory;
        var webConfig_executingPath = Path.GetFullPath(
            webConfigFileName,
            currentExecutingBaseDirPath
        );

        // If web.config does not exist, it means that app was not compiled for Release. 
        // Therefore exit further setting of the environment.
        // Environment will be set to Local by default.
        if (!File.Exists(webConfig_executingPath))
        {
            return;
        }

        var aspNetCoreEnvLineStart = $"<environmentVariable name=\"{ASPNETCORE_ENVIRONMENT}\"";
        string? line;
        using (var file = new StreamReader(webConfig_executingPath))
        {
            while ((line = file.ReadLine()) != null)
            {
                if (line.Contains(aspNetCoreEnvLineStart))
                {
                    var startToken = "value=\"";
                    var startIndex = line.IndexOf(startToken) + startToken.Length;
                    var envName = line.Substring(startIndex);
                    Name = envName.Substring(0, envName.IndexOf("\""));
                    break;
                }
            }
        }
    }

    //--------------------------------------------
    //      Methods
    //--------------------------------------------

    /// <summary>
    /// Explicitly adds appsettings files for the current environment to WebApplicationBuilder. <br />
    /// NOTE: you must call this code in Program.cs immediately after builder has been initialized like this:  <br />  <br />
    ///  <strong> var builder = WebApplication.CreateBuilder(new WebApplicationOptions { EnvironmentName = env.Name });   <br />
    ///   env.AddConfiguration(builder); </strong>  <br />  <br />
    ///
    /// It is necessary to call this code in order for your app to work properly on Linux server because:  <br />
    /// On local Windows computer settings files will be added automatically, but on Linux computer especially on our remote host, 
    /// settings files will not be added unless we explicitly call "builder.Configuration.AddJsonFile(FILE_PATH)".<br />
    /// Env.AddConfiguration method checks what is the current environment and adds the specific appsettings file for that environment 
    /// by calling builder.Configuration.AddJsonFile with a path to that file..
    /// </summary>
    /// <param name="builder"></param>
    public void AddConfiguration(WebApplicationBuilder builder)
    {
        if (IsLocal)
        {
            return;
        }

        var assembly = System.Reflection.Assembly.GetEntryAssembly();
        if (assembly != null)
        {
            var execFile = assembly.Location;
            var baseDir = Path.GetDirectoryName(execFile);
            if (baseDir != null)
            {
                var defaultSettingsPath = Path.Combine(baseDir, "appsettings.json");

                Console.WriteLine(
                    $"... - Adding default settings from: {defaultSettingsPath} ..."
                );

                builder.Configuration.AddJsonFile(defaultSettingsPath);
                if (!IsProd)
                {
                    var envSettingsPath = Path.Combine(baseDir, $"appsettings.{Name}.json");

                    Console.WriteLine(
                        $"... - Adding environment settings from: {envSettingsPath} ..."
                    );

                    builder.Configuration.AddJsonFile(envSettingsPath);
                }
            }
        }
    }
}
```

#### Update Program Class

- Update Program.cs - replace the code with the following :

```csharp
namespace Server;

public class Program
{
    public static void Main(string[] args)
    {
        Console.WriteLine("\n Starting app ...");
                
        Console.Write("\n --------------------------------------------- \n => Initializing ... \n --------------------------------------------- \n");

        //.......... Create Env object for environment init tasks .......
        var env = new Env();
        Console.WriteLine($"... Environment: {env.Name}");
        //...............................................................

        var builder = WebApplication.CreateBuilder( // args);
            //.......... Get Environment Name from web.config ...........
            new WebApplicationOptions { EnvironmentName = env.Name }
        );
        //...............................................................

        //............... Add config from appsettings files .............
        Console.WriteLine($"... Adding configuration ...");
        env.AddConfiguration(builder);
        //...............................................................


        // Add services to the container.

        Console.Write("\n ------------------------------- \n => Adding services to dependency injection ... \n -------------------------------- \n");

        Console.WriteLine($"... Adding controllers ...");

        builder.Services.AddControllers();

        if (env.IsLocal)
        {
            // Learn more about configuring Swagger/OpenAPI at https://aka.ms/aspnetcore/swashbuckle
            builder.Services.AddEndpointsApiExplorer();
            builder.Services.AddSwaggerGen();
        }

        Console.WriteLine("\n > BUILD");
        var app = builder.Build();


        // Configure the HTTP request pipeline.

        Console.Write("\n -------------------------------- \n => Configuring the HTTP request pipeline ... \n ---------------------------------- \n");

        if (env.IsLocal)
        {
            app.UseSwagger();
            app.UseSwaggerUI();
            app.UseHttpsRedirection();
        }

        Console.WriteLine($"... UseAuthorization ...");
        app.UseAuthorization();

        Console.WriteLine($"... MapControllers ...");
        app.MapControllers();

        Console.WriteLine("\n > RUN \n");
        app.Run();
    }
}
```


### Publish

Now let's publish all environments.

- Publish **Dev** into folder **bin/Dev**

      dotnet publish -c Release -o bin/Dev -p:EnvironmentName=Dev

- Publish **Test** into folder **bin/Test**

      dotnet publish -c Release -o bin/Test -p:EnvironmentName=Test

- Publish **Prod** into folder **bin/Prod**

      dotnet publish -c Release -o bin/Prod -p:EnvironmentName=Prod

### Run on Local Machine

 Let's run all environments locally to make sure they are running on correct ports that we have configured in appsettings files.

- Run Local environment

      dotnet run

- Run **Dev** environment and **verify** that it is running on Dev **port** **6379**

      dotnet ./bin/Dev/Server.dll

- Run **Test** environment and **verify** that it is running on Test **port** **7327**

      dotnet ./bin/Test/Server.dll

- Run **Prod** environment and **verify** that it is running on Prod **port** **9406**

      dotnet ./bin/Prod/Server.dll

 
### Server - Setup Reverse Proxy

Our NGINX server block for development environment domain dev.mysite.com should be set up as a reverse proxy - to forward all requests to our development environment app running on port 6379 on Kestrel server. 

We learnt how to do this in the previous book **"Linux WebApp"**. If you haven't done it, this is how your development server block should look like:

- Open **/etc/nginx/sites-available/dev.mysite.com** file

      sudo nano /etc/nginx/sites-available/dev.mysite.com

- You should see configuration like this:

```nginx
server {

        # Listen for http requests
        listen          80;
        listen          [::]:80;

        # Listen for https requests
        listen          443 ssl;
        listen          [::]:443 ssl;

        root /var/www/dev.mysite.com/htdocs;

        server_name dev.mysite.com www.dev.mysite.com;

        location / {

          proxy_pass         http://127.0.0.1:6379;
          proxy_http_version 1.1;
          proxy_set_header   Upgrade $http_upgrade;
          proxy_set_header   Connection keep-alive;
          proxy_set_header   Host $host;
          proxy_cache_bypass $http_upgrade;
          proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
          proxy_set_header   X-Forwarded-Proto $scheme;

        }
}
```

- Replace **dev.mysite.com** with your domain name

### Server - Set up Background Keep-Alive Service

We learnt, in the previous book **"Linux WebApp"**, how to set up background process service for keeping web api alive. If you haven't done it, this is how your Systemd service should look like:

- Go inside **/etc/systemd/system** folder

      cd /etc/systemd/system

- Open **/etc/systemd/system/dev.mysite.com.service** file

      sudo nano /etc/systemd/system/webapi_dev.mysite.com.service

- You should see configuration like this:

```ini
[Unit]
Description=.NET Web API App for site dev.mysite.com

[Service]
WorkingDirectory=/var/www/dev.mysite.com/htdocs
ExecStart=/usr/bin/dotnet /var/www/dev.mysite.com/htdocs/Server.dll
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=webapi_dev.mysite.com
User=admin
Environment=ASPNETCORE_ENVIRONMENT=Dev
Environment=DOTNET_PRINT_TELEMETRY_MESSAGE=false

[Install]
WantedBy=multi-user.target
```


- Make sure that service is enabled

      sudo systemctl enable webapi_dev.mysite.com.service

- Start the service

      sudo systemctl start webapi_dev.mysite.com.service

- Check the status of the service

      sudo systemctl status webapi_dev.mysite.com.service

- You should see status  **"active (running)"** and a green bullet point



### Deploy


For purpose of this exercise we will use example IP address **123.234.345.45** and example domain **mysite.com**

We will use **SFTP** to transfer files from our local machine to the server.

- Open Terminal window inside VS Codium where your project is open

- SFTP into the server

      sftp admin@123.234.345.45

    OR if you have set up passwordless connection in your ssh config file:

      sftp mysite.com


- Copy all files from local **bin/Dev** folder to remote **/var/www/dev.mysite.com/htdocs** folder

      put -r ./bin/Dev/* /var/www/dev.mysite.com/htdocs


- If you are not sure of locations you can do the following which is longer
    - Navigate to your development environment website dev.mysite.com folder

          cd /var/www/dev.mysite.com/htdocs

    - Print working directory to confirm you are in the right directory

          pwd

    - Delete all files

          rm *

    - Print local working directory

          lpwd

    - Go inside publish folder

          lcd bin/Dev

    - Copy all the files from local to remote folder

          put -r .

        OR

          mput *

- Exit SFTP

      exit

- SSH into the server

      ssh mysite.com

- Restart background service

      sudo systemctl restart webapi_dev.mysite.com.service

**NOTE:** You must restart the background service every time you publish new files to the server.

- Check the status of the service

      sudo systemctl status webapi_dev.mysite.com.service

### Test 

Now that we have deployed the Web API app to the server, we can test it.

- Open browser and type in api url, passing parameters: senderEmail, recipientEmail, subject, message, host, port

      https://dev.mysite.com/api/mail/admin@mysite.com/myemail@gmail.com/Testing_API/Hello_Im_testing_API_3/localhost/25

- You will get response like this:

      Version: 1.0.9 ... Error: System.Net.Mail.SmtpException: Failure sending mail.
       ---> System.IO.IOException: Unable to read data from the transport connection: The connection was closed.
         at System.Net.Mail.SmtpReplyReaderFactory.ProcessRead(Byte[] buffer, Int32 offset, Int32 read, Boolean readLine)
         at System.Net.Mail.SmtpReplyReaderFactory.ReadLines(SmtpReplyReader caller, Boolean oneLine)
         at System.Net.Mail.SmtpReplyReaderFactory.ReadLine(SmtpReplyReader caller)
         at System.Net.Mail.SmtpConnection.GetConnection(String host, Int32 port)
         at System.Net.Mail.SmtpTransport.GetConnection(String host, Int32 port)
         at System.Net.Mail.SmtpClient.Send(MailMessage message)
         --- End of inner exception stack trace ---
         at System.Net.Mail.SmtpClient.Send(MailMessage message)
         at Server.Controllers.EmailSenderController.SendMail(String from, String to, String subject, String message, String host, Int32 port, Boolean ssl, String username, String password)

- Check mail **log**

      sudo tail -f /var/log/mail.log

  - This will print out something like 

        Jan 12 03:47:46 mysite postfix/smtpd[1708519]: connect from localhost[::1]
        Jan 12 03:47:46 mysite postfix/smtpd[1708519]: fatal: no SASL authentication mechanisms
        Jan 12 03:47:47 mysite postfix/master[1694627]: warning: process /usr/lib/postfix/sbin/smtpd pid 1708519 exit status 1
        Jan 12 03:47:47 mysite postfix/master[1694627]: warning: /usr/lib/postfix/sbin/smtpd: bad command startup -- throttling

  - Exit : **CTRL + C**

- Check mail **errors**

      sudo nano /var/log/mail.err
      
  - This will print out something like

        Jan 12 03:47:46 mysite postfix/smtpd[1708519]: fatal: no SASL authentication mechanisms

  - Exit : **CTRL + C**

#### Problem - No SASL Authentication Mechanisms

The problem above is that we do not use encrypted authentication mechanism in .Net SMTP client, while our Postfix SMTP server requires it in this line of the /etc/postfix/main.cf:

    smtpd_sasl_security_options = noanonymous,noplaintext

The problem is outlined in more detail in this [Unix Stack Exchange post](https://unix.stackexchange.com/questions/763565/ubuntu-server-22-04-postfix-dovecot-error-postfix-smtpd-fatal-no-sasl-authenti)

Here you can read more about [Dovecot SASL Authentication Mechanisms](https://doc.dovecot.org/configuration_manual/authentication/authentication_mechanisms/#authentication-authentication-mechanisms)

Continue to next section to see how to fix this problem.

#### Solution - Remove SASL Authentication Mechanisms

We do not use the SMTP Client outside of our server. 
Because our Web API (SMTP Client) connects to Postfix (SMTP Server) on the same machine  - localhost - we do not need to require these 2 things:
1. Encryption of username and password (we are not even using username and password to talk to local smtp)
2. TLS encrypted auth 

Our message will still be encrypted on the way out from SMTP Server, when it is sent from postfix to the recipient email server.

To remove requiring TLS auth and encrypted credentials from SMTP Client, do the following:

- Open postfix/main.cf file

      sudo nano /etc/postfix/main.cf

- In **TLS parameters** section, add the following line

      smtpd_tls_auth_only = no

  - NOTE: You could skip this step because this is the default value. We are adding it here just to make it explicitly visible in the config that TLS auth is not required.

  - For more details on this attribute go to: https://www.postfix.org/TLS_README.html#server_tls_auth

- In **# SASL - Incoming / Server** section, **remove noplaintext** option in smtpd_sasl_security_options setting. It should now look like this:
   
      smtpd_sasl_security_options = noanonymous

  - For more details on this attribute go to: https://www.postfix.org/SASL_README.html#smtpd_sasl_security_options

- Restart postfix service

      sudo service postfix restart


#### Problem - Invalid SSL Certificate

- Open browser and type in emailsender API url - Replacing the sender email and recipient email parameters with your own email. NOTE: Sender email should be your user account email on the server e.g. admin@mysite.com

      https://dev.mysite.com/api/mail/admin@mysite.com/myemail@gmail.com/Testing_API/Hello_Im_testing_API_3/localhost/25

This time you will see a different error in the browser that looks like this:

    Version: 1.0.9 ... Error: System.Security.Authentication.AuthenticationException: The remote certificate is invalid according to the validation procedure:  RemoteCertificateNameMismatch
       at System.Net.Security.SslStream.SendAuthResetSignal(ReadOnlySpan`1 alert, ExceptionDispatchInfo exception)
       at System.Net.Security.SslStream.CompleteHandshake(SslAuthenticationOptions sslAuthenticationOptions)
       at System.Net.Security.SslStream.ForceAuthenticationAsync[TIOAdapter](Boolean receiveFirst, Byte[] reAuthenticationData, CancellationToken cancellationToken)
       at System.Net.Security.SslStream.AuthenticateAsClient(SslClientAuthenticationOptions sslClientAuthenticationOptions)
       at System.Net.Mail.SmtpConnection.GetConnection(String host, Int32 port)
       at System.Net.Mail.SmtpTransport.GetConnection(String host, Int32 port)
       at System.Net.Mail.SmtpClient.Send(MailMessage message)
       at Server.Controllers.EmailSenderController.SendMail(String from, String to, String subject, String message, String host, Int32 port, Boolean ssl, String username, String password)

- Print mail log

      sudo tail -f /var/log/mail.log

  - You will see ouput like this:

        Jan 14 22:48:23 mysite postfix/smtpd[1770150]: connect from localhost[::1]
        Jan 14 22:48:24 mysite postfix/smtpd[1770150]: lost connection after STARTTLS from localhost[::1]
        Jan 14 22:48:24 mysite postfix/smtpd[1770150]: disconnect from localhost[::1] ehlo=1 starttls=1 commands=2

  - Exit : **CTRL + C**


The problem is that we have set SSL flag to true when making SMTP call from our Web API. This is unnecessary because we are just calling the SMTP server on our localhost.

Continue to next section to see how to fix this problem.


#### Solution - Do Not Use SSL Connection on Local SMTP Server

To fix the problem with SSL certificate, we need to set SSL flag to false when making SMTP call from our Web API to SMTP server on localhost.

- Open browser and type in emailsender API url. This time add the parameter false for SSL. It should look like this

      https://dev.mysite.com/api/mail/admin@mysite.com/myemail@gmail.com/Testing_API/Hello_Im_testing_API_3/localhost/25/false

- This time you should see success response in browser like this:

      Version: 1.0.9 ... Success! Message Sent.

- Check mail log

      sudo tail -f /var/log/mail.log

  - You will see ouput like this:

        Jan 14 23:06:31 mysite postfix/smtpd[1770321]: connect from localhost[::1]
        Jan 14 23:06:31 mysite postfix/smtpd[1770321]: 3B875A1509: client=localhost[::1]
        Jan 14 23:06:31 mysite postfix/cleanup[1770555]: 3B875A1509: message-id=<20140014230731.3B875A1509@mysite.com>
        Jan 14 23:06:31 vexit opendkim[424]: 3B875A1509: DKIM-Signature field added (s=default, d=mysite.com)
        Jan 14 23:06:31 mysite postfix/qmgr[1769732]: 3B875A1509: from=<admin@mysite.com>, size=493, nrcpt=1 (queue active)
        Jan 14 23:06:34 mysite postfix/smtp[1770556]: 3B875A1509: to=<myemail@gmail.com>, relay=gmail-smtp-in.l.google.com[2700:3f0:4002:c03::1c]:25, delay=2.9, delays=0.07/0.02/1.9/0.89, dsn=2.0.0, status=sent (250 2.0.0 OK  1705273594 h12-9d79802c4ff4459888bee00b29d0b8b0>
        Jan 14 23:06:34 mysite postfix/qmgr[1769732]: 3B875A1509: removed

- Check your receiving email inbox e.g. myemail@gmail.com
  - You should receive the email message. Make sure to check your Spam/Junk folder.

**NOTE:** If you send your email to Gmail account you will be able to check details and confirm that the message was encrypted with TLS. You can do this by **expanding** the **To** field of the email message. Then you should see under the Date field a gray padlock icon with the text next to it **"Standard encryption (TLS)"**. Check out this page on Google for more details: https://support.google.com/mail/answer/6330403?hl=en


### Disable/Remove SendMail API End Point

In this exercise we have created a Web API End point to test if our SMTP Server can be called from .Net application.

Now that we have proven that it works correctly, we MUST remove this end point because it presents a system vulnerability, since anyone can start sending emails on our server by just calling this end point.

- Open **EmailSenderController.cs** file

- Comment out the method SendMail starting with this line:

```csharp
[HttpGet(
    "api/mail/{from}/{to}/{subject}/{message}/{host?}/{port?}/{ssl?}/{username?}/{password?}"
)]
```

If we wish to test this end point again in future we can uncomment it, however this API should only be used on Dev environment. It should never get deployed to other environments.

The best idea is to completely remove EmailSenderController.cs file from the project.



## Revision - Mail Logs Operations

- View **mail.log**

      sudo tail -f /var/log/mail.log

  - Exit : **CTRL + C**


- View **mail.err**

      sudo tail -f /var/log/mail.err

  - Exit : **CTRL + C**

- **Delete content** of mail.log and mail.err in **one command**

      sudo truncate -s 0 /var/log/mail.log \
      && sudo truncate -s 0 /var/log/mail.err

You can also delete the content manually by opening the files in nano, selecting all the content and cutting it out:

- **Delete content** of mail.log  **manually**

        sudo nano /var/log/mail.log 

  - Set Mark: **ALT + A**
  - Select All: **CTRL + DOWN_ARROW**
  - Cut: **CTRL + K**
  - Save & Exit: **CTRL+ S**, **CTRL + X**

You can delete and recreate the log files although it is not advisable. But just in case you deleted the files here is how you can recreate them:

- **Delete files** mail.log and mail.err if there is too much data, to save the server space

      sudo rm /var/log/mail.log \
      && sudo rm /var/log/mail.err

- Create  **mail.log** and **mail.err** files - don't create files manually but rather restart the services and send email to create them :

    - Restart postfix and rsyslog services

          sudo service postfix restart \
          && sudo service rsyslog restart
    
    - Send an email

          echo "test email" | sendmail your-account@gmail.com

      - This will create mail.log - ONLY once the email is sent. Wait until you receive email before checking that mail.log exists

NOTE: mail.err will be created only when error occurs while sending email.


- Get **more detail log** - Temporarily add -v flag (verbose) inside the /etc/postfix/master.cf

  - Open /etc/postfix/master.cf

        sudo nano /etc/postfix/master.cf

  - Add **-v** flag at the end of **"smtp inet"** line like this:

        smtp      inet  n       -       y       -       -       smtpd -v

**NOTE:** Make sure you remove the -v flag after you are done debugging



## Block SPAM


- Refs:
  - [Postfix.org - Postfix SMTP relay and access control](https://www.postfix.org/SMTPD_ACCESS_README.html)
  - [Linux Babe - 7 Effective Tips for Blocking Email Spam with Postfix SMTP Server](https://www.linuxbabe.com/mail-server/block-email-spam-postfix)
  - https://www.samlogic.net/articles/smtp-commands-reference.htm

- Open Postfix config file

      sudo nano /etc/postfix/main.cf

- Add new section **RESTRICTIONS**

      # RESTRICTIONS


### smtpd_sender_restrictions

Ref: https://www.postfix.org/postconf.5.html#smtpd_sender_restrictions


- If you have already added the setting **smtpd_sender_restrictions**, move it under **"# RESTRICTIONS"** section

- Reject Emails from senders with: 
  - No PTR Record, 
  - No valid A record, 
  - No valid MX record

        smtpd_sender_restrictions =
              permit_mynetworks
              permit_sasl_authenticated
        # -- Below settings did not work for me. I was not able to receive any emails I sent to myself from Gmail and Hotmail  ---
        #     reject_unknown_reverse_client_hostname    # No PTR record
        #     reject_unknown_client_hostname            # No A record          
        #     reject_unknown_sender_domain              # No MX record

**NOTE:** The above 3 settings to block senders without PTR, A record and MX record did not work as expected for me. I tried sending email from Hotmail and Gmail and they were not going through. So I have comment those settings out, but it's there for your information. You may find the way to make it work.


### smtpd_helo_restrictions

Ref: https://www.postfix.org/postconf.5.html#smtpd_helo_restrictions

- Require HELO/EHLO

      smtpd_helo_required = yes
      smtpd_helo_restrictions =
          permit_mynetworks
          permit_sasl_authenticated
          reject_invalid_helo_hostname
          reject_non_fqdn_helo_hostname
          reject_unknown_helo_hostname


### smtpd_relay_restrictions

Ref: https://www.postfix.org/postconf.5.html#smtpd_relay_restrictions

- Restrict relaying - should already be set by default

      smtpd_relay_restrictions =
          permit_mynetworks
          permit_sasl_authenticated
          defer_unauth_destination


### smtpd_recipient_restrictions

Ref: https://www.postfix.org/postconf.5.html#smtpd_recipient_restrictions


- If you have already added the setting **smtpd_recipient_restrictions**, move it under **"# RESTRICTIONS"** section

      smtpd_recipient_restrictions = 
                permit_mynetworks
                permit_sasl_authenticated
                check_policy_service unix:private/policyd-spf
                reject_unauth_pipelining, 
                reject_invalid_hostname,
                reject_non_fqdn_hostname,
                reject_non_fqdn_sender, 
                reject_unknown_sender_domain, 
                reject_unauth_destination, 
                reject_unknown_recipient_domain, 
                reject_unknown_reverse_client_hostname,
                reject_unknown_client_hostname,
                #
                # Uncomment check_client_access if you have created a whitelist
                #check_client_access hash:/etc/postfix/whitelist,
                #
                # RBL = Realtime Blackhole List || Reputation Block List (uncomment the ones you want to use)
                reject_rbl_client zen.spamhaus.org,
                #reject_rbl_client multi.uribl.com,
                #reject_rbl_client dsn.rfc-ignorant.org,
                #reject_rbl_client dul.dnsbl.sorbs.net,
                #reject_rbl_client list.dsbl.org,
                #reject_rbl_client sbl-xbl.spamhaus.org,
                #reject_rbl_client bl.spamcop.net,
                #reject_rbl_client dnsbl.sorbs.net,
                #reject_rbl_client cbl.abuseat.org,
                #reject_rbl_client ix.dnsbl.manitu.net,
                #reject_rbl_client combined.rbl.msrbl.net,
                #reject_rbl_client rabl.nuclearelephant.com,
                #reject_rbl_client cbl.abuseat.org,
                #reject_rbl_client psbl.surriel.com,
                #reject_rbl_client dul.dnsbl.sorbs.net,
                #reject_rbl_client bl.spamcop.net,
                reject_non_fqdn_recipient
                permit

NOTE: We have added a commented out line **check_client_access hash:/etc/postfix/whitelist,**. This is for the case when you have created a whitelist of email addresses, IP addresses or domain names that you want to allow to send emails to your server. We will create this whitelist in the next section.


### Whitelist

> Whitelist - is a list of email addresses, IP addresses or domain names that you want to allow to send emails to your server.

- Refs: 
  - https://mailmum.io/posts/whitelisting-hosts-ip-addresses-and-even-networks-in-postfix/
  - https://pieterbakker.com/whitelist-hosts-or-ip-addresses-in-postfix/
  - https://superuser.com/questions/1184728/postfix-only-accept-mails-from-specific-domains


- Create file whitelist inside /etc/postfix folder

      sudo nano /etc/postfix/whitelist

- Add email addresses, IP addresses or domain names that you want to whitelist, on a separate line each. Follow it with **"OK"**. For example:

    ```shell
    1.2.3.4 OK
    1.2.3.5 OK
    [2a02:c123:1234:5a88::1] OK
    mail.exampledomain.com OK
    example.net OK
    email@example.org OK
    email@ OK
    ```

- Save and close the file: **CTRL + S**, **CTRL + X**

- Generate hash table

      sudo postmap /etc/postfix/whitelist

  - Postfix should generate a file called **whitelist.db** in the same directory

- Open Postfix main configuration file

      sudo nano /etc/postfix/main.cf

- Uncomment the line **check_client_access hash:/etc/postfix/whitelist,** in **smtpd_recipient_restrictions** section

- Save and close the file: **CTRL + S**, **CTRL + X**



### Reload Postfix


- Save and close the file: **CTRL + S**, **CTRL + X**
- Verify Postfix configuration

      sudo postfix check

- Restart Postfix

      sudo service postfix restart
 



### SpamAssassin


> **SpamAssassin** - is a free open source anti-spam platform giving system administrators a filter to classify email and block spam (unsolicited bulk email).
> It uses a robust scoring framework and plug-ins to integrate a wide range of advanced heuristic and statistical analysis tests on email headers and body text including text analysis, Bayesian filtering, DNS blocklists, and collaborative filtering databases.
> SpamAssassin is a project of the Apache Software Foundation (ASF).

- Refs:
  - [SpamAssassin - Official Site](https://spamassassin.apache.org/)
  - [Vultr - How To Install SpamAssassin with Postfix on Ubuntu](https://docs.vultr.com/how-to-install-spamassassin-with-postfix-on-ubuntu)
  - [Linuxbabe - Block Spam with SpamAssassin](https://www.linuxbabe.com/mail-server/block-email-spam-check-header-body-with-postfix-spamassassin)
  - https://www.binarytides.com/install-spamassassin-with-postfix-dovecot/


#### Install SpamAssassin


- Update and upgrade packages

      sudo apt update
      sudo apt upgrade

- Install SpamAssassin

      sudo apt install spamassassin spamc

  - **spamc** (option we appended at the end of command) - is a client for SpamAssassin spam filtering daemon

  - **debian-spamd** user and group will be automatically created

> **spamd** - is a SpamAssassin daemon that listens listens on TCP port **783** for scan requests. It is designed to be called from a MTA using the spamc client.

- Enable spamassassin systemd service to auto start at boot time

      sudo systemctl enable spamassassin

- Start spamassassin service

      sudo systemctl start spamassassin

- Check status of spamassassin service

      sudo systemctl status spamassassin

#### Configure Default Settings

- Open SpamAssassin default settings file

      sudo nano /etc/default/spamassassin

- Set Automatic updates of SpamAssassin’s rules on a daily basis - look for line **CRON=0** and change it to **CRON=1**

- Save and close: **CTRL + S**, **CTRL + X**

- Make a backup of the SpamAssassin local configuration file

      sudo cp /etc/spamassassin/local.cf /etc/spamassassin/local.cf.bak

#### Configure Local Settings

- Open SpamAssassin local configuration file

      sudo nano /etc/spamassassin/local.cf

- Paste the following settings or uncomment them if they are already there but commented out

    ```shell
    rewrite_header Subject ***** SPAM _SCORE_ *****
    report_safe             0
    required_score          5.0
    use_bayes               1
    use_bayes_rules         1
    bayes_auto_learn        1
    skip_rbl_checks         0
    use_razor2              0
    use_pyzor               0
    ifplugin Mail::SpamAssassin::Plugin::Shortcircuit
    endif
    ```

- Save and close the file: **CTRL + S**, **CTRL + X**

#### Configure Postfix to Use SpamAssassin

- Open Postfix master configuration file

      sudo nano /etc/postfix/master.cf

- Look for this section

    ```shell
    smtp      inet  n       -       y       -       -       smtpd
    #smtp      inet  n       -       y       -       1       postscreen
    #smtpd     pass  -       -       y       -       -       smtpd
    #dnsblog   unix  -       -       y       -       0       dnsblog
    #tlsproxy  unix  -       -       y       -       0       tlsproxy
    submission inet n       -       y       -       -       smtpd
    ```

- Bellow the smtp configuration, add  a SpamAssassin content filter:

    ```shell
    smtp      inet  n       -       y       -       -       smtpd
    
    # SpamAssassin config - tells Postfix to use SpamAssassin filter when receiving emails
    -o content_filter=spamassassin
    spamassassin unix -     n       n       -       -       pipe
    user=debian-spamd argv=/usr/bin/spamc -f -e  
    /usr/sbin/sendmail -oi -f ${sender} ${recipient}
    ```
  - NOTE: In above config the user is set to debian-spamd - the default user created when installing SpamAssassin

- Save and close the file: **CTRL + S**, **CTRL + X**

- Restart Postfix and enable SpamAssassin to run at system startup

      sudo systemctl restart postfix && \
      sudo systemctl restart spamassassin

- Check that both services are running

      sudo systemctl status postfix && \
      sudo systemctl status spamassassin




## Virtual Mail Forwarding

- Ref:
  - [Postfix.org - Virtual Mail Forwarding](https://www.postfix.org/VIRTUAL_README.html#forwarding)
  - [Tecadmin - Set up mail forwarding](https://tecadmin.net/setup-mail-forwarding-in-postfix-on-linux/)

If we want to automatically forward the message to one or more other email addresses as soon as postfix receives it we can do that by using the feature called **virtual_alias_maps**.


**WARNING:** Do not forward your emails to outside domains if you don't want your server to be blacklisted. If you forward emails to Gmail, Yahoo, Hotmail, etc. and they mark your server as spam, your server will be blacklisted and you will not be able to send emails to those servers. This can happen when someone sends spam email to your email that you are forwarding to Gmail, Yahoo, Hotmail, etc. The SPF signature of the original sender will not match your server and the email will be marked as spam.
Furthermore, that spam email will be bounced back and because we have previously configured our server to use Brevo as a relay to send bounced emails, Brevo will see that you are sending spam and will suspend your account.


- Open **/etc/postfix/main.cf** file

      sudo nano /etc/postfix/main.cf

- Add section **Virtual** at the end of the file:

  ```shell
  # Virtual
  virtual_alias_maps = hash:/etc/postfix/virtual_alias_maps
  ```

- Save and exit the file : `CTRL + S`, `CTRL + X`

- Create a new file **virtual_alias_maps** inside **/etc/postfix** folder

      sudo nano /etc/postfix/virtual_alias_maps

- Add the following lines to the file:

  ```shell
  # List of email forwards
  # Add email forwarding rules as below, one per line
  # {{target_email}} {{destination_email1}} {{destination_email2}} {{destination_email3}} ...

  support@yourdomain.com admin@yourdomain.com jon@yourdomain.com bill@yourdomain.com
  ```
  - Above example rule will ensure that every email sent to email address support@yourdomain.com (which is not registered account) will be forwarded to admin@yourdomain.com jon@yourdomain.com bill@yourdomain.com - Hence why support@yourdomain.com email is called an **alias**, because it's not a registered email address on the server, but it forwards emails to other email addresses.
  - Obviously you should replace the destination emails with your own emails

- Save and exit the file : `CTRL + S`, `CTRL + X`

- Run the following command to create a hash db file from the file virtual_alias_maps:

      sudo postmap /etc/postfix/virtual_alias_maps

- Confirm that you can see the file **virtual_alias_maps.db**
  
      ls -l /etc/postfix

- Reload postfix service to apply changes

      sudo systemctl reload postfix

- Test the email forwarding by sending an email to forwarded target email address. For exapmle in our example above we have created support@yourdomain.com alias, so send an email to support@yourdomain.com
  - You should receive the email at the destination email addresses e.g. admin@yourdomain.com jon@yourdomain.com bill@yourdomain.com


## Virtual Mailboxes

- Refs: 
  - [Postfix.org - Virtual Mailbox Example](https://www.postfix.org/VIRTUAL_README.html#virtual_mailbox)
  - [Serverspace.io - Virtual Mailboxes](https://serverspace.io/support/help/postfix-virtual-mailboxes-ubuntu-20-04/)
  - [Linuxbabe.com - Virtual Mailboxes ](https://www.linuxbabe.com/mail-server/postfixadmin-ubuntu)


We have been able to send and receive emails with our Linux user account  **admin**.

**To have email addresses without associated user accounts**, we have to set up virtual mailboxes.

**WARNING 1:** The moment you set up virtual mailboxes, **your Linux user accounts mailboxes will stop working**. You will realize that you can no longer send emails to your Linux user accounts as the sender emails will recieve a bounce back email with the message **"User unknown in virtual mailbox table"**.

**WARNING 2:**
Before we apply virtual mailboxes settings we need to change mydestination field in /etc/postfix/main.cf file. **We MUST remove "yourdomain.com" from mydestination field** because we will use it in virtual_mailbox_domains field. 

If you don't do this you will get warning in **/var/log/mail.log** like this:

    warning: do not list domain yourdomain.com in BOTH mydestination and virtual_mailbox_domains

- Show the value of mydestination field

      sudo postconf mydestination

- Change mydestination field - remove "yourdomain.com" from the list of values

      sudo postconf -e "mydestination = localhost.\$mydomain, localhost"

Now we will add virtual mailboxes settings.

- Open **/etc/postfix/main.cf** file

      sudo nano /etc/postfix/main.cf

- Add settings to the section **Virtual** that we have created previously:(_**NOTE:** Replace **yourdomain.com** with your actual domain name_)

  ```shell
  # Virtual
  virtual_mailbox_domains = $myhostname
  virtual_mailbox_base = /var/mail/virtual_mailbox_base
  virtual_mailbox_maps = hash:/etc/postfix/virtual_mailbox_maps
  virtual_minimum_uid = 5000
  virtual_uid_maps = static:5000
  virtual_gid_maps = static:5000
  virtual_alias_maps = hash:/etc/postfix/virtual_alias_maps
  ```
  
    - **virtual_mailbox_domains** - list of domains that are served by this mail server
    - **virtual_mailbox_base** - base directory where virtual mailboxes will be stored
    - **virtual_mailbox_maps** - file that contains list of virtual users email addresses and their corresponding mailboxes
    - **virtual_minimum_uid, virtual_uid_maps, virtual_gid_maps** - user and group ids of the system user who will manage the mail folder

- Save and exit the file : `CTRL + S`, `CTRL + X`


- Create user and group **virtualmail** with ID **5000**

      sudo adduser virtualmail --system --group --uid 5000 --disabled-login --no-create-home

  - **--system** - create a system user
  - **--group** - create a group with the same name as the user
  - **--uid 5000** - set user id to 5000
  - **--disabled-login** - do not allow login for this user
  - **--no-create-home** - do not create home directory for this user

  - You will get  output

        Adding system user `virtualmail' (UID 5000) ...
        Adding new group `virtualmail' (GID 5000) ...
        Adding new user `virtualmail' (UID 5000) with group `virtualmail' ...
        Not creating home directory `/home/virtualmail'.

- Create folder **virtual_mailbox_base** with a child folder named same as your domain name (e.g. yourdomain.com) inside **/var/mail** folder: (_**NOTE:** Replace **yourdomain.com** with your actual domain name_)

      sudo mkdir -p /var/mail/virtual_mailbox_base/yourdomain.com

- Make the **virtualmail** user the owner of the **virtual_mailbox_base** folder

      sudo chown -R virtualmail:virtualmail /var/mail/virtual_mailbox_base


- Create a file **virtual_mailbox_maps** inside **/etc/postfix** folder

      sudo nano /etc/postfix/virtual_mailbox_maps

- Add virtual emails mailbox mappings to the file, for example: _(**NOTE:** Replace **yourdomain.com** with your actual domain name)_

  ```shell
  # List of virtual mailboxes
  #
  # Add virtual mailbox rules as below, one per line
  # {{email}} {{path_to_mailbox}}
  #
  # NOTE: Path to mailbox is relative to path set inside virtual_mailbox_base field in main.cf

  contact@yourdomain.com yourdomain.com/contact
  support@yourdomain.com yourdomain.com/support
  ```
  
  - NOTE: Path to mailbox is relative to path set inside virtual_mailbox_base field in main.cf. In our case it is relative to /var/mail/virtual_mailbox_base

- Save and exit the file : `CTRL + S`, `CTRL + X`
- Run the following command to create a hash db file from the file virtual_mailbox_maps:

      sudo postmap /etc/postfix/virtual_mailbox_maps

- List files in /etc/postfix folder to confirm that you can see the file **virtual_mailbox_maps.db**
  
      ls -l /etc/postfix

- Reload postfix service to apply changes

      sudo postfix reload


- Send test message from contact to support email address

      sudo echo "Virtual mailboxes test" | mail -r contact@yourdomain.com -u contact@yourdomain.com -s "Text vmail" support@yourdomain.com

- Check that email is received in support mailbox

      sudo cat /var/mail/virtual_mailbox_base/yourdomain.com/support

Check logs if email not received in support mailbox

- Check log

      sudo tail -f /var/log/mail.log

  - Exit : **CTRL + C**

- Check error log

      sudo tail -f /var/log/mail.err

  - Exit : **CTRL + C**




## 2. MDA - Dovecot

- Refs:
  - https://www.linode.com/docs/guides/running-a-mail-server/
  - https://www.cloudflare.com/en-gb/learning/ssl/transport-layer-security-tls/
  - https://www.cloudflare.com/en-gb/learning/email-security/what-is-smtp/
  - https://www.linuxbabe.com/mail-server/secure-email-server-ubuntu-postfix-dovecot
  - https://www.digitalocean.com/community/tutorials/how-to-set-up-a-postfix-e-mail-server-with-dovecot
  - https://github.com/LukeSmithxyz/emailwiz/blob/master/emailwiz.sh


> **Dovecot** is an open-source IMAP and POP3 server for Unix-like operating systems, written primarily with security in mind.

> **MDA** - Mail Delivery Agent - is a computer program that delivers e-mail messages to a local recipient's mailbox. It receives messages from a Mail Transfer Agent (MTA) and stores them in the recipient's mailbox. Dovecot is an example of an MDA.

In this section we will configure Dovecot IMAP server to allow users to send and receive emails via email clients like Thunderbird, Outlook, etc. 

### Overview of Mail Protocols

> **SMTP** - Simple Mail Transfer Protocol - is a protocol used to **send email messages** from one server to another. SMTP is a text-based protocol that uses port 25 to relay messages between mail servers. SMTP is used to send emails, while IMAP (Internet Message Access Protocol) and POP3 (Post Office Protocol 3) are used to retrieve emails.
> 
> Historically, SMTP only used port 25. Today, port 25 is still in use for SMTP, but it can also use ports 465, 587, and 2525.
> - **Port 25** is most used for connections between SMTP servers. Firewalls for end user networks often block this port today, since spammers try to abuse it to send large amounts of spam.
> - **Port 465** was once designated for use by SMTP with Secure Sockets Layer (SSL) encryption. But SSL was replaced by Transport Layer Security (TLS), and modern email systems "should not" use this port.
> - **Port 587** "should" now be **the default port** for email submission. SMTP communications via this port use **TLS encryption**.
> - **Port 2525** is not officially associated with SMTP, but some email services offer SMTP delivery over this port in case the above ports are blocked.

> **POP3** - Post Office Protocol version 3 - is an older protocol used to receive emails from a remote server to a local email client. Unlike modern protocols that use two-way synchronization, POP3 only supports one-way email synchronization, only allowing users to download emails from a server to a client. Unlike IMAP, which syncs your email across devices, POP3 downloads email messages from the server onto a single device, after which the server no longer retains a copy of the email.
> 
> POP3 runs on **ports 110 or 995** for SSL connections.

> **IMAP** - Internet Message Access Protocol - is a standard protocol used by email clients to **receive email messages** by downloading them from server over a TCP/IP connection. With IMAP accounts, messages are stored in a remote server. Users can log in via multiple email clients on computers or mobile device and read the same messages. All changes made in the mailbox will be synced across multiple devices and messages will only be removed from the server if the user deletes the email.
>
> IMAP runs on **port 143 or 993** for SSL connections.



### Open Ports

We will only open the ports used for secure email communication over IMAP and SMTP. For more details check previous section [Overview of Mail Protocols](#overview-of-mail-protocols)

- Open **port 587** for secure SMTP submission over TLS

      sudo ufw allow 587

- Open **port 465** for secure SMTP over SSL. We need to open this port because some email clients still use it. Microsoft Outlook mail client only supports submission over port 465.

      sudo ufw allow 465

- Open **port 143** for IMAP
      
      sudo ufw allow 143

- Open **port 993** for secure IMAP over TLS

      sudo ufw allow 993


### Enable Submission Service

In order to allow email clients to send emails via our server, we need to enable Submission service in Postfix.

- Open **/etc/postfix/master.cf** file

      sudo nano /etc/postfix/master.cf
 
- Look for section **"submission inet"** and update it to look like this:

```shell
submission inet n       -       y       -       -       smtpd
  -o syslog_name=postfix/submission
  -o smtpd_tls_wrappermode=no
  -o smtpd_tls_security_level=encrypt
  -o smtpd_sasl_auth_enable=yes
  -o smtpd_sasl_type=dovecot
  -o smtpd_sasl_path=private/auth
#  -o smtpd_tls_auth_only=yes
#  -o smtpd_reject_unlisted_recipient=no
#  -o smtpd_client_restrictions=$mua_client_restrictions
#  -o smtpd_helo_restrictions=$mua_helo_restrictions
#  -o smtpd_sender_restrictions=$mua_sender_restrictions
  -o smtpd_recipient_restrictions=permit_mynetworks,permit_sasl_authenticated,reject
  -o smtpd_relay_restrictions=permit_sasl_authenticated,reject
#  -o milter_macro_daemon_name=ORIGINATING
```

Above configuration will allow email clients to send emails via our server over **port 587** using **TLS encryption**.


If you are going to use Microsoft Outlook, then you also need to enable submission service on port 465 by doing the following inside master.cf file:

- Look for section **"smtps inet"** and update it to look like this:

```shell
smtps     inet  n       -       y       -       -       smtpd
  -o syslog_name=postfix/smtps
  -o smtpd_tls_wrappermode=yes
  -o smtpd_sasl_auth_enable=yes
  -o smtpd_sasl_type=dovecot
  -o smtpd_sasl_path=private/auth
#  -o smtpd_reject_unlisted_recipient=no
#  -o smtpd_client_restrictions=$mua_client_restrictions
#  -o smtpd_helo_restrictions=$mua_helo_restrictions
#  -o smtpd_sender_restrictions=$mua_sender_restrictions
  -o smtpd_recipient_restrictions=permit_mynetworks,permit_sasl_authenticated,reject
  -o smtpd_relay_restrictions=permit_sasl_authenticated,reject
#  -o milter_macro_daemon_name=ORIGINATING
```

- Reload postfix service to apply changes

      sudo postfix reload

- Run the following command to confirm that Postfix is listening on port 587 and 465

      sudo ss -lnpt | grep master

  - You should get output like this:

```shell
LISTEN 0      100          0.0.0.0:587        0.0.0.0:*    users:(("master",pid=138116,fd=18))                                                     
LISTEN 0      100          0.0.0.0:465        0.0.0.0:*    users:(("master",pid=138116,fd=22))                                                     
LISTEN 0      100          0.0.0.0:25         0.0.0.0:*    users:(("master",pid=138116,fd=13))                                                     
LISTEN 0      100             [::]:587           [::]:*    users:(("master",pid=138116,fd=19))                                                     
LISTEN 0      100             [::]:465           [::]:*    users:(("master",pid=138116,fd=23))                                                     
LISTEN 0      100             [::]:25            [::]:*    users:(("master",pid=138116,fd=14))     
```


### Configure TLS

SSL = TLS. 

Make sure that your server has a valid SSL certificate. If you don't have one, you can get a free SSL certificate from Let's Encrypt by using Certbot. 
This is described in the previous book **"Linux Web Server"**.

Make sure that TLS for incoming and outgoing connections to Postfix is properly configured.

- Open main.cf file

      sudo nano /etc/postfix/main.cf

- Add the following lines if they are not already there: (_**Replace yourdomain.com with your actual domain name**_)

      # TLS Incoming - Postfix Receives
      smtpd_tls_cert_file=/etc/letsencrypt/live/yourdomain.com/fullchain.pem
      smtpd_tls_key_file=/etc/letsencrypt/live/yourdomain.com/privkey.pem
      smtpd_tls_security_level=may
      smtpd_tls_loglevel = 1
      smtpd_tls_session_cache_database = btree:${data_directory}/smtpd_scache

      # TLS Outgoing - Postfix Sends
      smtp_tls_CApath=/etc/ssl/certs
      smtp_tls_security_level=may
      smtp_tls_loglevel = 1
      smtp_tls_session_cache_database = btree:${data_directory}/smtp_scache

      #Enforce TLSv1.3 or TLSv1.2
      smtpd_tls_mandatory_protocols = !SSLv2, !SSLv3, !TLSv1, !TLSv1.1
      smtpd_tls_protocols = !SSLv2, !SSLv3, !TLSv1, !TLSv1.1
      smtp_tls_mandatory_protocols = !SSLv2, !SSLv3, !TLSv1, !TLSv1.1
      smtp_tls_protocols = !SSLv2, !SSLv3, !TLSv1, !TLSv1.1

- Save and exit the file : `CTRL + S`, `CTRL + X`
- Verify that the configuration is correct

      sudo postfix check

- Reload postfix service to apply changes

      sudo postfix reload

### Install Dovecot

- Install Dovecot

      sudo apt install dovecot-core dovecot-imapd

If you also want to use the old POP3 protocol, you can install it by running the following command:

      sudo apt install dovecot-pop3d

- Check the status of the Dovecot service

      sudo systemctl status dovecot

- Check dovecot version

      dovecot --version




### Enable IMAP / POP3 Protocols

- Open **/etc/dovecot/dovecot.conf** file

      sudo nano /etc/dovecot/dovecot.conf

- Scroll down to the line `# Enable installed protocols` and underneath it add the line:
  
      protocols = imap

  - If you want to use pop3 as well, you should add it so the line will look like this: `protocols = imap pop3`


### Configure Mailbox Location

By default the **mbox format** is used to store emails - location for user’s emails is in a single file **/var/mail/{{USERNAME}}**. 

We will set up dovecot to use **Maildir format**

Ref: https://en.wikipedia.org/wiki/Maildir


- Open dovecot 10-mail.conf

      sudo nano /etc/dovecot/conf.d/10-mail.conf

- Comment out the mail location line and add

  ```shell
  # mail_location = mbox:~/mail:INBOX=/var/mail/%u
  ```
- Add under it the following line to use Maildir format

      mail_location = maildir:~/Maildir   


- Make sure this line is in the file. If not add it

      mail_privileged_group = mail


- Save and exit the file : `CTRL + S`, `CTRL + X`


- Add user **dovecot** to the group **mail**

      sudo adduser dovecot mail

### Set Delivery Agent

Although we configured Dovecot to store emails in Maildir format, by default, Postfix uses its built-in local delivery agent (LDA) to move inbound emails to the message store (inbox, sent, trash, Junk, etc), and it will be saved in mbox format.

We need to configure Postfix to pass incoming emails to Dovecot, via the LMTP protocol, which is a simplified version of SMTP, so incoming emails will saved in Maildir format by Dovecot. LMTP allows for a highly scalable and reliable mail system. It also allows us to use the sieve plugin to filter inbound messages to different folders.

- Install Dovecot LMTP service

      sudo apt install dovecot-lmtpd
      
- Edit main dovecot configuration file

      sudo nano /etc/dovecot/dovecot.conf


- Add lmtp service to the protocols section

      protocols = imap lmtp

- Save and exit the file : `CTRL + S`, `CTRL + X`

- Edit the 10-master.conf file

      sudo nano /etc/dovecot/conf.d/10-master.conf


- Change lmtp service to listen on unix socket

  ```shell
  service lmtp {
    unix_listener /var/spool/postfix/private/dovecot-lmtp {
      mode = 0600
      user = postfix
      group = postfix
    }
  }
  ```
- Save and exit the file : `CTRL + S`, `CTRL + X`

- Edit Postfix main configuration file

      sudo nano /etc/postfix/main.cf

- Scroll down to section `# TRANSPORT` that we created previously and add the following lines:

  ```shell
  mailbox_transport = lmtp:unix:private/dovecot-lmtp
  smtputf8_enable = no
  ```

  - **mailbox_transport** - tells Postfix to use the LMTP protocol to deliver emails to Dovecot
  - **smtputf8_enable** - tells Postfix to disable the SMTPUTF8 feature. This feature is not supported by Dovecot-LMTP.
  

- Save and exit the file : `CTRL + S`, `CTRL + X`


### Configure Dovecot Authentication

- Edit the 10-auth.conf file

      sudo nano /etc/dovecot/conf.d/10-auth.conf

- Uncomment the following lines:

  ```shell
  disable_plaintext_auth = yes
  auth_mechanisms = plain login
  ```
      
  - **disable_plaintext_auth** - prevents users from logging in with plaintext passwords when there’s no SSL/TLS encryption
  - **auth_mechanisms** - specifies the authentication mechanisms that Dovecot supports. We are using plain and login mechanisms.

- Search for the line **auth_username_format** : CTRL + W, type `auth_username_format` and press ENTER

- Uncomment and change auth_username_format to look like this:

      auth_username_format = %n

  - This setting tells Dovecot to use the username without the domain part. This is necessary because Postfix will pass the username without the domain part to Dovecot.


- Save and exit the file : `CTRL + S`, `CTRL + X`


### Configure Dovecot SSL/TLS Encryption

- Edit the 10-ssl.conf file

      sudo nano /etc/dovecot/conf.d/10-ssl.conf

- Change `ssl = yes` to `ssl = required` to enforce encryption

      ssl = required

- Set certificate and private key paths: (_**Replace yourdomain.com with your actual domain name**_)

  ```shell
  ssl_cert = </etc/letsencrypt/live/yourdomain.com/fullchain.pem
  ssl_key = </etc/letsencrypt/live/yourdomain.com/privkey.pem
  ```

- Set prefer server ciphers

      ssl_prefer_server_ciphers = yes

- Disable insecure SSLv3, TLSv1 and TLSv1.1 protocols - uncomment the following line:

      ssl_min_protocol = TLSv1.2


- Save and exit the file : `CTRL + S`, `CTRL + X`


### Disable the FIPS

REf: https://ubuntu.com/security/certifications/docs/fips

Ubuntu 22.04 ships with OpenSSL 3.0, which features a FIPS provider. 
FIPS does not work with Dovecot, so we need to disable it.


- Edit openssl.cnf file

      sudo nano /etc/ssl/openssl.cnf

- Comment out this line `openssl_conf = openssl_init`. It should look like this:

      # openssl_conf = openssl_init

- Save and exit the file : `CTRL + S`, `CTRL + X`

If you don’t disable the FIPS provider in OpenSSL, Dovecot will produce the following error:

      imap-login: Error: Failed to initialize SSL server context: Can't load SSL certificate: error:25066067:DSO support routines:dlfcn_load:could not load the shared library: filename(libproviders.so)


### Configuring SASL Authentication


- Edit the 10-master.conf file

      sudo nano /etc/dovecot/conf.d/10-master.conf

- Update section `service auth` to contain the following `unix_listener` block of lines:

  ```shell
  service auth {
    unix_listener /var/spool/postfix/private/auth {
      mode = 0660
      user = postfix
      group = postfix
    }
  }
  ```
  - This ensures that Postfix can find the Dovecot authentication server


- Save and exit the file : `CTRL + S`, `CTRL + X`

### Auto Create Folders


- Edit the 15-mailboxes.conf file

      sudo nano /etc/dovecot/conf.d/15-mailboxes.conf

  - You will see `mailbox` sections inside `namespace inbox` block, like : `mailbox Junk {` and `mailbox Sent {`

- Add line `auto = create` to each mailbox section like this:

  ```shell
  mailbox Junk {
    auto = create
    special_use = \Junk
  }
  mailbox Trash {
    auto = create
    special_use = \Trash
  }
  mailbox Sent {
    auto = create
    special_use = \Sent
  }
  mailbox Drafts {
    auto = create
    special_use = \Drafts
  }
  ```

- Save and exit the file : `CTRL + S`, `CTRL + X`

### Restart Postfix and Dovecot

- Check Dovecot configuration

      sudo doveadm config test


- Restart Postfix 

      sudo service postfix restart

- Restart Dovecot

      sudo service dovecot restart

NOTE: This may take a minute to restart. If the cursor is hanging for a long time, hit **CTRL + C**

- Check the status of the Dovecot service

      sudo service dovecot status

- Check what ports Dovecot is listening on

      sudo ss -lnpt | grep dovecot

  - You should see output like this:

```shell
LISTEN 0      100          0.0.0.0:143        0.0.0.0:*    users:(("dovecot",pid=152123,fd=35))                                                    
LISTEN 0      100          0.0.0.0:993        0.0.0.0:*    users:(("dovecot",pid=152123,fd=37))                                                    
LISTEN 0      100             [::]:143           [::]:*    users:(("dovecot",pid=152123,fd=36))                                                    
LISTEN 0      100             [::]:993           [::]:*    users:(("dovecot",pid=152123,fd=38)) 
```

We can see that Dovecot is listening on ports 143 and 993 for IMAP and IMAPS respectively.

### Set up Automatic Restart

- Create folder for dovecot service override

      sudo mkdir -p /etc/systemd/system/dovecot.service.d

- Create file to override the service

      sudo nano /etc/systemd/system/dovecot.service.d/restart.conf

- Add code to restart 5 seconds after failure

  ```shell
  [Service]
  Restart=always
  RestartSec=5s
  ```

- Save and exit the file : `CTRL + S`, `CTRL + X`

- Reload the systemd daemon

      sudo systemctl daemon-reload

- Test if auto-restart works - Kill the dovecot service

      sudo pkill dovecot

- Check the status of the Dovecot service

      sudo service dovecot status

  - You should see that the service is not active

- Wait for 5 seconds and check the status of the Dovecot service again

      sudo service dovecot status

  - You should see that the service is active

## 3. Email Client

### Desktop Client - Thunderbird

- On your personal computer - Download Thunderbird to your desktop from the official website: https://www.thunderbird.net

- Install Thunderbird on your computer

- Open Thunderbird

- Set up account by providing your name, email address and password and click **Continue**

  - This should work and Thunderbird will automatically detect the settings for your email account

If Thunderbird does not automatically detect the settings, you can manually configure the account by providing the following settings:

#### Incoming Server (IMAP):
- **Server Type**: IMAP Mail Server
- **Server Hostname**: yourdomain.com
- **Port**: 993
- **Connection Security**: SSL/TLS
- **Authentication**: Normal Password
- **Username**: admin
- **Password**: your-password

#### Outgoing Server (SMTP):
- **Server Hostname**: yourdomain.com
- **Port**: 587
- **Connection Security**: STARTTLS
- **Authentication Method**: Normal Password
- **Username**: admin
- **Password**: your-password

**Note**: If port 587 with STARTTLS doesn't work, you can try port 465 with SSL/TLS as an alternative.

### Mobile Client - Edison Mail





### Problem - Certificate Expired

**PROBLEM:**

When your SSL certificate is updated on the server it will not be automatically updated in dovecot.

Therefore Thunderbird will pop up message in the bottom right : `the certificate for mysite1.com expired` - where "mysite1.com" is the domain name of your server.

**TEMPORARY FIX:**

- Connect to your server : `ssh mysite1.com`
- Reload Dovecot : `sudo systemctl reload dovecot`
- Confirm Dovecot status is active: `sudo systemctl status dovecot`
- Restart email client on your device for example:
  - Thunderbird on your desktop
  - Edison Mail on your mobile

**PERMANENT SOLUTION:**

To automatically reload Dovecot when certificates are renewed, create a post-renewal hook:

- Create a post-renewal script:

  ```bash
  sudo nano /etc/letsencrypt/renewal-hooks/post/dovecot-reload.sh
  ```

- Add the following content to the script:

  ```bash
  #!/bin/bash
  # Reload Dovecot after certificate renewal
  systemctl reload dovecot
  echo "Dovecot reloaded after certificate renewal: $(date)" >> /var/log/letsencrypt/dovecot-reload.log
  ```

- Make the script executable:

  ```bash
  sudo chmod +x /etc/letsencrypt/renewal-hooks/post/dovecot-reload.sh
  ```

- Test the script manually:

  ```bash
  sudo /etc/letsencrypt/renewal-hooks/post/dovecot-reload.sh
  ```

  The script runs silently with no output. To verify it worked:

  ```bash
  # Check if Dovecot is still running
  sudo systemctl status dovecot

  # Check if the log file was created
  sudo cat /var/log/letsencrypt/dovecot-reload.log
  ```

  You should see:
  - Dovecot status showing "active (running)"
  - A log entry with today's date and time

  If the log file doesn't exist, check permissions:

  ```bash
  # Check if the script has execute permissions
  ls -la /etc/letsencrypt/renewal-hooks/post/dovecot-reload.sh

  # Check if the letsencrypt log directory exists and has write permissions
  ls -la /var/log/letsencrypt/
  ```

Now Dovecot will automatically reload whenever your SSL certificates are renewed by certbot.



## Dictionary

|              |                                                                                                                                                                                                                                                                                                                                                                          |
| ------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **MTA**      | Mail transfer agent, a simple mail transfer protocol (SMTP) server such as Postfix, Exim, and Sendmail. SMTP servers talk to each other                                                                                                                                                                                                                                  |
| **MUA**      | Mail user agent, your local mail client such as Evolution, KMail, Claws Mail, or Thunderbird.                                                                                                                                                                                                                                                                            |
| **POP3**     | Post-office protocol, the simplest protocol for moving messages from an SMTP server to your mail client. A POP server is simple and lightweight; you can serve thousands of users from a single box.                                                                                                                                                                     |
| **IMAP**     | Interactive message access protocol. Most businesses use IMAP because messages remain on the server, so users don’t have to worry about losing them. IMAP servers require a lot of memory and storage.                                                                                                                                                                   |
| **TLS**      | Transport socket layer, an evolution of SSL (secure sockets layer), which provides encrypted transport for SASL-authenticated logins.                                                                                                                                                                                                                                    |
| **SASL**     | Simple authentication and security layer, for authenticating users. SASL does the authenticating, then TLS provides the encrypted transport of the authentication data.                                                                                                                                                                                                  |
| **StartTLS** | Also known as opportunistic TLS. StartTLS upgrades your plain text authentication to encrypted authentication if both servers support SSL/TLS. If one of them doesn’t then it remains in cleartext. StartTLS uses the standard unencrypted ports: 25 (SMTP), 110 (POP3), and 143 (IMAP) instead of the standard encrypted ports: 465 (SMTP), 995 (POP3), and 993 (IMAP). |
| **FQDN**     | Abreviation for **Fully Qualified Domain Name** e.g. yourdomain.com                                                                                                                                                                                                                                                                                                      |
| **VPS**      | Abreviation for **Virtual Private Server**                                                                                                                                                                                                                                                                                                                               |
|              |                                                                                                                                                                                                                                                                                                                                                                          |

# >>>>>>>>>>>>> PROOF-READ <<<<<<<<<<<<<<<<<



## Landing in the Inbox - Reputation


Ref: https://www.linuxbabe.com/mail-server/how-to-stop-your-emails-being-marked-as-spam

- Google Postmaster Tools: https://postmaster.google.com/managedomains

## Troubleshooting

#### Use Google Admin Toolbox - Check MX

- Go to https://toolbox.googleapps.com/apps/checkmx/
- Enter your domain name and click **RUN CHECKS!**

#### Wrong Hostname in the mail header

If you have changed server hostname to your FQDN (fully qualified domain name e.g. yourdomain.com) after the Postfix has already been installed, then your emails will be sent with your old host name in the header.

If you read the message in the /var/mail/{{username}} look for this part

    
    This is a MIME-encapsulated message.

    --7A1BDFE8A4.1669237076/my-old-server-name
    Content-Description: Notification
    Content-Type: text/plain; charset=utf-8
    Content-Transfer-Encoding: 8bit

You will see your old server name.

- To fix email headers to show your new server name corresponding to your domain name, open main.cf

      sudo nano /etc/postfix/main.cf

- Remove my-old-server-name and replace with my current server name e.g. yourdomain.com
  - You should now have changed these two lines to contain your FQDN e.g. yourdomain.com

        myhostname = yourdomain.com
        mydestination = $myhostname, yourdomain.com, localhost.localdomain, localhost

- Your messages will now contain correct server name in the headers

#### Wrong Reverse DNS

Read the message in the /var/mail/{{username}} look for this part

    Reporting-MTA: dns; my-old-server-name

If it does not contain your FQDN e.g. yourdomain.com , which is same as your new server name, that means you did not configure properly your Reverse DNS / PTR.

In this case go to [Configure Reverse DNS](#5-set-ptr-record--reverse-dns) section and do as explainedin there.