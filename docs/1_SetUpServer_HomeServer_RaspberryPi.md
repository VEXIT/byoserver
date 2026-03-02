# Linux Server - Home Server - Raspberry Pi


|               |                                                   |     |
| ------------- | ------------------------------------------------- | --- |
| Copyright:    | © 2022 VEXIT , Tomorow is today ® , www.vexit.com |     |
| Author:       | Vex Tatarevic                                     |     |
| Date Created: | 2022-11-23                                        |     |
|               |                                                   |     |


## Definitions

In this book we will use technical words that you may not be familiar with. 

Below is a table explaining these words.

|           |                                                                                                                                                                                    |
| --------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Client    | Your personal computer: Windows, Mac or Linux, that you use to connect remotely to home server                                                                                     |
| Server    | Computer that we are using as a home server machine                                                                                                                                |
| Directory | Folder                                                                                                                                                                             |
| Machine   | Computer                                                                                                                                                                           |
| Terminal  | Program used for inputing commands to interact with operating system. On Windows we use **Git Bash** terminal program. On Linux and macOS, terminal program is called **Terminal** |


## Obtain Static IP Address from ISP

By default, your **ISP (internet service provider)** will assign your local network a **Dynamic IP Address** - which keeps on changing all the time.

In order for your server computer to be reachable from outside world (by other computers on the internet), it must have a **Static IP Address**.

- Contact your ISP and ask them to upgrade your internet connection plan to one that provides a **Static IP Address**


## Install Client Software

- Log into your [Client computer](#definitions). This is the computer you use every day. We will use this computer to connect remotelly to our server at the later stage.

### Install Git Bash

- On your [Client computer](#definitions)
- Open the browser and go to [git-scm.com](https://www.git-scm.com/).
- Download and install Git

### Install Telnet

On **Windows**:
- Open **Control Panel**
- Click on **Programs**
- Click on **Turn Windows features on or off**
- Check the box next to **Telnet Client**
- Click **OK**
- Telnet is now installed

On **macOS**:
- Open Terminal: CMD + Space > type Terminal > Enter
- Install Telnet

      brew install telnet

  - If you don't have **brew** installed, go to [brew.sh](https://brew.sh) and follow the instructions to install it.

On **Linux**:
- Open Terminal: CTRL + ALT + T
- Install telnet

      sudo apt install telnet




## Prerequisites

- Buy a **Raspberry Pi computer**. Go to [Raspberrypi.com](https://www.raspberrypi.com) for more info.
- Buy **Micro SD card** of minimum 32 Gb memory size
- **SSD (solid state drive) disc** for storage (ideally 2 terabytes), but for practice you can even use a USB drive

- Why choose Raspberry Pi ? 
  - **Small** - It is the smallest server computer in the world. It is only the size of your palm
  - **Portable** - If there's a flood, fire, war, earthquake or any kind of disaster happens around you and you need to quickly move, you simply unplug your Raspberry Pi, put it in your pocket and run. It's that simple.
  - **Reliable** - you can simply plug it in the power, leave it there and forget about it. You can turn the power on or off in the wall whenever and it will not affect the Pi. The Pi just keeps running.
  - **Low power consumption** - It uses very little electricity. It costs only $5 per year to run it 24/7.



## Install Raspberry Pi OS

### Download and Install Raspberry Pi OS

- On your [Client computer](#definitions)
- Go to Raspberry Pi OS download page: [www.raspberrypi.org/software](https://www.raspberrypi.org/software)

- Download and install **"Raspberry Pi Imager"**
  - This program is used to install the operating system onto the MicroSD Card

- Plug the **Micro SD card** into your Client PC. 
  - If you don't have a Micro SD card slot on your computer, you will have to use **MicroSD to USB adapter**.

- Run the **"Raspberry Pi Imager"** program:
	- Under **Operating System**, Click **"CHOOSE OS"** 
	- Select **"Raspberry Pi OS (other)"** 
	- Scroll down and select **"Raspberry Pi OS (64-bit)"** - A port of Debian Bullseye with the Raspberry Pi Desktop
	- Under **Storage**, Click **"CHOOSE STORAGE"**, then select your SD card
	- Click **"WRITE"** and wait for the process to complete

- Remove the MicroSD card from your PC and insert it into your Raspberry Pi

- Connect keyboard, mouse and monitor to [Server](#definitions) (Raspberry Pi).

- Turn on the [Server](#definitions)
- You will see the first screen of the setup wizard with the text:

      Welcome to the Raspberry Pi Desktop!

> **IMPORTANT:** If you are using Bluetooth keyboard and mouse, make sure to put them in pairing mode on the very first screen of the setup wizard and wait for the Connect popup to come up. Do not progress to the next wizard screen until your keyboard has been connected, because you will not be able to type in the text or connect the keyboard at the later screens.


- Walk through installation wizard by clicking **Next**

- On **Set Country** screen:
  - Set your Country and Timezone
  - Check Use English language
  - Check use US keyboard

- On **Create User** screen, set traditional default username and password :
  - Username: **main-user**
  - Password: **main**

- You may get the warning popup

      You have used a known default value for the username or password.
      We strongly recommend you go back and chose something else.

  - Don't wory about this. We will change the username and password later.

- On **Select WiFi Network** screen, select your wireless network from the list and provide password so that server computer can connect to the internet.

- On **Update Software** click **Next** to download the latest software.
- At the end of the wizard you should be prompted to restart the computer.
- After the computer restarts, you will see the Desktop with the menu at the top.

### Change Boot Mode

Raspberry Pi computer can boot in one of 2 modes:

1. [Desktop Boot](#option-1-desktop-boot---terminal) - computer runs in graphic user interface mode.

2. [CLI Boot](#option-2-cli-boot) - computer runs only in command line interface mode and you cannot access any graphic user interface.

**It is highly recommended to use Desktop Boot mode**, because it is easier to use and you can use comand line interface by opening Terminal program.

#### Changing to CLI-Only Boot Mode

- Use this option only if 
  - you want to use Raspberry Pi computer as a headless server (without monitor, keyboard and mouse) - in this case probably you will be using SSH to connect to your server remotely from another computer, so better wait until you set up SSH connection before changing to CLI-Only Boot mode.
  - you want to save resources by not running Desktop interface
  - you are comfortable using command line interface only.

- Click on : **Start (Top-Left Raspberry Pi Icon) > Preferences > Raspberry Pi Configuration**

- Change the setting **Boot** to '**To CLI**' and click OK

- Click Menu > Shutdown > Reboot , to restart the computer

- The computer should restart and you will see the [command line prompt](#command-line-prompt):

	  main-user@raspberrypi:~ $ _

#### Changing to Desktop Boot Mode

- `sudo raspi-config` **> System Options > Boot / Auto Login > Desktop > OK > Reboot**

In the next section read in more detail instructions on how to open and use raspi-config.

### Open/Close raspi-config 

Raspi-config is Raspberry Pi Configuration Tool. 

- **Open raspi-config** - type in the following command

      sudo raspi-config

- Use the up and down arrow keys to move the highlighted selection between the options available. Pressing the right arrow key will jump out of the Options menu and take you to the **< Select >** and **< Finish >** buttons. Pressing left will take you back to the options. 

- If arrows do not work, then use the **Tab** key to move from one section to another.

- **Close raspi-config**
  - Hit **ESC** key

    OR 
  - Move to **< Finish >** and hit Enter
<br>

### Change Keyboard Layout

**YOU CAN SKIP**

> **NOTE:** You can skip this section and go to [Change Autologin to Require Login](#change-autologin-to-require-login) because you have already configured keyboard to **"use US keyboard"** in the Setup Wizard. This **"Change Keyboard Layout"** section was for the users who were installing Raspberry Pi from old command line interface installation and is no longer relevant.

By default Raspberry Pi uses **English (UK)** keyboard layout which has a problem with displaying pipe symbol **"|"** in a standard way. It instead displays tilde symbol **"~"**.
<br>
Because we need "|" (pipe symbol) for some of the commands, we will change keyboard layout to standard US keyboard layout.

NOTE: If you are using Desktop view and you get problems with navigating to Keyboard layout via Terminal CLI, you can do it via Desktop menu instead: 
- **Start > Preferences Keyboard and Mouse > Keyboard > Keyboard Layout > Model: Generic 105-key PC (intl.) > Layout English (US)**

To configure Keyboard in CLI (command line interface):
- [Open raspi-config](#openclose-raspi-config)
- Select: **5 Localisation Options > L3 Keyboard**
  - Use **Down Arrow** to move down to **"5 Localisation Options"**, then hit **Enter** key
  - Use **Down Arrow** to move down to **"L3 Keyboard"**, then hit **Tab** key to focus on **< Select >** button, then hit **Enter** key to select

  - You should see **Keyboard model** page with **Generic 105-key PC** selected
  - Hit **Enter** to continue to **Keyboard layout** 
  - On **Keyboard layout** it will have selected **English (UK)**
  - On **Keyboard layout** page scroll-down and select **Other**
  - Select **English (US)** > Hit Enter > Scroll-up > Select **English (US)** > Hit Enter

- On the next pages hit **Enter** to select defaults, until you return back to main screen with **System Options** selected

- Hit **Tab** 2 times to select **Finish** 
- Hit **Enter** to exit raspi-config
- <vxm-kbd>CTRL + L</vxm-kbd> to clear the screen

### Change Autologin to Require Login

By default your Raspberry Pi will be set to log you in automatically. This is unsafe. We want to change this so that login is required every time.

> **HINT:** In command line interface you can select previously typed-in commands by hitting **"UP-ARROW"**

- [Open raspi-config](#openclose-raspi-config) 
- Select:  **System Options > Boot / Auto Login** 
  - If you are using **Desktop** interface : Select **B3 Desktop** > Hit **Enter**

    OR

  - If you are using **CLI** interface : Select **B1 Console** > Hit **Enter**
- You will be taken to main config screen
- Hit **Tab** > **Tab** to select **< Finish >** button, then hit **Enter**
- You should see "**Would you like to reboot now?**" . Select **< Yes >** and hit **Enter**

The computer will restart and then you will be presented with login interface.

Now Log in with:
- Username: **main-user**
- Password: **main**

### Set up Wi Fi

**YOU CAN SKIP**

**NOTE:** You can skip this section as your WiFi has already been configured in the Setup Wizard. 

- Refs: 
	- Settig up WiFi: https://www.raspberrypi.org/documentation/configuration/wireless/wireless-cli.md
 

- [Open raspi-config](#openclose-raspi-config)

      sudo raspi-config

- Select: **5 Localisation Options > WLAN Country** 
	- Select your country > Enter > Reboot
	
- After reboot > log in > run : sudo raspi-config 

- Select: **1 System Options > S1 Wireless LAN > Enter SSID (means type-in WiFi name) > Hit Enter > Type-in WiFi password > Hit Enter > Hit Esc (to exit) > CTRL + L (to clear the screen)**


## Start Using Command Line Interface

> **CLI (Command Line Interface)** - is a name for any command line program that accepts text input to execute operating system functions.

From this point on we will configure the system via command line interface.


- **Open Terminal** with keyboard shortcut : <vxm-kbd>CTRL + ALT + T</vxm-kbd>

- This will open the Terminal command line interface and you will see the [command line prompt](#command-line-prompt): 

      main-user@raspberrypi:~ $

- You can now type the commands.

- **Close Terminal** - type command `exit` to close the Terminal


 
## Update and Upgrade system

**YOU CAN SKIP**

**NOTE:** You can skip this section as your system has already been updated and upgraded in the Setup Wizard.

- Update system, type command:

	  sudo apt-get update

- Upgrade system, type command:

	  sudo apt-get upgrade

## Shut Down Safely

When turning off your Server, you should always shut down safely.

- To safely turn off your Server, type in the following command

	  sudo shutdown -h now

  - The **-h** option will halt the working processes. If you remove -h from the above command, it will directly shutdown the Raspberry Pi without waiting for anything, which is not recommended.

- To shut down after 10 minutes type

	  sudo shutdown -h 10

- To cancel scheduled shutdown

		shutdown -c

## Configure the System
<br>

Refs: 

- Using Raspi Config:  https://www.raspberrypi.org/documentation/configuration/raspi-config.md



### Set up Wi-Fi

**YOU CAN SKIP**

> **NOTE:** You can skip this WiFi setup section, because your Wi-Fi should have already been set in the Setup Wizard. This section shows you how to set up WiFi in the command line interface on any version of Linux operating system, including Raspberry Pi OS.

- Get the name of wireless network interface

      iwconfig

  - You should get something like:

        lo        no wireless extensions.

        eth0      no wireless extensions.

        wlan0     IEEE 802.11  ESSID:"TPG-5G-1C2A"  
                  Mode:Managed  Frequency:5.18 GHz  Access Point: 00:1C:2A:1C:2A:1C   
                  Bit Rate=6 Mb/s   Tx-Power=31 dBm   
                  Retry short limit:7   RTS thr:off   Fragment thr:off
                  Power Management:on
                  Link Quality=70/70  Signal level=-32 dBm  
                  Rx invalid nwid:0  Rx invalid crypt:0  Rx invalid frag:0
                  Tx excessive retries:0  Invalid misc:0   Missed beacon:0
    
    - **wlan0** - is the name of the wireless network interface. 
    - **ESSID** (Extended Service Set Identifier) - is the name of your wireless network. In above example it is **TPG-5G-1C2A**
    - ESSID should be "off/any" if the wireless interface has not been activated yet.
    
- Activate wirelessnetwork interface

      sudo ifconfig wlan0 up

- Connect to your wireless network using WPA (Wi-Fi Protected Access) encryption

      sudo wpa_passphrase {{WIFI_NAME}} {{WIFI_PASSWORD}} > /etc/wpa_supplicant.conf

  - Replace {{WIFI_NAME}} and {{WIFI_PASSWORD}} with your actual WiFi name and password

- Check that your WiFi details have been saved:

		sudo nano /etc/wpa_supplicant/wpa_supplicant.conf

	- You should see

			country={{YOUR_COUNTRY_CODE}}

			network={
				ssid={{YOUR_WIFI_NAME}}
				psk={{YOUR_WIFI_PASSWORD}}
			}

- Reboot

      sudo reboot

- Check that you are connected to your WiFi

### Enable/Disable root user 

<definition>

>**root** account is a superuser account that exists in system by default and it can execute any priviledged actions without using prefix **sudo**.
</definition>

It is not advisable to ever enable **root** account but there are some situation when we may need it.

#### Enable root


- Log in as **main-user**

- Enable "**root**" user

	  sudo passwd root

  - This will prompt you for password

- Enter password **root**

- Reboot (restart computer)

	  reboot

- Log in with username **root** and password **root**

> **NOTE:** If using the Dektop view, when you log in with **root**, you will not see the menu at the top. In this case, hit <vxm-kbd>CTRL + ALT + T</vxm-kbd> to open the **Terminal**. Now you will be able to execute the commands with root.

- Execute priviledged command "apt-get update"

      apt-get update

  - This time we did not have to use prefix sudo

> **IMPORTANT:** Make sure to disable the root user after you complete priviledged actions available only to root user

- Reboot computer

      reboot

#### Disable root

- Log in with non-root account

- Disable **root** account

	  sudo passwd -l root

- Restart computer

      reboot
      
<br>

### Change Username and Group Name


In this section we will change initially created username **"main-user"** and password **"main"** to our new username and password.
 
We cannot change username of main-user while logged in as **main-user** and the only other user in the system is super user account called **root**.

This is the only reason why we will enable user root in this unique situation. Normally root user should always be disabled and instead all the commands that require root priviledges, can be executed by prefixing them with keyword **sudo**.

After we are finished with executing priviledged actions using root account, we must disable the **root** account.
<br/>

- Log in as **main-user**

- **Enable root account** and reboot

      sudo passwd root
      sudo reboot

- Log in as **root**

- If in Desktop view : Open Terminal <vxm-kbd>CTRL + ALT + T</vxm-kbd>
  
- **Change username** **main-user** to your desired username (We will call it "my-user" but you can call it whatever you like)

	  usermod -l my-user main-user

    > **NOTE:** THROUGHOUT THIS BOOK WE WILL REFER TO YOUR USERNAME AS "**my-user**". Replace all the occurences of my-user with your own personal username.

- **Change user's directory** name to my-user

	  usermod -m -d /home/my-user my-user

- **Change user's group** name to my-user

      groupmod -n my-user main-user


- **Reboot**

      reboot

- Log in as **"my-user"**, with password **"main"**

- **Change password** to a very strong password

	  passwd

- Confirm that **my-user** account has sudo privileges, by running system update command:

	  sudo apt-get update

  - You will be prompted for password

        [sudo] password for my-user:

  - Type in password to continue

> **NOTE:** When we changed the username main-user to my-user, it has reset the way sudo behaves to default - asking for password every 15 minutes.
In the next section we will configure sudo not to prompt **my-user** to enter password every time we want to run a priviledged command.

- **IMPORTANT:** **Disable** **root** account

	  sudo passwd -l root

> **NOTE:** From this point forward we will not use **root** account again.


### Remove Sudo Password Prompt

When we want to run priviledged commands we type **sudo** before the command. For this to work, our user account must be a member of **sudo** **group** - which our default account is.

By default, using sudo asks for password every 15 minutes

To disable asking for sudo password, do the following:

- Open **sudoers** file for editing using **visudo** command (visudo command is a safe and secure way of editing the /etc/sudoers file)

      sudo visudo

- Look for the line starting with **%sudo**. It should look like this:

      # Allow members of group sudo to execute any command
      %sudo   ALL=(ALL) ALL

- Add keyword **NOPASSWD** and a column symbol before the last **ALL**. It should now look like this

      # Allow members of group sudo to execute any command
      %sudo   ALL=(ALL) NOPASSWD:ALL

- Save and close the file: <vxm-kbd>CTRL + S</vxm-kbd> <vxm-kbd>CTRL + X</vxm-kbd>

- Reboot and log in with **my-user**

- Run update command to make sure you are not being prompted for password:

      sudo apt-get update

  - You should NOT be prompted for password this time

### Configure Users' Home Folder Security

When you create a new user in Raspberry Pi operating system, he can see all other users' folders by going inside **/home** folder.

Then a user can enter any other user's folder and read and execute any files.

This is not a very secure set-up for a serious multi user information system.

Let's demonstrate this insecure setup practically and then we will apply the fix to make it more secure.

#### Demonstrate System Insecurity

> NOTE: You can skip to next section [Fix System Insecurity](#fix-system-insecurity) . This section is just a demonstration for understanding of the problem.

- Log in as **my-user**
- Create a **sample file** inside user's home directory
  - Go inside user's home folder

        cd ~/

  - Create file **"my-secure-file.txt"** in the root of my-user'shome folder

        touch my-secure-file.txt

  - Open my-secure-file.txt in nano editor

        nano my-secure-file.txt

  - Write inside it the text: **"My secure content..."**
  - Save and close the file: <vxm-kbd>CTRL + S</vxm-kbd> <vxm-kbd>CTRL + X</vxm-kbd>

- Create user **testuser**

      sudo adduser testuser

  - When prompted for password type "test"
  - For all other prompts hit <vxm-kbd>Enter</vxm-kbd>
  - On the last prompt "Is the information correct? [Y/n]" , type  "y" and hit Enter

- Reboot
- Log in as **testuser** 
- Go inside **/home** folder

      cd /home

- List all the files and folders inside the /home
  
      ls -l

  - You should see the folders: testuser, my-user

> A secure system should not show other users' home folders

- Attempt to navigate inside the folder of the user **my-user** that is not currently logged in and list the contents of that folder:

      cd my-user
      ls -l

  - You should see a list of user's folders and a file my-secure-file.txt

> A secure system should not allow access to files and folders of other users

- Open file my-secure-file.txt

      nano my-secure-file.txt

  - The file will be opened and you will see "**My secure content...**"

> A secure system should not allow viewing the contents of other users' files

Clean up the test data:

- Reboot the Server
- Log in as **my-user**
- Delete **testuser** account (if you have created it in previous section)

      sudo userdel -r -f testuser

  - You will get a message "userdel: testuser mail spool (/var/mail/testuser) not found" . Don't worry about that message. It just means that there was no mail folder found when deleting this user. 
  - The user has been deleted
- Go inside the /home directory and list  the contents. You will see that testuser folder has disappeared:

      cd /home
      ls -l

- Delete the temporary file "my-secure-file.txt" that we created

      rm /home/my-user/my-secure-file.txt

In the next section we will fix the above system insecurities in a way that a security policy will be applied to any new users that get created in the system.

#### Fix System Insecurity

> NOTE: We will use **chmod** command to change permissions on user folders. For now you don't need to understand how chmod works. We will explain this in the later sections. 

There are 3 things we need to do to secure our system. Go ahead and do the following:

1. Hide all the folders inside the **/home** folder:

       sudo chmod 701 /home

   - Users will now NOT be able to see other users' folders inside /home
   - Users will still be able to access other users' folders if they know the location of the folder e.g. /home/my-user. We will fix this in the next 2 steps

2. Change permissions on our user's folder, to prevent access by other users

       sudo chmod 750 /home/my-user

3. Change the default permission assigned to user's folder automatically when user is created using **adduser** command. The default permission is 755 - means anyone can read and execute the files in any user's folder. We want to change it to 750 - means other users have no access to user's folder at all.
   - Open **adduser.conf**

         sudo nano /etc/adduser.conf 

   - Scroll down to **"DIR_MODE=0755"** and change it to **"DIR_MODE=0750"**

   - Save and close the file: <vxm-kbd>CTRL + S</vxm-kbd> <vxm-kbd>CTRL + X</vxm-kbd>

Now only the owner of the folder and the group assigned to folder will be able to access it. All others will not be able to, unless of course they have root priviledges, then they can access it with "sudo".


#### CHROOT Jail

https://phoenixnap.com/kb/chroot-jail

https://www.tecmint.com/restrict-ssh-user-to-directory-using-chrooted-jail/



### Create User Admin

In the previous sections we showed you how to change existing username and password.

Now we will learn how to create a priviledged account called **admin**. 


> **IMPORTANT:** **Disable** **root** account - in case you missed to do it before. 

	  sudo passwd -l root

> **NOTE:** **my-user** is already a priviledged account, so we can execute any priviledged commands with my-user account by prefixing them with keyword **sudo**

- Log in with **my-user**

- **Create user admin**

	  sudo adduser admin
      
  - Provide password: **admin** and Full Name: **Admin**. For all other fields hit **Enter** to continue.

  - The last question of the user creation wizard will ask you if the information is correct. Type **y** and hit **Enter**.

  - You should see :
		
		  Adding user `admin' ...
		  Adding new group `admin' (1001) ...
		  Adding new user `admin' (1001) with group `admin' ...
		  Creating home directory `/home/admin' ...
		  Copying files from `/etc/skel' ...
		  New password:
		  Retype new password:
		  passwd: password updated successfully
		  Changing the user information for admin
		  Enter the new value, or press ENTER for the default
		  		Full Name []: Admin
		  		Room Number []:
		  		Work Phone []:
		  		Home Phone []:
		  		Other []:
		  Is the information correct? [Y/n] y


> **IMPORTANT:** You should create a **strong password** for your admin account. Password we are using here is just for practice.

> **NOTE:** When you create user **admin** with **adduser** command, this will also create a group **admin** and a home directory **/home/admin** 

- Go inside /home and list the contents

      cd /home
      ls -l

  - we will get a message:

        ls: cannot open directory '.': Permission denied


   - This is because we configured our system, in the previous section, to hide user directories. 
 
   - As a priviledged user we can still see users' directories by using sudo keyword

         sudo ls -l

  - You should see that a new directory **admin** has been created.

- Try to enter into admin user's directory /home/admin

      cd /home/admin

  - You will get a message

        -bash: cd: /home/admin: Permission denied

  - Our security configuration works well. No user can enter inside the directory of the other user.

- **Give elevated priviledges** to admin - assign admin user to the **sudo** group:

	  sudo usermod -aG sudo admin

  - The **-aG** flag stands for Append and Group. This command instructs **usermod** command to append the username to a group, which in this case is the sudo group.

  - Keyword **sudo** stands for: **"superuser do"**      
  - You now have an **admin** account with sudo priviledges. This means that admin account can execute commands which require elevated root priviledges by prefixing those commands with keyword **sudo**.


- Check what groups user **admin** is in

	  groups admin

  - You should get

        admin : admin sudo

- Reboot computer

> **NOTE:** From this point forward we will only use **admin** account.



### Change Computer Name

> **NOTE:** THROUGHOUT THIS BOOK WE WILL REFER TO YOUR SERVER COMPUTER NAME AS "**my-home-server**". Wherever you see "my-home-server", replace it with the actual name of your home server machine.

- Log in as **admin**

- Change name in **hostname file** 

  - Open file "hostname", located in the "/etc" directory, using nano editor

	    sudo nano /etc/hostname
  
  - Change the line of text that says "**raspberrypi**" to your desired computer name (e.g. "my-home-server"). 
	
  - Save and close the file: <vxm-kbd>CTRL + S</vxm-kbd> <vxm-kbd>CTRL + X</vxm-kbd>

<definition />

>**nano** - is a popular simple text editor used in Linux to edit text in files.
</definition>


- Change name in **hosts file**

  - Open file "hosts", located in the "/etc" directory, with nano

		sudo nano /etc/hosts
	
  - Change the line of text that has your old computer name. It should be the last line that starts with 127.0.0.1 

        127.0.0.1       localhost
        ::1             localhost ip6-localhost ip6-loopback
        ff02::1         ip6-allnodes
        ff02::2         ip6-allrouters
        
        127.0.1.1       old-computer-name

      - Change **old-computer-name** to your new computer name (e.g. **my-home-server**)
	

  - Save and close the file: <vxm-kbd>CTRL + S</vxm-kbd> , <vxm-kbd>CTRL + X</vxm-kbd>

- Reboot computer

	  sudo reboot

- Log in with **admin**

- Your command line prompt should now show

      admin@my-home-server:~ $

- Confirm hostname has been changed

	  hostname

	- it should now say **my-home-server**

## Set up Network

### Check Local Network Connectivity

- **Ping the machine** - Check that your computer is on the network using ping command

	  ping my-home-server.local

  - Hit <vxm-kbd>CTRL + C</vxm-kbd> to exit PING (Packet Internet Groper)

  - You should see lines of something like this repeating:

		64 bytes from 192-168-1-105.tpgi.com.au (192.168.1.105): icmp_seq=1 ttl=64 time=0.102 ms
	
  - **NOTE**: 192.168.1.105 in this example is Server machine's IP address on your local network

- **Print internal IP address** of this machine

	  hostname -I

  - You should see address like : 192.168.1.105

- **Print interface configuration**

	  ifconfig

	- You should see your Ethernet, loopback and wireless network interfaces: eth0, lo, wlan0

### Open Router Admin

  - On your [Client computer](#definitions) open any browser e.g. Chrome, and navigate to your local network **router admin** web-page by typing in the address bar the following url:

        http://192.168.1.1

  - If that does not work, then try:

        http://192.168.0.1
   
  - If that does not work, search on Google "what is router admin url for {{NAME_OF_YOUR_ROUTER_BRAND}}", for example "what is router admin url for tp-link"

### Get Internal IP

- In your Router Admin - **Verify that the your server machine is visible on router admin website** 

  - You should see a link that says something like "**Wireless Clients**". It may say something different depending on your router brand.

  - On the list of connected wireless clients you should see a name of your [Server computer](#definitions) ( e.g. **my-home-server** ) and its **Internal IP Address** next to it (e.g. 192.168.1.105)

- Note down the Internal IP address of your Server machine. You will be using it in the next section to configure Port Forwarding.


### Set up Static Internal IP

Internal IP addresses are not static. They get changed by the router's DHCP server.

<definition>

> **DHCP (Dynamic Host Configuration Protocol)** - is a protocol that provides quick, automatic, and central management for the distribution of IP addresses to devices on the network within a network. A DHCP server dynamically issues unique IP addresses to devices on the network and automatically configures other network information. In most homes and small businesses, the router acts as the DHCP server.
</definition>

We need to make sure that our home server machine always has the same IP address on the local network (static IP) so that we can reliably connect to it.

- In your router admin look for **LAN Settings**.
  For example on tp-link modem go to : **Advanced > Network > Lan Settings**

  - You should see sections: **DHCP Server, Client List, Address Reservation**

- In **Client List** find your server computer and note it's **MAC Address** and **Assigned IP** for example 192.168.1.105
- In **Address Reservation** section, click **Add** button which will prompt you to enter MAC and IP address.
  - Type in manually MAC and IP or click on button SCAN if available and select your device from list.
  - Click OK
  
- Your server computer now has static internal IP and it will not be changed by DHCP server.


### Get External IP

- [Open Router Admin](#open-router-admin)

- You should see the **External IP** address of your router on the main page of the router admin website. It should look something like this:

      IP Address: 113.216.184.151

### Configure Port Forwarding

**SKIP this section if you do not want to open your server to Internet**, but rather just want to access it from your local network. In this case go to section [Set up Firewall](#set-up-firewall).

**NOTE:** You can only do this if you have a **Static External IP** that your ISP has provided for you.

In order to access our Server from anywhere via internet, we need to first set up **port forwarding** on our router.


<definition>

> **Port Forwarding** - allows incoming connections from broader Internet (in technical terms known as WAN (Wide Area Network)) to reach a specific device/port on a **LAN** (Local Area Network). Port forwarding is achieved by using **NAT** (Network Address Translation) table that maps **External Port** to the **Internal IP** address.
</definition>

- On your [Client computer](#definitions), open the internet browser window and go to **router admin** webpage which should be located at url **http://192.168.1.1/** or **http://192.168.0.1/**

- Go to the page for port forwarding. It is different for different routers.

  - To find where the port forwarding settings are on your router => Search on the internet for keywords: "{{NAME_OF_YOUR_ROUTER_BRAND}} port forwarding", for example "tp-link router port forwarding"

  - Here is an example of port forwarding location for tplink router Archer VR1600v:

    - On the main menu go to : **Advanced > NAT Forwarding > Virtual Servers**
    - You should see :
      - Buttons **Add** and **Delete**
      - List of Virtual Servers with IPs and Ports 

At this point we want to add a new virtual server that points to our SSH port accessing SSH program. 

**SSH (Secure Shell)** is used to remotely execute commands on our home server in a secure way. 

We will install SSH later.

 - Add new Virtual Server by clicking button **Add**
	
	- Set Service Type to "SSH"	
	- Set External Port to number from 1024 to 49151
	- Set Internal IP to your Home Server's IP address on the network e.g. 192.168.1.105
	- Set Internal Port to default SSH port 22
	- Set Protocol to "TCP"

  - Settings should look something like this:

  |                |               |
  | -------------- | ------------- |
  | Service Type:  | SSH           |
  | External Port: | 1234          |
  | Internal IP:   | 192.168.1.105 |
  | Internal Port: | 22            |
  | Protocol:      | TCP           |
  
	
Now you have forwarded **External Port** 1234 to machine on your network with **Internal IP** 192.168.1.105 and SSH program that is running on that machine's **Internal Port** 22.

This means that you can now access SSH on that machine from the remote computer via your home network's **Static External IP** e.g. 113.216.184.151 and  **External Port** 1234.

We will explain later how to connect to your server remotely using SSH in [SSH Setup Section](#set-up-ssh).
	
  
<definition>

> **Network Port** - is a logical number that identifies to which local **process** or **service**, the specific incoming internet traffic should be directed towards.
> 
> Ports are classified into 3 main categories:
>
> 1. **Well Known Ports** - ( 0 - 1023 ) - are used by system or processes run by root or with specific privileges.
>
> 2. **Registered Ports** - ( 1024 - 49151 ) - are used by programs run by users in the system.
>
> 3. **Dynamic Ports** - ( 49152 - 65535 ) - are not assigned for any specific purpose.
</definition>



<br>

### Check Outside Connectivity

There are 2 ways to check network ports' outside connectivity. You should know both ways.
- IsMyPortOpen.com
- Netcat

#### Use IsMyPortOpen.com

Check if your server's ip and port is reachable from outside by using utility website [ismyportopen.com](http://ismyportopen.com)

- Go to http://ismyportopen.com/
- Type in your IP and port and hit button Check
- You should get a green check and a message : **Port is open**

#### Use Netcat

- **On Mac OS Install Homebrew first** - If you are on Mac OS make sure to install **homebrew** first. Go to: https://brew.sh/ for more info.
  - Open Terminal
  - Run command

        /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

  - Add homebrew to your PATH. **NOTE:** replace **admin** with your username:

        (echo; echo 'eval "$(/opt/homebrew/bin/brew shellenv)"') >> /Users/admin/.bash_profile

        eval "$(/opt/homebrew/bin/brew shellenv)"

- Install Netcat on your client computer

  |         |                                          |
  | ------- | ---------------------------------------- |
  | Windows | Go to: https://nmap.org/download.html    |
  | Mac OS  | Run command: **brew install netcat**     |
  | Linux   | Run command: **sudo apt install netcat** |
  |         |                                          |

- Check on your client computer if you can connect from outside to your server computer ip and port e.g. 113.216.184.151:8333

  - On **Linux** and **Mac OS** run

        nc -zv 113.216.184.151 1234

  - On **Windows** open command prompt and run

        ncat -zv 113.216.184.151 1234



## Set up Firewall

<definition>

> Firewall - is a network security system that monitors and controls incoming and outgoing network traffic based on predetermined security rules. It  restricts internet traffic in to, out of, or within a private network. Firewall functions by selectively blocking or allowing data packets. Its purpose is to prevent anyone inside or outside of a private network from engaging in unauthorized web activities.
</definition>

<definition>

> **UFW** - **Uncomplicated Firewall** is a standard CLI program for working with the firewall on Linux.
</definition>

### Install and Enable UFW

**NOTE:** Make sure you are logged in physically on the server machine and not remotely(e.g. via SSH). This because if you enable firewall remotely, you will be disconnected from the server and will not be able to connect again until you disable firewall or add a rule to allow the port on which remote connection program connects to the server (e.g. port 22 for SSH).

- Install ufw 

      sudo apt install ufw

- Check status: 
      
      sudo ufw status

  - You may get 
  
        ERROR: Couldn't determine iptables version
    OR
        
        Status: inactive

- Reboot: 
      
      sudo reboot

- Check status again: `sudo ufw status`
  - This time you should get: `Status: inactive`

- Enable firewall: 

      sudo ufw enable

  - You should see

        Firewall is active and enabled on system startup

- Check existing firewall rules (list of allowed ports)

      sudo ufw status

  - You should see: `Status: active`
  - This means that firewall is active and running but has no rules yet. You have to add rules to allow ports.
  
  We will first add a rule for SSH port because in the next section we will enable SSH on the server.


### Add Firewall Rule

- Add port 22 - the standard port for SSH (Secure Shell) connections, which is how we will connect to our server remotely.

      sudo ufw allow 22

- Add another port 1234 

      sudo ufw allow 1234

- Show firewall status

      sudo ufw status

  - You should see

        Status: active
        
        To                         Action      From
        --                         ------      ----
        22                         ALLOW       Anywhere
        1234                       ALLOW       Anywhere
        22 (v6)                    ALLOW       Anywhere (v6)
        1234 (v6)                  ALLOW       Anywhere (v6)

- Reboot

      sudo reboot

### Remove Firewall Rule

- Display a numbered list of rules

      sudo ufw status numbered

  - You should see something like
       
             To                         Action      From
             --                         ------      ----
        [ 1] 22/tcp                     ALLOW IN    Anywhere
        [ 2] 1234                       ALLOW IN    Anywhere
        [ 3] 22/tcp (v6)                ALLOW IN    Anywhere (v6)
        [ 4] 1234 (v6)                  ALLOW IN    Anywhere (v6)


- Remove port 1234 by rule number

      sudo ufw delete 2

  - NOTE: When you remove the rule, the number position of all rules after it , decreases by one

- Display numbered list again

      sudo ufw status numbered

  - You should now see

        
             To                         Action      From
             --                         ------      ----
        [ 1] 22                         ALLOW IN    Anywhere
        [ 2] 22 (v6)                    ALLOW IN    Anywhere (v6)
        [ 3] 1234 (v6)                  ALLOW IN    Anywhere (v6)


- Remove port **"1234 (v6)"** by rule number

      sudo ufw delete 3
	


## SSH - Remote Connect

Ref: https://www.raspberrypi.org/documentation/remote-access/ssh/

<definition>

> **SSH (Secure Shell)** - is a cryptographic network protocol for operating network services securely over an unsecured network. Typical applications include remote command-line, login, and remote command execution, but any network service can be secured with SSH.
</definition>

In order to connect to our Server remotely (from the Client computer), we need to enable SSH on the Server.



### Step 1 - Enable SSH

- Log into the [Server](#definitions) with **admin** user.

- Enable SSH with Linux commands

	  sudo systemctl enable ssh
	  sudo systemctl start ssh

- Confirm that SSH is active

      sudo systemctl status ssh

  - You should see a green dot and a text like

        ssh.service - OpenBSD Secure Shell server
            Loaded: loaded (/lib/systemd/system/ssh.service; enabled; vendor preset: enabled)
            Active: active (running) ...
            ...


### Step 2 - Remote Connect with SSH
 
We can connect remotely from another machine on our Local Network (LAN) or from the Internet (WAN).

#### SSH over LAN

To connect over **LAN** we need to get Server's **Internal IP** and Internal Port

- [Open Router Admin](#open-router-admin)
- [Get Internal IP](#get-internal-ip) of your server e.g. my-home-server



#### SSH over Internet

To connect over **Internet** we need to get Server's **External IP** and External Port

- Log into the [Client computer](#definitions) (your personal computer)

- **Open Router Admin** - in the browser go to url **http://192.168.1.1/** or **http://192.168.0.1/**

- **Get the IP Address** - You should see your network's IP Address on the first page of your router admin. This is the static internet address assigned by your Internet Service Provider.
- **Get the port of the Server machine** - The port should be the same as the one you configured in the section [Configure port forwarding](#configure-port-forwarding) e.g. 1234

- Suppose that we are using the following details

    |          |                 |
    | -------- | --------------- |
    | Port     | 1234            |
    | Username | admin           |
    | IP       | 113.216.184.151 |

- Open **Git Bash** or **Terminal**

- **Connect to the Server** - connect remotely to the Server using ssh command

      ssh -p {{EXTERNAL_PORT}} {{USERNAME}}@{{IP_ADDRESS}}


    **For example:**

	  ssh -p 1234 admin@113.216.184.151

  - You will get this message

        The authenticity of host '[113.216.184.151]:1234 ([113.216.184.151]:1234)' can't be established.
        ED25519 key fingerprint is SHA256:1f8b2ad4be0e47c9b52ce4af6c652663.
        This key is not known by any other names
        Are you sure you want to continue connecting (yes/no/[fingerprint])?

  - Enter **yes** to continue. You will get something like this

        Warning: Permanently added '[113.216.184.151]:1234' (ED25519) to the list of known hosts.

  - You may (or may not) get disconnected with this message

        Connection closed by 113.216.184.151 port 1234

    - In this case, just enter ssh connect command again

          ssh -p 1234 admin@113.216.184.151   

- You should see a password prompt

      admin@113.216.184.151's password:

- After you type in the correct password, you will be remotely logged into the Server

- You can do everything on SSH remote connection as if you were logged in on the physical server computer. You should see a [command line prompt](#command-line-prompt) like this:

      admin@my-home-server:~ $

- **Disconnect from the Server** : Hit <vxm-kbd>CTRL + D</vxm-kbd>

- From this point onwards you can use your Client PC to access the Server and run all the commands on it.
	- Therefore you can disconnect monitor and keyboard from the Server computer if you wish so.

>**NOTE:** On your Client computer, ssh has created a [dot directory](#dot-directories) called **.ssh** inside the [user home directory](#user-home-directory)
- To see the hidden .ssh directory, open terminal and type in this command

      ls -la

- Go inside the .ssh folder and view contents of it

      cd .ssh
      ls -l

  - You should see the file called **known_hosts**

- Open **known_hosts**

      nano known_hosts

- You should see something like:

      [113.216.184.151]:1234 ssh-ed25519 jfY8xUO1cfDAlbbB ...
      [113.216.184.151]:1234 ecdsa-sha2-nistp256 FUbUx1a3sM= ...

  - These are identifying keys related to your Server. If you connect to any other servers, similar keys will be added to this file to mark those servers/hosts as known hosts.


### Step 3 - Passwordless Connect with RSA


<definition>

> **RSA (Rivest-Shamir-Adleman) encryption** - is a public-key encryption used for securing data transmission of e-mail and other digital transactions over the Internet. "RSA" is the acronym for the surnames of Ron Rivest, Adi Shamir and Leonard Adleman, who publicly described the algorithm in 1977.
</definition>

SSH protocol uses RSA encryption to enable Client computer to connect remotely to Server computer by use of **public and private key**, instead of having to type in the password.

SSH files are stored inside **".ssh"** folder located in the [User Home directory](#user-home-directory).


- Let's suppose that we have the following client-server set-up:

  - Client computer:

    |                     |                 |
    | ------------------- | --------------- |
    | Client Machine Name | my-pc           |
    | Operating System    | Windows         |
    | Username            | Admin           |
    | User directory      | C:\Users\Admin\ |

    - Replace Admin above with your username


  - Server computer:

    |                                |                 |
    | ------------------------------ | --------------- |
    | Server Machine Name (hostname) | my-home-server  |
    | Operating System               | Raspberry Pi OS |
    | Username                       | admin           |
    | User directory                 | /home/admin/    |

    - Replace **my-home-server** above with your server name
  
- On the [Client](#definitions) open **Git Bash** or **Terminal**

- **Create RSA encryption keys on Client** using **ssh-keygen** command:
	
	  ssh-keygen 

  - You should see a prompt:

	    Generating public/private rsa key pair.
        Enter file in which to save the key (/c/Users/Admin/.ssh/id_rsa):

    The prompt gives a suggestion to use a default file called **id_rsa** inside **.ssh/** folder to store the private key.
    
    However this is not good enough for us. We want to structure our files with the view that we will connect to many other servers.
    
  - **Copy the suggested file path** (/c/Users/Admin/.ssh/id_rsa) and **append the Server name at the end** like:

        /c/Users/Admin/.ssh/id_rsa_my-home-server
	
  - Hit <vxm-kbd>Enter</vxm-kbd>
  - Next you should see passphrase prompts. Just hit <vxm-kbd>Enter</vxm-kbd> without typing in any text. 

	  Enter passphrase (empty for no passphrase):
	  Enter same passphrase again:

  - The keys will be generated and you will see the following output (where **"my-pc"** is the name of your Client computer, **"Admin"** is your username on your Client computer,**"my-home-server"** is your Server computer name):

        Your identification has been saved in /c/Users/Admin/.ssh/id_rsa_my-home-server
        Your public key has been saved in /c/Users/Admin/.ssh/id_rsa_my-home-server.pub
        The key fingerprint is:
        SHA256:h7FUEfiH8PtvNANPjc7uZM1VH6pGG/5LmI/PNp83M6E Admin@my-pc
        The key's randomart image is:
        +---[RSA 3072]----+
        |         .+o     |
        |        o.       |
        |        o+ .   +.|
        |       . ++ o + =|
        |        S .= B  o|
        |         .+ * B+.|
        |           O ++o+|
        |          . BEo+o|
        |           .oX*o=|
        +----[SHA256]-----+


- **Copy the public key to the [Server](#definitions)** using **ssh-copy-id** command. Type in the following command. (Replace the tokens - words starting with **{{** and ending with **}}** - with your own data)

	  ssh-copy-id -i ~/.ssh/id_rsa_{{SERVER_NAME}}.pub -p {{SERVER_PORT}} {{SERVER_USERNAME}}@{{SERVER_IP_ADDRESS}}

	**For example:**

	  ssh-copy-id -i ~/.ssh/id_rsa_my-home-server.pub -p 1234 admin@113.216.184.151

    - **NOTE:** Path **"~/"** is a short notation for your [user home directory path](#user-home-directory) e.g. "/c/Users/Admin/" 

	- You will be prompted for password:
    
          /usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/c/Users/Admin/.ssh/id_rsa_my-home-server.pub"
          /usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
          /usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
          admin@113.216.184.151's password:

    - After you type in the password you will see output:

          Number of key(s) added: 1

          Now try logging into the machine, with:   "ssh -p '1234' 'admin@113.216.184.151'"
          and check to make sure that only the key(s) you wanted were added.

	- **NOTE:** logging into the Server machine by using command **"ssh -p 1234 admin@113.216.184.151"** will still ask us for password. This is because we have not created our RSA keys in the default file "id_rsa" but instead we have created it in the custom file named "id_rsa_my-home-server". Therefore we need to specify the location of the key file when using ssh to connect, for example: **"ssh -i ~/.ssh/id_rsa_my-home-server -p 1234 admin@113.216.184.151"**

- **Connect to the Server without a password**

	  ssh -i ~/.ssh/id_rsa_my-home-server -p 1234 admin@113.216.184.151

  - **NOTE:** We are using the path to file "id_rsa_my-home-server" and NOT "id_rsa_my-home-server.pub" (private key, not public key).

  - This time, you should be logged into Server without a password prompt

- View public key on Server (for the purpose of understanding the private-public key handshake)
  - Navigate to .ssh folder on the server

        cd ~/.ssh
  - View contents of the folder

        ls -l

    - You should see one file **"authorized_keys"**

  - Open **authorized_keys** file

        nano authorized_keys

  - You will see inside the file **authorized_keys**, the copied public key from the Client that looks like **"ssh-rsa VMXfGsZk9MmnTFvkwSZ11O ..."**

  - This is how Server authorizes Client connection - by making sure that the public RSA key matches the one stored in **authorized_keys** file.

  - Exit nano editor  : <vxm-kbd>CTRL + X</vxm-kbd>

As you can see, eventhough we now don't need to provide the password, it is still cumbersome to type in all the connection data every time we want to connect to the Server.

In the next section we will learn how to connect to the Server by just providing the Server name in ssh command like: **"ssh my-home-server"**.

- Disconnect from the Server : <vxm-kbd>CTRL + D</vxm-kbd>
- Clean the terminal window : <vxm-kbd>CTRL + L</vxm-kbd>



### Step 4 - One Word Connect with SSH Config


- On the Client, go into .ssh folder:
  
      cd ~/.ssh

- Create file **"config"** without any file extension

      touch config

- Open file config

      nano config

- Paste into it Host connection configuration like:		

	  Host {{SERVER_NAME}}
		HostName {{SERVER_IP_ADDRESS}}
		Port {{SERVER_PORT}}
		User {{SERVER_USERNAME}}

	**For example:**

	  Host my-home-server
		HostName 113.216.184.151
		Port 1234
		User admin
        IdentityFile  ~/.ssh/id_rsa_my-home-server


- Save and exit nano editor : <vxm-kbd>CTRL + S</vxm-kbd> <vxm-kbd>CTRL + X</vxm-kbd>
  
- Connect to Server

	  ssh my-home-server

  - You should be automatically logged into Server without password prompt



## RDP - Remote Connect - Windows

**NOTE:** This section is relevant only to Windows client computer.

<definition>

> RDP (Remote Desktop Protocol) is a proprietary protocol developed by Microsoft which provides a user with a graphical interface to connect to another computer over a network connection.
</definition>

If we want to access remotely graphical user interface (GUI) of our server, from Windows personal computer, we can use **Remote Desktop Connection** program that comes pre-installed on Windows.

But first we need to install program called **xrdp** on the Linux home server and then we can connect to it from our Windows computer.
Ref: http://www.xrdp.org/

### Configure XRDP on Server

- Open terminal on your Client and SSH into your server machine

      ssh my-home-server

- Update and upgrade packages

      sudo apt update
      sudo apt upgrade

- Install **xrdp**

      sudo apt install xrdp

- Check status of xrdp service

      sudo service xrdp status

  - You should see something like

        ● xrdp.service - xrdp daemon
           Loaded: loaded (/lib/systemd/system/xrdp.service; enabled; vendor preset: enabled)
           Active: active (running) since Mon 2023-09-04 22:55:13 BST; 4min 18s ago
             Docs: man:xrdp(8)
                   man:xrdp.ini(5)

**XRDP** listens on port **3389**, so you will need to open that port on your firewall.

- Allow port 3389 in your firewall

      sudo ufw allow 3389

- Get your server's IP address and note it down

      hostname -I

  - NOTE: this will give you internal IP address of your server on your local network. If you want to connect to your server from outside your local network, you will need to get your external IP address. See section [Get External IP](#get-external-ip) for more info.

### Connect from Client

- On your Windows computer, open **Remote Desktop Connection** program
  - You can find it by typing **"Remote Desktop Connection"** in the search bar at the bottom left of your screen
  - Or you can find it in the Start Menu under **Windows Accessories** folder

- In the **Remote Desktop Connection** window, type the IP address of your server and click **Connect**

  - You will get a warning message about certificate. Click **Yes** to continue
  - You will get a login screen. Enter your username and password and click **OK**
  - You should be logged in and see the desktop of your Linux server on the screen of your Windows computer

That's it. Now you can use your Linux server's GUI from your Windows computer, from anywhere in the world.



## Upload / Download

### Upload 

- Copy file from local windows computer to Raspbery Pi server

		scp -P {{SERVER_SSH_PORT}} {{CLIENT_FILE_PATH}} {{USERNAME}}@{{SERVER_IP_ADDRESS}}:/{{SERVER_FILE_OR_FOLDER_PATH}}

	- Example: from directory of the source files
  
			scp -P 1234 file1.json admin@220.237.80.202:/home/my-files/
			scp -P 1234 file2.json file3.txt admin@220.237.80.202:/home/my-files/

	- Example: from any directory using full path

			scp -P 1234 /C/my-files/file1.json admin@220.237.80.202:/home/my-files/
			scp -P 1234 /C/my-files/file2.json /C/my-files/file3.txt admin@220.237.80.202:/home/my-files/

### Download

- Copy file from Raspbery Pi server to local windows computer

	

## Manage External Storage Device


Refs: 	
- [Raspberry Pi - External Storage Config](https://www.raspberrypi.com/documentation/computers/configuration.html#external-storage-configuration)

- [Ubuntu.com - Fstab](https://help.ubuntu.com/community/Fstab)


### Temporary Storage

- Turn on your server machine in Desktop mode (not headless mode) - it should already be set up in Desktop mode

- Insert external storage device into your server computer
  - On Raspberry Pi you will get popup that says **"Removable medium is inserted."** Click OK

- Open file explorer
  - You should see your external drive mounted on the left panel inside directory **"/media/{{USERNAME}}"**. 

    For example: **"/media/admin/2.1 TB Volume"**


By default, Linux mounts external drives onto **/media/{{USERNAME}}** folder. 

In **file explorer** there is a **quick access link** to your external drive on the left panel.
- On Linux Mint it is under the **"Devices"** section
- On Raspberry Pi it is at the top of the left panel under the **Home Folder** and **Filesystem Root**

You should see a name of your external drive for example **"123 GB Volume"**.

Now you can use your external drive as a temporary storage for your files.


### Permanent Storage
 
In this section we are going to learn how to mount an external drive on the selected directory of our Server machine so that we can use it as a permanent storage for our data. 

We will reformat the drive to use ext4 file format which is Linux's file format that allows us to set permissions on files and folders for multiple users - same as for the rest of the files on our Server machine. 

We will configure the server to automatically mounts our storage when machine is rebooted.


- Prepare External Storage device.  It can be one of these 3:

|     |                         |                                                                                      |
| --- | ----------------------- | ------------------------------------------------------------------------------------ |
| 1.  | HDD (Hard Disk Drive)   | Cheap, spacious and slow. Old spinning disc technology                               |
| 2.  | USB (Flash Drive)       | Small, portable and quick (prefered is USB 3 which is quicker and larger than USB 2) |
| 3.  | SSD (Solid State Drive) | Best performance, large storage, most expensive. Best option.                        |

- Plug your external storage device into your Server

- **NOTE:** if using USB 3, you should plug it in the port that has a **BLUE** colored line




In order to properly administer user permissions on the external drive, we need to reformat it into EXT4 format.

> **ext4** (fourth extended filesystem) - is the default file system for Linux.
 

#### Step 1. Create Partition

- Get external drive partition: `sudo blkid`

	- Your external drive details will look something like:
  
		  /dev/sda1: UUID="5C24-1453" BLOCK_SIZE="512" TYPE="exfat" PARTUUID="00000000-01"

    - If you are not sure which one on the list is your drive's block device info , then unplug the external drive and run the `blkid` command again. The one that is missing from the list is your external drive.

    - Note down these details:

        |                 |                  |
        | --------------- | ---------------- |
        | **device name** | /dev/sda1        |
        | **device id**   | UUID="5C24-1453" |
        | **device type** | TYPE="exfat"     |


- Start disk format:

      sudo fdisk /dev/sda1 --wipe auto

  - You will get warning in red like this: 
	
        The device contains 'exfat' signature and it will be removed by a write command. See fdisk(8) man page and --wipe option for more details.

    Just ignore it

- You will see a prompt: **Command (m for help):**

- Type `g` and hit **Enter** - to create a new empty GPT partition table
  - You will see message like `Created a new GPT disklabel (GUID: aa60d5a8-6a7f-43f6-8c41-8958ae018526).`
  - You may see a warning in red - just ignore it
  - You will get a prompt: **Command (m for help):**

- Type `n` and hit **Enter** - to create a new partition on the disk
  - You will get a prompt: `Partition number (1-128, default 1):`
- Hit **Enter** to accept default value
  - You will get a prompt: `First sector (2048-1953525167, default 2048):`
- Hit **Enter** to accept default value
  - You will get a prompt: `Last sector, +/-sectors or +/-size{K,M,G,T,P} (2048-4095997918, default 4095995903):`
- Hit **Enter** to accept default value
  - You will get message like: `Created a new partition 1 of type 'Linux filesystem' and of size 1.9 TiB.`
  - You will get a prompt: **Command (m for help):**

- Type `w` and hit **Enter** - to write table to disk and exit
  - You will get message like: `The partition table has been altered.`

  - NOTE: You may get this error message or messages:
		
		Failed to add partition 1 to system: Invalid argument

    OR
        
        Calling ioctl() to re-read partition table.
        Re-reading the partition table failed.: Invalid argument

    AND

		The kernel still uses the old table. The new table will be used at the next reboot.
        Syncing disks.

  - Just ignore above messages

- Reboot

      sudo reboot

- Run `sudo blkid` again

- Your external drive details should now look like this:

	  /dev/sda1: PTUUID="aa60d5a8-6a7f-43f6-8c41-8958ae018526" PTTYPE="gpt" PARTUUID="eb3cd722-01"

#### Step 2. Format Partition

- To format partition:

	  sudo mkfs.ext4 /dev/sda1

- You will get:  
		
	  Found a gpt partition table in /dev/sda1
	  Proceed anyway? (y,N)

- Type **y**, hit **Enter** and **WAIT** !!!
  - **NOTE:** Creating journal may take a long time especially if you have a large disk (terabytes in size). So **BE PATIENT AND DON'T TOUCH ANYTHING** until it's finished. Go and grab a coffee while you wait ;-)

- You will see output like:
		
		Creating filesystem with 15155195 4k blocks and 3792896 inodes
		Filesystem UUID: aa60d5a8-6a7f-43f6-8c41-8958ae018526
		Superblock backups stored on blocks:
				32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208,
				4096000, 7962624, 11239424

		Allocating group tables: done
		Writing inode tables: done
		Creating journal (65536 blocks): done
		Writing superblocks and filesystem accounting information: done

- You will know it's finished when you see the last line:

      Writing superblocks and filesystem accounting information: done

- Run command to check block ids again

	  sudo blkid

- It should now look like this:
		
	  /dev/sda1: UUID="aa60d5a8-6a7f-43f6-8c41-8958ae018526" BLOCK_SIZE="4096" TYPE="ext4" PARTUUID="eb3cd722-01"








#### Step 3. Mount External Drive

To start using our external drive, we first need to **mount it**.

><definition />
>
> Mounting - is the act of associating a storage device to a particular location in the directory tree. Unix systems have a **single directory tree**. All accessible storage must have an associated location in this single directory tree.

><definition />
>
> **"/dev/" directory** - contains files that represent devices that are attached to the local system. These are not regular files that a user can read and write to. These files are called devices files or special files.

><definition />
>
> **blkid command** - displays information about available block devices e.g. external hard drives or USBs. **blkid** without any argument will list all the available devices with their Universally Unique Identifier (UUID), the TYPE of the file system, and the LABEL if it's set.
>
><definition />

**NOTE:** We will refer to your external storage device as "**my-drive**". You can replace this with your own name.

We will have to create a directory onto which we will mount our external drive.

**NOTE:** Standard practice is to create mount directory inside **"/mnt/"** directory. However, you can create it anywhere you like and it will still work.


- Create mount directory

      sudo mkdir /mnt/my-drive

- Get device id 

      sudo blkid

- **Mount** external drive by pointing device id to mount directory

	  sudo mount /dev/sda1 /mnt/my-drive


- **Unmount** the external drive

      sudo umount /mnt/my-drive

Manually mounting external drive that we want to use as permanent storage is not practical. We want to set it up to automatically mount on the same folder every time Server machine starts up.

To automatically mount external drive we need to add entry to **/etc/fstab** file.

> **FSTAB** (File System Table) file - is a system configuration file that contains information about disk partitions and other devices that are mounted after the system boots.

To understand persistent mounting of file system in more details check out [Redhat - Persistently mounting file systems](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/managing_file_systems/assembly_persistently-mounting-file-systems_managing-file-systems)

For the full description of fstab file syntax, see [FSTAB File Syntax](#fstab-file-syntax) in the [Appendix](#appendix).

You can also check out [Ubuntu.com - Fstab](https://help.ubuntu.com/community/Fstab) for more detail info about fstab.

- Run command to check block ids again

	  sudo blkid

  - Find your external drive in the list (normally starts with **/dev/sd...**)
  
- **Write down 3 things**
  1. UUID
  2. TYPE 
  3. mount directory path

    **For example:**

    |                |               |
    | -------------- | ------------- |
    | UUID           | 5C24-1453     |
    | TYPE           | exfat         |
    | MOUNT_DIR_PATH | /mnt/my-drive |
    

- **Open the fstab file for edit**

	  sudo nano /etc/fstab

  - Add the following line at the bottom of fstab file 

        UUID={{UUID}} {{MOUNT_DIR_PATH}} {{TYPE}} defaults,auto,users,rw,nofail 0 0
	
    - Replace tokens {{UUID}}, {{MOUNT_DIR_PATH}} and {{TYPE}} with the data you noted above in the previous step.

    For example:

        UUID=aa60d5a8-6a7f-43f6-8c41-8958ae018526 /mnt/my-drive ext4 defaults,auto,users,rw,nofail 0 0

	- Save and exit nano editor : <vxm-kbd>CTRL + S</vxm-kbd> <vxm-kbd>CTRL + X</vxm-kbd>

- Restart the Server machine

	  sudo reboot

Now your Server should automatically mount the external drive when you start the Server machine.

You should not unplug this drive. This is our main storage device.

If you want to unplug the external drive that has been mounted, you should **unmount** it first. 



 #### Step 4. Test Auto Mounting
 
If after reboot you get a black screen with a long message that contains "**Root account locked**" got to section [Troubleshoot Mounting](#troubleshoot-mounting) to fix it. The solution is in [Fixing Error - Root account locked](#fixing-error-root-account-locked) section.

Otherwise continue with steps bellow

- **Check your mount directory** to see if the external drive has been mounted

      ls -l /mnt/my-drive

  - You should see **"lost+found"** directory inside your mount directory like:

        total 0
        drwxr-xr-x 2 root root 0 Sep  5 00:00 lost+found

  - If you cannot see the folder **"lost+found"** inside your mount directory, then your external drive has not been mounted successfully. Proceed to next checks.

- **Check fstab file** to see if the UUID, mount directory path and type are correct. First runn blkid to get your drive details.

      sudo blkid
      sudo nano /etc/fstab

  - **UUID** must match the UUID of your external drive e.g. UUID=aa60d5a8-6a7f-43f6-8c41-8958ae018526 
  - **Mount directory path** must match the mount directory path you created e.g. /mnt/my-drive
  - **File System Type** must match the file system type of your external drive e.g. ext4

- **Check journal logs** for mount directory /mnt/my-drive

      journalctl -u mnt-my-drive.mount -b
      
  - Slashes in mount directory path are escaped with dashes. So if your mount directory path is **"/mnt/my-drive"** then you need to type **"mnt-my-drive.mount"** in the command above.
	
  - **-b** - filters out entries for last boot. If you dont't use -b , you will see a journal log for the whole journal of mounting onto directory /mnt/my-drive

  - If mount was succesfull journal log should show you something like this

        Dec 08 12:44:33 my-home-server systemd[1]: Mounting mnt-my-drive.mount - /mnt/my-drive...
        Dec 08 12:44:34 my-home-server systemd[1]: Mounted mnt-my-drive.mount - /mnt/my-drive.

  - If mount failed, you may see this error in journal logs

        mount: /mnt/my-drive: wrong fs type, bad option, bad superblock on /dev/sda1, missing codepage or helper program, or other error.

If journal logs show that the boot failed and you can see **"bad superblock error"**:

    mount: /mnt/my-drive: wrong fs type, bad option, bad superblock on /dev/sda1, missing codepage or helper program, or other error.

Then you need to run **file system check** for your external drive.

- **Run fsck (file system check)** command on the external drive

      sudo fsck /dev/sda1

  - Say **yes** to all prompts

- Reboot the Server machine

      sudo reboot

- Check journal logs again

If your external drive has not been mounted again and you got the bad superblock error again, chances are that your external drive is corrupte and not fixable. In this case you just have to get a new external drive.

- **Check that files and directories persist**
  - Create files and directories inside your mount directory

        sudo touch /mnt/my-drive/test.txt  \
        && sudo mkdir /mnt/my-drive/test  \
        && sudo touch /mnt/my-drive/test/test2.txt

  - Reboot the Server machine

        sudo reboot

  - List the files in your mount directory

        ls -l /mnt/my-drive \
        && ls -l /mnt/my-drive/test 

    - You should see the files and directories you created before reboot

            total 20
            drwx------ 2 root root 16384 Dec  8 11:10 lost+found
            drwxr-xr-x 2 root root  4096 Dec  8 13:12 test
            -rw-r--r-- 1 root root     0 Dec  8 13:12 test.txt
            total 0
            -rw-r--r-- 1 root root 0 Dec  8 13:12 test2.txt


  - If you cannot see the files and directories you created before reboot, then your external drive has not been mounted successfully. Proceed to next checks.

      
 
- If you have problems with booting or auto mounting, go to section [Troubleshoot Mounting](#troubleshoot-mounting)

#### Step 5. Give Access


- Give all access to group admins (read, write, execute)

      setfacl -m ""g:admins:rwx" /mnt/my-drive



### Unmount

- Make sure you are not inside the directory that you are trying to unmount. So if you are inside the directory, you need to go out of it first:

      cd ~

For this exercise we will rename the directory **"my-drive"** to **"myDrive"** to demonstrate unmounting but we will rename it back to "my-drive" after that.

- **Try to rename mount directory** from my-drive to myDrive

      sudo mv /mnt/my-drive /home/myDrive

  - You will get this message

        mv: cannot move '/mnt/my-drive' to '/home/myDrive': Device or resource busy
    
  - This is because your external drive is mounted onto this directory

In this situation we need to unmount the external drive.

- **Unmount the external drive**

      sudo umount /mnt/my-drive

- Rename mount directory from my-drive to myDrive 

      sudo mv my-drive myDrive

- List home directories

      ls -l /home/

  - You will see that the directory my-drive has been changed to myDrive

  - If you wanted this directory to be mounted automatically, you would have to change the line of text inside the file **"/etc/fstab"** to use new directory name for example:

        UUID=5C24-1453 /home/myDrive exfat defaults,auto,users,rw,nofail 0 0

  - And then you would have to reboot the server computer
  - However we will not be changing the name of the drive. This was just for practice.

- Change back the name of the drive to my-drive

      sudo mv /home/myDrive /mnt/my-drive

- Make sure that file **"/etc/fstab"** contains the correct file path **"/mnt/my-drive"**, for example:
    
      UUID=5C24-1453 /mnt/my-drive exfat defaults,auto,users,rw,nofail 0 0


> NOTE: When you use **sudo mv** to rename the directory, the group and the owner of the directory will change to **root**. You need to change this back to your user e.g. admin

    sudo chown admin /mnt/my-drive 
    sudo chgrp admin /mnt/my-drive


#### Dealing with 'target is busy'

If you cannot unmount external drive because of the persisting '**target is busy**' message, then continue to read this section to fix it.

The 'target is busy' message means there are files on the storage device that are in use by a program. To close the files, use the following procedure.

- Close any program which has open files on the storage device.

- If you have a terminal open, make sure that you are not in the folder where the storage device is mounted, or in a sub-folder of it.

- If you are still unable to unmount the storage device, you can use the lsof tool to check which program has files open on the device. You need to first install lsof using apt:

	  sudo apt update
	  sudo apt install lsof

- To use lsof:

 	  lsof /mnt/my-drive


### Assign access only to our user


- We will temporarily allow access to external-drive only to our user:

	  sudo chown $(whoami):$(whoami) -R /mnt/my-drive

    - The $(whoami) variable will take the value of the user you are currently logged in as e.g. admin




### Troubleshoot Mounting


#### FIXING ERROR Root account locked 

You reboot Raspberry Pi after having changed /etc/fstab to auto-mount your external drive.

You get the black screen with a long mesage:

    You are in emergency mode. After logging in, type "journalctl -xb" to view system logs, "systemctl reboot" to reboot, "systemctl default" or "exit" to boot into default mode.

    Cannot open access to console, the root account is locked.
    See sulogin(8) man page for more details.

    Press Enter to continue.

- Turn off your RaspberryPi
- Take out the Micro SD that OS is installed on
- Put the SD into your personal computer (Windows, Mac etc.)
- Go inside the SD boot disk and find file **cmdline.txt** and open it
- Add the following to the end of the text on the same line, separating it with space

		init=/bin/sh

- Eject the SD card
- Put the SD card back into Raspberry Pi and turn the Pi on. 
  - This time the Pi will start in command line mode and take your commands input.

- Raspberry pi SD card has two main partitions, since we cannot read the partition table directly you must manually locate the device for your root and boot partitions and mount them.

- To do this 

	ls /dev

- You should see **mmcblk0p1** & **mmcblk0p2**. The second device mmcblk0p2 will be your root partition. You need to remount this with read write permissions.

		mount -o remount,rw /dev/mmcblk0p2 /

- Open fstab

		nano /etc/fstab

	- Remove the line for mounting your external drive. Use <vxm-kbd>CTRL + K</vxm-kbd> to delete the whole line.

- Turn off Raspberry Pi by turning off power or pulling out the power cable
- Put the SD card back into your PC and open **cmdline.txt** file again

- Lastly we need to revert **cmdline.txt** to original state (remove the temporary **init=/bin/sh** we added to it). 

- Now put SD card back into Pi and turn it on. The Pi will boot normally this time

<br>

#### FIXING ERROR Failed to mount 

If you rebooted your Pi and your USB disc is not mounted. 

- For example you see red line in boot progress text:

		[FAILED] Failed to mount /mnt/my-drive.
OR

- Or you log in and notice that /mnt/my-drive does not contain files
- Then you try to manually mount it and you receive error:

	mount : /mnt/my-drive : wrong fs type, bad option, bad superblock on /dev/sda1, missing codepage or helper program, or other error.

- You can check report of what happened by running

		journalctl -u mnt-my-drive.mount -b

    - Slashes in mount directory path are escaped with dashes. So if your mount directory path is **"/mnt/my-drive"** then you need to type **"mnt-my-drive.mount"** in the command above.
	- Option -b is just for last boot. Omit that if you want the whole journal for mounting your external drive
	- Hit **CTRL + C** to exit journal logs

##### FIX 1. - Change mount option for check fylesystem during boot

- Open fstab 

		sudo nano /etc/fstab

- Change mount mapping for your external drive to use flags "**1 1**" instead of "**0**" at the end. For example:

		UUID=aa60d5a8-6a7f-43f6-8c41-8958ae018526 /mnt/my-drive ext4 defaults 1 1

- Reboot

##### FIX 2. - Format and Partition USB disc in Desktop GUI with GParted

- Change to Desktop boot

		sudo raspi-config

	- System Options > Boot > Desktop

- Reboot

- Install GParted - tool for disc partitioning. Open Terminal at the top menu and type command

		sudo apt install gparted

- Open GParted: Start Menu > System Tools > GParted

- Select your device from dropdown list in top right. Should be something like: /dev/sda (1.91 TiB)
	- This will display your disk below
- Delete old partition: 
	- Right-click on partition > Delete
	- This will display : "Delete pending" at the bottom . Ignore that and continue
- Create new parition:
	- Right-click on partition > New 
	- "Create new Partition" window will pop-up
      - Leave all the defaults
	- Select File system: ext4
	- Click Add
	- popup will close and you will see green tick at the top

- Click on green tick at the top
	- You will get warning : "Are you sure you want to apply the pending operations?"
	- Click Apply
	- Wait for operations to complete

- Get your USB drive's UUID from the list of blocks

		sudo blkid

- Open fstab 

		sudo nano /etc/fstab

- Edit the line for your external drive mount or add it if it doesn't exist already:

		UUID=aa60d5a8-6a7f-43f6-8c41-8958ae018526 /mnt/my-drive ext4 defaults 1 1

    - NOTE: UUID should be changed to the one you noted previously, after running **blkid** command
	- NOTE: flags "**1 1**" at the end. That's very important. They mean that on startup should : 1 = Dump, 1 = Check filesystem for errors. 
	- With **1 1** flags set Raspberry Pi will take long time to start about 5 to 10 minutes because it will be doing a file system check. However this is necessary if you have a large USB 1 terabite or more.

- Save 

#### FIXING Long Boot - Checking in progress

After you have enabled auto mounting your external drive, your server machine may be taking very long time to boot, especially if your external drive is large e.g. terabytes in size.

- You may get this message on boot **Checking in progress on 1 disk (1.0% complete)**

- The Problem: 

	fsck is supposed to only run only when it needs to: either the filesystem wasn't cleanly unmounted or "it's been a while since it ran". The filesystem records the timestamp of when it was last unmounted for that second reason.

	The pi doesn't have a real time clock, though. This doesn't matter if you're connected to the internet because of NTP. Otherwise it will just default to 1/1/1970 12:00 AM UTC (or some hardcoded time in kernel) on every boot.

	So during boot (without ntp, the date is set back 1970), it looks at the current ridiculously current time, which is "before the last unmount date". Linux fscks just to be on the safe side in this insane situation. From its point of view, the FS was last unmounted "in the future".

	There's already a workaround this: a service called fake-hwclock which keeps the time going forward by auto saving and loading from a file. It's enabled by default. The problem with it is that the boot order is such that often the fsck part is run before the system time is restored by fake-hwclock.

- The Solution:

	Fix the boot order. Configure systemd so that fake-hwclock runs before the fsck.
 
- Create /etc/systemd/system/fake-hwclock.service.d/fsck.conf

		sudo mkdir /etc/systemd/system/fake-hwclock.service.d
		sudo touch /etc/systemd/system/fake-hwclock.service.d/fsck.conf
		sudo nano /etc/systemd/system/fake-hwclock.service.d/fsck.conf

- Edit fsck.conf - add following lines

		[Unit]
		Before=systemd-fsck-root.service

- Reboot


## Optional Installation

### Add Support for 32 bit programs on 64 bit OS

- When we try to run programs created for 32 bit system on our 64 bit Raspberry Pi OS, it will not work. 
  
- We recommend adding support for 32 bit programs as some software providers still haven't upgraded their products to support 64 bit Raspberry Pi OS 
  - For example, in the next optional section - [Install and Run VPN](#install-and-run-vpn) - we will be installing Express VPN but at the current time ExpressVPN only has installation for 32 bit Raspbery Pi OS.

- To provide support for 32 bit programs run the following commands one by one:

		sudo dpkg --add-architecture armhf

		sudo apt-get update

		sudo apt-get install libc6:armhf libstdc++6:armhf

		cd /lib
		sudo ln -s arm-linux-gnueabihf/ld-2.23.so ld-linux.so.3

- **NOTE**: There is a downside to this approach - after adding armhf you have to be careful when installing new packages that you get the 64 bit version if available and don't end up installing 32bit packages that you don't need.



### Express VPN

- Optionally you may install VPN (Virtual Private Network) to ensure your privacy on the internet.
- VPN effectively masks your IP address and geographical location.
- Open account with any VPN provider. We recommend [ExpressVPN](https://www.expressvpn.com/)
- You need to download and install VPN client on your Pi. We will describe here how to do it with ExpressVPN. But you can also follow steps provided by ExpressVPN at : https://www.expressvpn.com/support/vpn-setup/app-for-raspberry-pi/

#### 1. Download Express VPN

   - Open **Git Bash** terminal on your PC and connect to your Pi.
   - Create download directory where you will download expressvpn. This should be inside your mounted USB directory. Suppose your USB drive is mounted on /mnt/my-drive then run this command:
        
           sudo mkdir -p /mnt/my-drive/downloads

        - This will create downloads directory on your external drive folder if you don't already have it. You should download installation files into this folder.

   - On your PC open browser
   - Go to :
     - Linux distribution page https://www.expressvpn.com/setup#linux
     - Or latest downloads page : https://www.expressvpn.com/latest#linux
   - Select **Raspberry Pi OS** from dropdown list of distribution
   - Right-click on **Download** button > Copy link address
     - Should be something like: https://www.expressvpn.works/clients/linux/expressvpn_3.30.0.2-1_armhf.deb    
   - On your server cd into **/mnt/my-drive/downloads** and run download command
        
         sudo wget {{download_url}}

        - Replace {{download_url}} with the link you have copied

#### 2. Install Express VPN
  
        sudo dpkg -i {{installation_file_name}}

- Confirm expressvpn is installed

        expressvpn

   - This command should give you overview of expressvpn program

#### 3. Activate ExpressVPN.
   
        expressvpn activate

   - This will prompt you to enter your activation code
   - Go to https://www.expressvpn.com/setup#linux - linux setup page on Express VPN website, and you should see your code there. Copy and paste the code in your command line interface where you were prompted. 
   - **NOTE**: you will not see characters when you paste the code. Just hit Enter to proceed.
   - If you haven't selected to opt out of diagnostics, do that now. This will disable sending anonymous diagnostics to expressvpn
  
		 expressvpn preferences set send_diagnostics false

#### 4. Connect & Disconnect to VPN

- Ref: https://www.expressvpn.com/support/vpn-setup/app-for-raspberry-pi/#connect

- Connect

		expressvpn connect

	or connect to specific country

		expressvpn connect Colombia

- Check Status

		expressvpn status

- Disconnect

		expressvpn disconnect

- To see all the commands simply type

		expressvpn


#### 5. Automatically start on logn

Let's say if we wanted to connect expressvpn to Bahamas automatically when user logs into Raspbery Pi. Then do this:

- Open ~/.profile file
  - "~/" stands for /home/{my_username}. For example if user is admin then "~/" will be "/home/admin/"
- Paste this at the bottom of the **.profile** file

		#___________ Run commands on login___________

		# Start Express VPN on login
		expressvpn connect Bahamas

- Reboot

		sudo reboot

- Check status

		expressvpn status

  - It should say

		Connected to Bahamas


# Appendix

This section is used to explain concepts in more detail. It is not meant for reading from start to finish. It is meant to be used as a reference when you need to understand a particular concept in more detail. 

There will be reference links to Appendix sections from other parts of the book.


## Command Line Prompt

><definition />
>
> **Command Line Prompt** - is a small text inside the command line interface that is printed by the system before every command that you execute. This text informs user of his username, computer name, current folder location, and account type (normal user or root).

The format of the command line prompt is

    {{USERNAME}}@{{COMPUTER_NAME}}:{{CURRENT_DIRECTORY_LOCATION_PATH}} {{ACCOUNT_TYPE}}

**For example**:

    john@my-home-server:/home $

<def>
>
> **Account Type Symbol** - is the symbol printed at the end of the Command Line Prompt. It can be $ or #
> 
> **$** - stands for normal account
> 
> **\#** - stands for superuser / root account. # symbol will be visible instead of $ **only when you are logged in as a root account**
</def>


When you first open command line, you are automatically placed inside ~ (tilde) folder - which in fact is your [User Home folder](#user-home-directory).

**For example:** if we log into computer called **my-home-server** with a username **john**, we will see prompt:

    john@my-home-server:~ $

- In above example **"~"** is a short notation for **"/home/john/"**
 

 

## User Home Directory

<def>
>
> **User Home Directory** - is a directory created automatically by the operating system at the time of user account creation. This directory is intended for storing only user related documents and programs.
</def>


The following is the location of user's home directory on the main 3 operating systems:

| OS (Operating System) | User's Home location   |
| --------------------- | ---------------------- |
| Linux                 | /home/{{USERNAME}}     |
| macOS                 | /Users/{{USERNAME}}    |
| Windows               | C:\Users\\{{USERNAME}} |

>NOTE: In Windows operating system we use back slash symbol **"\\"** to separate folders in the file path. In most other operating systems we use forward slash **"/"**.

<def>

> **~ (tilde symbol)** - is a shorthand notation for User Home directory path. In Linux command line, User Home directory path can be replaced with **tilde symbol ~**
</def>

You can navigate into User Home directory by using short notation **"~"** instead of the whole User Home path

    cd ~

When inside User Home directory, the command line prompt will display ~ instead of the full directory path. It will look like this:

    john@my-home-server:~ $

## Dot Directories

<def>

> **Dot Directories** - are hidden directories whose names start with a dot symbol. Dot directories are normally used to store configuration data for the application named the same as the dot-directory without a dot. 
</def>

**For example:** 
- **Visual Studio Code** application data for user **John** on **Windows** is stored inside "**C:\Users\\John\\.vscode**"

Because the dot directories are hidden, we need to show them to be able to view them:

- In **Windows 11** : Open **File Explorer > View > Show > Hidden items**
- In **Linux** and **OS X** terminal : Add argument "**a**" to list command (it means show **all** files and folders including hidden)

      ls -a
    OR

      ls -la



## Permissions Explained
<br>

When we run command **ls -l** we can see file permissions displayed at the start of each line representing file or folder inside the folder we are currently in.

It looks something like this:

		drwxr-xr-x 5 admin developers 4096 Apr 10 02:21 repos


<br>

### Symbolic notation
<br>

Ten characters that represent symbolic notation of file or folder permisisons are:
drwxrwxrwx

The first character, d, signifies that the file is a directory.
It can be blank (-) or one of the following characters:

	d: directory
	c: Character device
	b: Block device
	s: socket
	p: pipe
	D: Door
	l: symbolic link etc.

- Owner - Next three characters (d**rwx**r-xr-x), are permissions for the owner of the file/folder

- Group - Next three characters (drwx**r-x**r-x), permissions for the group

- Others - Last three characters (drwxr-x**r-x**), permissions for all the other users

Meaning of each character is as follows:

		- : Empty - no permission
		r : Read
		w : Write
		x : Execute

<br>

### Numeric notation
<br>

Permisisons can be displayed in numeric notation.
Numerals used are the following:

	0 : No permission
	1 : Execute (x)
	2 : Write (w)
	4 : Read (r)

The numbers are added together to form one digit number that represents combination of rwx for either an owner, group or other. For example :

| Numeric | Symbolic |
| ------- | -------- |
| 7       | rwx      |
| 6       | rw-      |
| 3       | -wx      |
|         |          |

So the permission rwxrwxrwx is same as 777, rwxr-xr-x is same as 755, and so on.




## FSTAB File Syntax

In this section we just layout the full syntax of fstab entry.

### FSTAB ENTRY

Example line in /etc/fstab file (aka fstab entry):
    
     UUID=aa60d5a8-6a7f-43f6-8c41-8958ae018526 /mnt/my-drive ext4 defaults,auto,users,rw,nofail 0 0

Structure:

    {{DEVICE}} {{MOUNT_POINT}} {{FILE_SYSTEM_TYPE}} {{OPTIONS}} {{DUMP}} {{PASS}}

Fields Explained:

| Field            | Description                                                                                                                                                                                  |
| ---------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| DEVICE           | /dev location or UUID of the device/partition                                                                                                                                                |
| MOUNT_POINT      | The file path location where the device will be mounted.                                                                                                                                     |
| FILE_SYSTEM_TYPE | The type of file system on the device.                                                                                                                                                       |
| OPTIONS          | Mount options, separated by commas. See [FSTAB Options Section](#fstab-options) for the list of options and their meaning.                                                                   |
| DUMP             | Can be 0 or 1. Enable or disable backing up of the device/partition (the command dump). 0 means no backup. 1 means backup.                                                                   |
| PASS             | Can be 0, 1 or 2. Controls the order in which fsck checks the device/partition for errors at boot time. The root device should be 1. Other partitions should be 2, or 0 to disable checking. |

### FSTAB OPTIONS

NOTE: options with slashes mean that you can choose only one of the options separated by slash, for example: **auto/noauto** - means that you can write either **auto** or **noauto** but not both.

| Option                | Description                                                                                                                                                                                                                                                                                                                                                                                                                |
| --------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **auto/noauto**       | partition is mounted automatically on boot (or not)                                                                                                                                                                                                                                                                                                                                                                        |
| **ro/rw**             | “ro” is read-only, and “rw” is read-write.  If you want to be able to write to a file-system as the user and not as root, you’ll need to have “rw” specified.                                                                                                                                                                                                                                                              |
| **exec/noexec**       | partition can execute binaries (or not). Usually set to “noexec” for security                                                                                                                                                                                                                                                                                                                                              |
| **nouser/user/users** | Using user or users **defaults to noexec for all user accounts.** So if you need to execute binaries and still mount as a user, be sure to explicitly use **“exec”** as an option.                                                                                                                                                                                                                                         |
| **nouser**            | only root can mount the filesystem                                                                                                                                                                                                                                                                                                                                                                                         |
| **user**              | any user can mount the filesystem. Only the user that mounted a filesystem can unmount it again                                                                                                                                                                                                                                                                                                                            |
| **users**             | any user can mount or unmount the filesystem                                                                                                                                                                                                                                                                                                                                                                               |
| nofail                | the system will look for your disk (and partition) at boot time. If the device is plugged, the filesystem will be mounted. If not, the boot will continue as normal.                                                                                                                                                                                                                                                       |
| **sync/async**        | “sync” forces writing to occur immediately on execution of the command, which is ideal for floppies and USB drives, but isn’t entirely necessary for internal hard disks.  What “async” does is allow the command to execute over an elapsed time period, perhaps when user activity dies down and the like.  Ever get a message asking to your “wait while changes are being written to the drive?”  This is usually why. |

- These options are separated by a comma and no spaces, and can be put in any order.



## Format Device in Desktop UI

For Desktop formatting, you need to have a monitor and keyboard connected to your server machine. Or you can [RDP](#rdp---remote-connect---windows) into it from Windows computer.

Depending on your server's operating system, you will need to use different programs to format the external drive.

We describe below how to format the drive in **Linux Mint** and **Raspberry Pi** desktop.

### Linux Mint - Format on Desktop

In Linux Mint formating external drive is very easy. 

- Right click on the drive icon and select **Format**, if that option is available

If you can't see option Format on the context menu, you need to open program **USB Stick Formatter**

- Click on Start icon (LM) in the bottom left corner of the screen. This will open the main menu and you will see the list of programs.
- Start typing **format** and you should see **USB Stick Formatter** program
- Click on it to open it. 
- You will see 3 fields: USB stick, Filesystem, Volume label. Make sure to:     
  - select the correct USB stick - You can mouse over the USB stick icon, in the left file explorer panel, to see the name of the USB stick. For example: **/dev/sda1**
  - select **EXT4** as Filesystem 
  - type in a name for Volume label.For example:

    |              |           |
    | ------------ | --------- |
    | USB stick    | /dev/sda1 |
    | Filesystem   | EXT4      |
    | Volume label | my-drive  |

- Click **Format**
- Wait for the formatting to finish. You will see a confirmation message when it's done.

- Open file explorer
  - You should see your external drive mounted on the left panel under **Devices** section
- Click on it to select it
  - You should see a folder called **lost+found** inside it

- **Right click > Properties** on the drive to see the details. Note the location. It should say /media/{{USERNAME}} For example: **/media/admin**

By default, Linux mounts external drives onto **/media/{{USERNAME}}** folder. 

We will learn later how to mount the drive onto a different folder.


### Raspberry Pi - Format on Desktop

In Raspberry Pi we use program Gparted to format the external drive.

- Click on Main Menu - Start icon (Raspberry) in the top left corner of the screen. This will open the main menu and you will see the list of programs.
- You should see menu item **System Tools > GParted**. If you don't see it, you need to install it first. 

To install GParted:

- Click on **Main Menu > Preferences > Add/Remove Software**
- Type gparted in the search bar and hit **Enter**
- You should see 2 packages: GNOME partition editor and GNOME partition editor - common data
- Check the boxes next to both packages and click **Apply** at the bottom right of the window
- Wait for the installation to finish

You should be able to see GParted in the main menu now

- Click on **Main Menu > System Tools > GParted** to open it
- Select your external drive from the drop down menu in the top right corner of the window. It should be something like **/dev/sda1**
- You will see a list of partitions with only one partition named like **/dev/sda1**
- Righ-click on the partition and select **Format to > ext4**
- Click on Green tick icon at the top of the window to apply the changes

You will see a confirmation message when formating is completed.

- Reboot

- Open file explorer
  - You should see your external drive mounted on the left panel inside folder /media/{{USERNAME}}. For example: **"/media/admin/2.1 TB Volume"**


By default, Linux mounts external drives onto **/media/{{USERNAME}}** folder. 

We will learn later how to mount the drive onto a different folder.


