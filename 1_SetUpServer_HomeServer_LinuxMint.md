# Linux Server - Home Server - Linux Mint


|               |                                                   |                                                                                                 |
| ------------- | ------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| Copyright:    | © 2022 VEXIT , Tomorow is today ® , www.vexit.com |                                                                                                 |
| Author:       | Vex Tatarevic                                     |                                                                                                 |
| Date Created: | 2022-11-23                                        |                                                                                                 |
| Date Updated: | 2024-11-19                                        | Separated Linux Mint into separate book. Extracted from initial book Linux Server - Home Server |
|               |                                                   |                                                                                                 |


## Definitions

In this book we will use technical words that you may not be familiar with. 

Below is a table explaining these words. You can skip this table and refer to it when you encounter a word you don't understand. We will link these words to this table throughout the book.

|                              |                                                                                                                                                                                    |
| ---------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Local machine                | Your personal computer: Windows, Mac or Linux, that you use to connect remotely to home server                                                                                     |
| Server                       | Computer that we are using as a home server machine                                                                                                                                |
| Directory                    | Folder                                                                                                                                                                             |
| Machine                      | Computer                                                                                                                                                                           |
| Terminal                     | Program used for inputing commands to interact with operating system. On Windows we use **Git Bash** terminal program. On Linux and macOS, terminal program is called **Terminal** |
| CLI (Command Line Interface) | A generic name for any command line interface that accepts text input to execute programs. Terminal is an example of program with such interface.                                  |

We will use the following sample usernames and machine names:

| Machine type | Machine name | Usernames                  |
| ------------ | ------------ | -------------------------- |
| Local        | my-pc        | Jon                        |
| Server       | my-server    | sysadmin , main_user, jons |

**Note:** sample personal username on local is **jon** while on server is **jons** (s is for server) to avoid confusion with jon on local machine.



## Install Local machine Software

- Log into your [Local machine](#definitions). This is your personal computer that you use every day. We will use this computer to connect remotely to our server at the later stage.

### Install Git Bash

- On your [Local machine](#definitions)
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
- Open Terminal: <kbd>CMD + Space</kbd> > type Terminal > <kbd>Enter</kbd>
- Install Telnet

  ```bash
  brew install telnet
  ```

  - If you don't have **brew** installed, go to [brew.sh](https://brew.sh) and follow the instructions to install it.

On **Linux**:
- Open Terminal: <kbd>CTRL + ALT + T</kbd>
- Install telnet

  ```bash
  sudo apt install telnet
  ```


## Install Server Operating System

We will use **Linux Mint** as a server operating system.


> **Linux Mint** - is a free and open-source operating system based on Debian and Ubuntu. It is a great operating system for beginners and advanced users. It is easy to use and has a lot of software available for it. It is a great choice for a home server operating system.

You need to have avaliable a spare computer (desktop or laptop) to install Linux Mint on it.

### Create Bootable USB

- **Download Linux Mint ISO** - Go to [linuxmint.com](https://www.linuxmint.com/) and download Linux Mint operating system iso image file (.iso)

- **Download Balena Etcher** - Go to [balena.io/etcher](https://www.balena.io/etcher). Download Etcher, install it and run it.

- Create **Bootable USB** 
  - Insert empty USB of at least 4 GB in size
  - Open **balenaEtcher** app
  - Click on **"Flash from file"** and select Linux Mint **.iso** file that you have downloaded in the previous step
  - Click on **Select target** and select your USB drive
  - Click on the button **"Flash!"** to start the process. Once the process is finished, you will get a message **"Flash completed"**
  - Your USB drive is now bootable with Linux Mint operating system

From this point on, we will boot our spare computer from USB drive and install Linux Mint operating system on it. We will refer to this spare computer as **server computer**.

### Boot Computer from USB

> BIOS - is a program that runs on a computer when it is first turned on. It is responsible for loading and starting the operating system. BIOS is also responsible for configuring the hardware of the computer. BIOS is stored on a chip on the motherboard of the computer. BIOS is also called **"firmware"**.

Boot Order is the order in which BIOS tries to boot operating systems from different devices.

For safety reasons, computers try to boot operating system from the internal hard drive by default. 

We need to change the boot order in BIOS of your server computer to boot from USB first.

- **Enter BIOS**
    - **Turn off** your server computer
    - **Turn on** your server computer and **immediately** start pressing **F2** key repeatedly until you see the BIOS screen. If this does not work, try these keys: **ESC, DEL, F1, F2, F8, F10, F11, F12**. If none of these keys work, you need to **search online** for your computer model and find out which key to press to enter BIOS.

- **Change Boot Order in BIOS**   
    - Use **Arrow keys** to navigate to **Boot** tab
    - Use **Arrow keys** to navigate to **Boot Priority** and press **Enter**
    - Use **Arrow keys** to navigate to **Boot Option #1** and press **Enter**
    - Select **USB** and press **Enter**
    - Press **F10** to save and exit
  
**If you CAN NOT SEE USB** in the list of boot options, then you need to enable USB boot in BIOS, or rather disable **Secure Boot**. This is a typical BIOS setup on Windows computers.

- **Disable Secure Boot**
    - Enter BIOS   
    - Use **Arrow keys** to navigate to **Boot** tab
    - Use **Arrow keys** to navigate to **Secure Boot** and press **Enter**
    - Select **Disabled** and press **Enter**
    - Press **F10** to save and exit

If you still cant see USB in the setting called Boot Option #1, then you need to enable **Legacy Boot** in BIOS.  

- **Enable Legacy Boot**
    - Enter BIOS   
    - Use **Arrow keys** to navigate to **Boot** tab
    - Use **Arrow keys** to navigate to **Boot List Option** and press **Enter**
    - Select **Legacy** and press **Enter**
    - You should now see setting called **Set Boot Priority** and underneath it you should see options like:
      - 1st Boot Priority [Network]
      - 2nd Boot Priority [Hard Drive]
      - 3rd Boot Priority [Diskette Drive]
      - 4th Boot Priority [CD/DVD/CD-RW Device]
      - 5th Boot Priority [USB Storage Device]
    - Select 1st Boot Priority [Network] and press **Enter** to open the list of options
    - Select **USB Storage Device** and press **Enter**
    - Press **F10** to save and exit
    
The system will restart and this time it should boot from USB.

### Run in Live Mode

When you boot from USB, you will see the Linux Mint installation screen.

You should see selected a line that says something: **"Start Linux Mint 21.2 Cinnamon 64-bit"**, or similar depending on the version of Linux Mint you have downloaded.

- Hit **Enter** 
- Linux Mint will load in **Live Mode**. This means that you can try out Linux Mint without installing it on your computer.
- You will see a disc icon on the desktop called **"Install Linux Mint"**. You can click on it to start the installation process when you decide to install Linux Mint and wipe out your existing operating system.
 

### Install Linux Mint

During the installation you will be asked to set username and password.

Set username to **"main_user"** and password to **"main"**.

These are just temporary credentials. We will later change both, username and password.


## Start Using Terminal

From this point on we will configure the system via [Command Line Interface (CLI)](#definitions) because that's how we will communicate with server at the later stage when we start connecting remotely to it.

- **Open Terminal** : hit keyboard shortcut <kbd>CTRL + ALT + T</kbd> to open the Terminal

- This will open the Terminal command line interface and you will see the [command line prompt](#command-line-prompt): 

```bash
main_user@my-server:~ $
```

- You can now type the commands.

- **Close Terminal** - type command `exit` to close the Terminal 

## Configure Terminal


### Customize Prompt

We will change the prompt to look similar to popular Git Bash terminal on Windows: more colourized and cursor on new line.

- CD into user home: `cd ~`
- Open bash profile : `codium .bashrc` or `nano .bashrc`
- Search for Terminal prompt configuration block
  - If using nano - hit <kbd>CTRL + W</kbd> to open the search prompt
  - If using VS Code or VS Codium - hit <kbd>CTRL + F</kbd>
  - Paste this line `if [ "$color_prompt"` and hit <kbd>ENTER</kbd> to go to the start of the block
  - The default prompt code block should look like this:

```bash
if [ "$color_prompt" = yes ]; then
    PS1='${debian_chroot:+($debian_chroot)}\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\$ '
else
    PS1='${debian_chroot:+($debian_chroot)}\u@\h:\w\$ '
fi
unset color_prompt force_color_prompt
``` 

- Comment out default prompt:

```bash
#------------------------------------------
#       Default Prompt
#------------------------------------------
# if [ "$color_prompt" = yes ]; then
#     PS1='${debian_chroot:+($debian_chroot)}\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\$ '
# else
#     PS1='${debian_chroot:+($debian_chroot)}\u@\h:\w\$ '
# fi
# unset color_prompt force_color_prompt
#------------------------------------------
```

- Add custom prompt like in Git Bash

```bash
#------------------------------------------------
#       Git Bash Prompt
#------------------------------------------------
parse_git_branch() {
 git branch 2> /dev/null | sed -e '/^[^*]/d' -e 's/* \(.*\)/(\1)/'
}
if [ "$color_prompt" = yes ]; then
 PS1='${debian_chroot:+($debian_chroot)}\[\e[38;5;34m\]\u\[\e[38;5;171m\]@\h\[\033[00m\] \[\e[38;5;220m\]\w \[\e[38;5;14m\]$(parse_git_branch)\[\033[00m\] \n\$ '
else
 PS1='${debian_chroot:+($debian_chroot)}\u@\h:\w$(parse_git_branch)\$ '
fi
#-------------------------------------------------
```

- Save and Close .bashrc (in nano : <kbd>CTRL + S</kbd> , <kbd>CTRL + X</kbd>)
- Reload .bashrc in Terminal `source .bashrc`
  - The terminal prompt should change. If not, then restart the Terminal

**NOTE:** To change colours to something else you can go to bash prompt generator website to generate the code for .bashrc.
- Go to: https://robotmoon.com/bash-prompt-generator/ to play with different prompt colours and generate bash code for prompt customization inside .bashrc 


### Customize Terminal Background Color

This is how you can change background color of terminal to black rather than default gray color:

- Open Terminal : <kbd>CTRL + ALT + T</kbd>
- Open Colours: **Edit > Preferences > Profiles - Unnamed (left menu) > Colours (top tab)**
- Uncheck **"Use colours from system theme"**
- In **Default Colour** field, click on **Background** square color picker
- Type in black colour **"#000000"**
- Click **Select**, Click **Close**

### Customize Terminal Cursor

This is how you can change cursor to thin vertical line, rather than default vertical block:

- Open Terminal : <kbd>CTRL + ALT + T</kbd>
- Open Text: **Edit > Preferences > Profiles - Unnamed (left menu) > Text (top tab)**
- Select: **Cursor > Cursor shape : I-Beam**
- Click **Close**

 
## Update and Upgrade system


**NOTE:** You can skip this section as your system has already been updated and upgraded in the Setup Wizard.

- Update system, type command:

  ```bash
  sudo apt-get update
  ```

- Upgrade system, type command:

  ```bash
  sudo apt-get upgrade
  ```

## Shut Down via CLI

When turning off your Server machine, you should always shut down safely.

- To safely turn off your Server, type in the following command

  ```bash
  sudo shutdown -h now
  ```

  - The **-h** option will halt the working processes. If you remove -h from the above command, it will directly shutdown the machine without waiting for anything, which is not recommended.

- To shut down after 10 minutes type

  ```bash
  sudo shutdown -h 10
  ```

- To cancel scheduled shutdown

  ```bash
  shutdown -c
  ```

## Configure the System


### Set up Wi-Fi

**YOU CAN SKIP**

> **NOTE:** You can skip this WiFi setup section, because your Wi-Fi should have already been set in the Setup Wizard. This section shows you how to set up WiFi in the command line interface on any version of Linux operating system.

- Get the name of wireless network interface

  ```bash
  iwconfig
  ```

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
    
- Activate wireless network interface

  ```bash
  sudo ifconfig wlan0 up
  ```

- Connect to your wireless network using WPA (Wi-Fi Protected Access) encryption

  ```bash
  sudo wpa_passphrase {{WIFI_NAME}} {{WIFI_PASSWORD}} > /etc/wpa_supplicant.conf
  ```

  - Replace {{WIFI_NAME}} and {{WIFI_PASSWORD}} with your actual WiFi name and password

- Check that your WiFi details have been saved:

  ```bash
  sudo nano /etc/wpa_supplicant/wpa_supplicant.conf
  ```

	- You should see

        ```bash
	  country={{YOUR_COUNTRY_CODE}}

	  network={
	  	  ssid={{YOUR_WIFI_NAME}}
	  	  psk={{YOUR_WIFI_PASSWORD}}
	  }
        ```
    - Exit nano editor: <kbd>CTRL + X</kbd> , <kbd>Y</kbd> , <kbd>Enter</kbd>

- Reboot

  ```bash
  sudo reboot
  ```

- Check that you are connected to your WiFi

  ```bash
  iwconfig
  ```

### Enable/Disable root user 

<def>

>**root** account is a superuser account that exists in system by default and it can execute any priviledged actions without using prefix **sudo**.
</def>

It is not advisable to ever enable **root** account but there are some situation when we may need it.

#### Enable root

- Log in as **main_user**

- Enable "**root**" user

	  sudo passwd root

  - This will prompt you for password

- Enter password **root**

- Reboot (restart computer)

	  reboot

- Log in with username **root** and password **root**

  **NOTE:** If using the Dektop view, when you log in with **root**, you will not see the menu at the top. In this case, hit <kbd>CTRL + ALT + T</kbd> to open the **Terminal**. Now you will be able to execute the commands with root.

- Execute priviledged command "apt-get update"

      apt-get update

  - This time we did not have to use prefix sudo

  **IMPORTANT:** Make sure to disable the root user after you complete priviledged actions available only to root user

- Reboot computer

      reboot

#### Disable root

- Log in with non-root account

- Disable **root** account

	  sudo passwd -l root

- Restart computer

      reboot
    

### Change Username and Group Name


In this section we will change initially created generic username **"main_user"** and password **"main"** to our own username and password.
 
In order to change our username we must log in as super user account called **root**.

This is the only reason why we will enable user root in this unique situation. Normally user **root** should always be disabled and instead all the commands that require root priviledges, can be executed by prefixing them with keyword **sudo**.

After we are finished with executing priviledged actions using root account, we must disable the **root** account.

> **root** account, in Linux operating system, is a superuser account that exists in system by default and it can execute any priviledged actions without using prefix **sudo**.


- Log in as **main_user**

- **Enable root account** and reboot

      sudo passwd root
      sudo reboot

- Log in as **root**

- If in Desktop view : Open Terminal <kbd>CTRL + ALT + T</kbd>
  
- Change username **"main_user"** to **"jons"** - where you should use your own username instead of "jons" 

  ```bash
  usermod -l jons main_user
  ```
  
- Change password for jons to "mypassword"

  ```bash
  passwd jons
  ```
  - Enter password **mypassword**

- Change user's directory name to jons

	  usermod -m -d /home/jons jons

  
  - `-d /home/jons`: Sets the new home directory for the user `jons`.
  - `-m`: Moves the content from the user's current home directory (e.g., `/home/main_user`) to the new home directory (`/home/jons`).
    - If `/home/jons` does not exist, it will be created.
    - All files and subdirectories from the old home are moved to the new location.
    - The ownership of the files remains with the user (`jons`).
    - The old home directory (`/home/main_user`) will remain as an empty directory unless you delete it manually.

- Delete the old home directory

      rmdir /home/main_user

- Change user's group name to jons

      groupmod -n jons main_user


- Reboot: `reboot`

- Log in as **"jons"**, with password **"mypassword"**

- Change password to a strong password - run command `passwd`

- Confirm that **jons** account has sudo privileges, by running system update command:

  ```bash
  sudo apt-get update
  ```

  - You will be prompted for password

        [sudo] password for jons:

  - Type in password to continue

**NOTE:** When we changed the username main_user to jons, it has reset the way sudo behaves to default - asking for password every 15 minutes.
In the next section we will configure sudo not to prompt **jons** to enter password every time we want to run a priviledged command.

- **IMPORTANT:** **Disable** **root** account

	  sudo passwd -l root

**NOTE:** From this point on we will not use **root** account again.


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

- Save and close the file: <kbd>CTRL + S</kbd> <kbd>CTRL + X</kbd>

- Reboot and log in with **jons**

- Run update command to make sure you are not being prompted for password:

      sudo apt-get update

  - You should NOT be prompted for password this time

### Create User sysadmin

In the previous sections we showed you how to change existing username and password.

Now we will learn how to create a priviledged account called **sysadmin**. 

We will create the username **"sysadmin"** which will be the main account for adminsitration of our server. In other versions of Linux we would normally call this user **admin**, but in Linux Mint this is already a group name, so we have to call our admin user something else. Therefore sysadmin is a good alternative name.


**IMPORTANT:** **Disable** **root** account - in case you missed to do it before. 

```bash
sudo passwd -l root
```

**NOTE:** **jons** is already a priviledged account, so we can execute any priviledged commands with jons account by prefixing them with keyword **sudo**

- Log in with **jons**

- Create user sysadmin

	  sudo adduser sysadmin
      
  - Provide password: **sysadmin** and Full Name: **System Admin**. For all other fields hit **Enter** to continue.

  - The last question of the user creation wizard will ask you if the information is correct. Type **y** and hit **Enter**.

  - You should see :
		
		  Adding user `sysadmin' ...
		  Adding new group `sysadmin' (1001) ...
		  Adding new user `sysadmin' (1001) with group `sysadmin' ...
		  Creating home directory `/home/sysadmin' ...
		  Copying files from `/etc/skel' ...
		  New password:
		  Retype new password:
		  passwd: password updated successfully
		  Changing the user information for sysadmin
		  Enter the new value, or press ENTER for the default
		  		Full Name []: Admin
		  		Room Number []:
		  		Work Phone []:
		  		Home Phone []:
		  		Other []:
		  Is the information correct? [Y/n] y


**IMPORTANT:** You should create a **strong password** for your sysadmin account. Password we are using here is just for practice.

**NOTE:** When you create user **sysadmin** with **adduser** command, this will also create a group **sysadmin** and a home directory **/home/sysadmin** 

- Go inside /home and list the contents

      cd /home
      ls -l

  - we will get a message:

        ls: cannot open directory '.': Permission denied


   - This is because we configured our system, in the previous section, to hide user directories. 
 
   - As a priviledged user we can still see users' directories by using sudo keyword

         sudo ls -l

  - You should see that a new directory **sysadmin** has been created.

- Try to enter into sysadmin user's directory /home/sysadmin

      cd /home/sysadmin

  - You will get a message

        -bash: cd: /home/sysadmin: Permission denied

  - Our security configuration works well. No user can enter inside the directory of the other user.

- Give elevated priviledges to sysadmin - assign sysadmin user to the **sudo** group:

	  sudo usermod -aG sudo sysadmin

  - The **-aG** flag stands for Append and Group. This command instructs **usermod** command to append the username to a group, which in this case is the sudo group.

  - Keyword **sudo** stands for: **"superuser do"**      
  - You now have an **sysadmin** account with sudo priviledges. This means that sysadmin account can execute commands which require elevated root priviledges by prefixing those commands with keyword **sudo**.


- Check what groups user **sysadmin** is in

	  groups sysadmin

  - You should get

        sysadmin : sysadmin sudo

- Reboot computer

**NOTE:** From this point on we will only use **sysadmin** account.



### Change Computer Name

**NOTE:** THROUGHOUT THIS BOOK WE WILL REFER TO YOUR SERVER COMPUTER NAME AS "**my-server**". Wherever you see "my-server", replace it with the actual name of your home server machine.

- Log in as **sysadmin**

- Change name in **hostname file** 

  - Open file "hostname", located in the "/etc" directory, using nano editor

	    sudo nano /etc/hostname
  
  - Change the line of text that says "**raspberrypi**" to your desired computer name (e.g. "my-server"). 
	
  - Save and close the file: <kbd>CTRL + S</kbd> <kbd>CTRL + X</kbd>

<def>

>**nano** - is a popular simple text editor used in Linux to edit text in files.
</def>


- Change name in **hosts file**

  - Open file "hosts", located in the "/etc" directory, with nano

    ```bash
    sudo nano /etc/hosts
    ```
	
  - Change the line of text that has your old computer name. It should be the last line that starts with 127.0.0.1 

    ```bash
    127.0.0.1       localhost
    ::1             localhost ip6-localhost ip6-loopback
    ff02::1         ip6-allnodes
    ff02::2         ip6-allrouters
    
    127.0.1.1       old-computer-name
    ```
      - Change **old-computer-name** to your new computer name (e.g. **my-server**)
	

  - Save and close the file: <kbd>CTRL + S</kbd> , <kbd>CTRL + X</kbd>

- Reboot computer

  ```bash
  sudo reboot
  ```

- Log in with **sysadmin**

- Your command line prompt should now show

  ```bash
  sysadmin@my-server:~ $
  ```

- Confirm hostname has been changed

  ```bash
  hostname
  ```

	- it should now say **my-server**

## Set up Network

### Check Local Network Connectivity

- **Ping the machine** - Check that your computer is on the network using ping command

	  ping my-server.local

  - Hit <kbd>CTRL + C</kbd> to exit PING (Packet Internet Groper)

  - You should see lines of something like this repeating:

		64 bytes from 192-168-1-105.tpgi.com.au (192.168.1.105): icmp_seq=1 ttl=64 time=0.102 ms
	
  - **NOTE**: 192.168.1.105 in this example is Server machine's IP address on your local network


You can try running these 2 commands for more information about the network:
1. `ifconfig`
2. `ip addr show`

**To learn more about these commands and how the network works go to Appendix section** [Networking](#networking).

### Get Internal IP from Command Line

- **Print internal IP address** of this machine

  ```bash
  hostname -I
  ```

  - You should see something like:
  
    ```bash
    192.168.1.105 fe80::6941:c5ff:fe0f:3033
    ```
- **Note-down the IPv4 address**, which is **192.168.1.105** in above example. The address starting with **fe80::** is IPv6 address that we will not use in this book.


**For more explanation go to Appendix section** [Networking](#networking).

### Open Router Admin

  - On your [Local machine](#definitions) open any browser e.g. Chrome, and navigate to your local network **router-admin** web-page by typing in the address bar the following url:

        http://192.168.1.1

  - If that does not work, then try:

        http://192.168.0.1
   
  - If that does not work, search on Google "what is router admin url for {{NAME_OF_YOUR_ROUTER_BRAND}}", for example "what is router admin url for tp-link"

### Get Internal IP from Router Admin

- In your Router Admin - **Verify that the your server machine is visible on router admin website** 

  - You should see a link that says something like "**Wireless Clients**". It may say something different depending on your router brand.

  - On the list of connected wireless clients you should see a name of your [Server computer](#definitions) ( e.g. **my-server** ) and its **Internal IP Address** next to it (e.g. 192.168.1.105)

- Note down the Internal IP address of your Server machine. You will be using it in the next section to configure Port Forwarding.


### Set up Static Internal IP

Internal IP addresses are not static. They get changed by the router's DHCP server.

<def>

> **DHCP (Dynamic Host Configuration Protocol)** - is a protocol that provides quick, automatic, and central management for the distribution of IP addresses to devices on the network within a network. A DHCP server dynamically issues unique IP addresses to devices on the network and automatically configures other network information. In most homes and small businesses, the router acts as the DHCP server.
</def>

We need to make sure that our home server machine always has the same IP address on the local network (static IP) so that we can reliably connect to it.

- In your router admin look for **LAN Settings**.
  For example on tp-link modem go to : **Advanced > Network > Lan Settings**

  - You should see sections: **DHCP Server, Client List, Address Reservation**

- In **Client List** find your server computer and note it's **MAC Address** and **Assigned IP** for example 192.168.1.105
- In **Address Reservation** section, click **Add** button which will prompt you to enter MAC and IP address.
  - Type in manually MAC and IP or click on button SCAN if available and select your device from list.
  - Click OK
  
- Your server computer now has static internal IP and it will not be changed by DHCP server.


## Obtain Static IP Address from ISP


**SKIP this section if you do not want to open your server to Internet or pay extra to your ISP for static IP.**.
You will still be able to access your server from other machines on your local network but not on the internet.

In this case go to section [Set up Firewall](#set-up-firewall).

__________


By default, your **ISP (internet service provider)** will assign your local network a **Dynamic IP Address** - which keeps on changing all the time.

In order for your server computer to be reachable from outside world (by other computers on the internet), it must have a **Static IP Address**.

- Contact your ISP and ask them to upgrade your internet connection plan to one that provides a **Static IP Address**


### Get External IP

- [Open Router Admin](#open-router-admin)

- You should see the **External IP** address of your router on the main page of the router admin website. It should look something like this:

      IP Address: 113.216.184.151

### Configure Port Forwarding

**SKIP this section if you do not want to open your server to Internet**, but rather just want to access it from your local network. In this case go to section [Set up Firewall](#set-up-firewall).

**NOTE:** You can only do this if you have a **Static External IP** that your ISP has provided for you.

In order to access our Server from anywhere via internet, we need to first set up **port forwarding** on our router.


<def>

> **Port Forwarding** - allows incoming connections from broader Internet (in technical terms known as WAN (Wide Area Network)) to reach a specific device/port on a **LAN** (Local Area Network). Port forwarding is achieved by using **NAT** (Network Address Translation) table that maps **External Port** to the **Internal IP** address.
</def>

- On your [Local machine](#definitions), open the internet browser window and go to **router admin** webpage which should be located at url **http://192.168.1.1/** or **http://192.168.0.1/**

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
	
  
<def>

> **Network Port** - is a logical number that identifies to which local **process** or **service**, the specific incoming internet traffic should be directed towards.
> 
> Ports are classified into 3 main categories:
>
> 1. **Well Known Ports** - ( 0 - 1023 ) - are used by system or processes run by root or with specific privileges.
>
> 2. **Registered Ports** - ( 1024 - 49151 ) - are used by programs run by users in the system.
>
> 3. **Dynamic Ports** - ( 49152 - 65535 ) - are not assigned for any specific purpose.
</def>



<br>

### Check Outside Connectivity

Skip this if you don't have a static IP.

--------------------------

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

  - Add homebrew to your PATH. **NOTE:** replace **jon** with your username:

        (echo; echo 'eval "$(/opt/homebrew/bin/brew shellenv)"') >> /Users/jon/.bash_profile

        eval "$(/opt/homebrew/bin/brew shellenv)"

- Install Netcat on your Local machine

  |         |                                          |
  | ------- | ---------------------------------------- |
  | Windows | Go to: https://nmap.org/download.html    |
  | Mac OS  | Run command: **brew install netcat**     |
  | Linux   | Run command: **sudo apt install netcat** |
  |         |                                          |

- Check on your Local machine if you can connect from outside to your server computer ip and port e.g. 113.216.184.151:8333

  - On **Linux** and **Mac OS** run

        nc -zv 113.216.184.151 1234

  - On **Windows** open command prompt and run

        ncat -zv 113.216.184.151 1234



## Set up Firewall

<def>

> Firewall - is a network security system that monitors and controls incoming and outgoing network traffic based on predetermined security rules. It  restricts internet traffic in to, out of, or within a private network. Firewall functions by selectively blocking or allowing data packets. Its purpose is to prevent anyone inside or outside of a private network from engaging in unauthorized web activities.
</def>

<def>

> **UFW** - **Uncomplicated Firewall** is a standard CLI program for working with the firewall on Linux.
</def>

### Install and Enable UFW

**NOTE:** This sequence is safe for remote access (e.g., via SSH) because we add critical rules before enabling the firewall. If doing this physically, it's even safer.

- Install ufw

  ```bash
  sudo apt install ufw
  ```

- Check status:

  ```bash
  sudo ufw status
  ```

  - You may get

    ```bash
    ERROR: Couldn't determine iptables version
    ```

    OR

    ```bash
    Status: inactive
    ```

- Reboot:

  ```bash
  sudo reboot
  ```

- Check status again: `sudo ufw status`
  - This time you should get: `Status: inactive`

### Add Firewall Rules (Before Enabling)

We add rules while UFW is inactive to ensure your SSH connection isn't disrupted when we enable it later.

- Add port 22 - the standard port for SSH (Secure Shell) connections, which is how we will connect to our server remotely.

  ```bash
  sudo ufw allow 22
  ```

- Add another port 1234

  ```bash
  sudo ufw allow 1234
  ```

- Show firewall status (still inactive, but rules are queued)

  ```bash
  sudo ufw status
  ```

  - You should see

    ```bash
    Status: inactive
    ```

### Enable UFW

Now that rules are in place, enable the firewall safely.

- Enable firewall:

  ```bash
  sudo ufw enable
  ```

  - You should see

    ```bash
    Firewall is active and enabled on system startup
    ```

- Check existing firewall rules (list of allowed ports)

  ```bash
  sudo ufw status
  ```

  - You should see: `Status: active`
  - With your rules listed, e.g.:

    ```bash
    Status: active

    To                         Action      From
    --                         ------      ----
    22                         ALLOW       Anywhere
    1234                       ALLOW       Anywhere
    22 (v6)                    ALLOW       Anywhere (v6)
    1234 (v6)                  ALLOW       Anywhere (v6)
    ```

- Reboot

  ```bash
  sudo reboot
  ```

### Remove Firewall Rule

- Display a numbered list of rules

  ```bash
  sudo ufw status numbered
  ```

  - You should see something like

    ```bash
         To                         Action      From
         --                         ------      ----
    [ 1] 22/tcp                     ALLOW IN    Anywhere
    [ 2] 1234                       ALLOW IN    Anywhere
    [ 3] 22/tcp (v6)                ALLOW IN    Anywhere (v6)
    [ 4] 1234 (v6)                  ALLOW IN    Anywhere (v6)
    ```

- Remove port 1234 by rule number

  ```bash
  sudo ufw delete 2
  ```

  - NOTE: When you remove the rule, the number position of all rules after it , decreases by one

- Display numbered list again

  ```bash
  sudo ufw status numbered
  ```

  - You should now see

    ```bash
         To                         Action      From
         --                         ------      ----
    [ 1] 22                         ALLOW IN    Anywhere
    [ 2] 22 (v6)                    ALLOW IN    Anywhere (v6)
    [ 3] 1234 (v6)                  ALLOW IN    Anywhere (v6)
    ```

- Remove port **"1234 (v6)"** by rule number

  ```bash
  sudo ufw delete 3
  ```
	

## Remotely Connect with SSH

Ref: https://www.raspberrypi.org/documentation/remote-access/ssh/

<def>

> **SSH (Secure Shell)** - is a cryptographic network protocol for operating network services securely over an unsecured network. Typical applications include remote command-line, login, and remote command execution, but any network service can be secured with SSH.
</def>

In order to connect to our Server remotely (from the Local machine), we need to enable SSH on the Server.



### Step 1 - Enable SSH

- Physically log into your **Server machine** with **sysadmin** user.

- Open terminal

- Check if SSH is installed:

  ```bash
  sudo systemctl status ssh
  ```

  - If not installed,you will get this message:

     `Unit ssh.service could not be found.`

  - If not installed, install it:

    ```bash
    sudo apt update
    sudo apt install openssh-server
    ```

- Make sure that SSH is running

  ```bash
  sudo systemctl start ssh
  ```
  
- Enable SSH  - This will make sure that SSH starts automatically after reboot

  ```bash
  sudo systemctl enable ssh
  ```

- Confirm that SSH is active

      sudo systemctl status ssh

  - You should see a green dot and a text like

        ssh.service - OpenBSD Secure Shell server
            Loaded: loaded (/lib/systemd/system/ssh.service; enabled; vendor preset: enabled)
            Active: active (running) ...
            ...


### Step 2 - Remote Connect with SSH
 
- Physically log into your **Local machine** with user **Jon**

We can connect remotely in 2 ways: 
1. Via **Local Network (LAN)** 
2. Via **Internet (WAN)** - In this case you will need to have a **Static External IP** leased from your ISP

NOTE: In both cases you would have to have [set up Static Internal IP](#set-up-static-internal-ip)

- For LAN get Server's **Internal IP**
  - [Open Router Admin](#open-router-admin)
  - [Get Internal IP](#get-internal-ip) of your server e.g. my-server
  - Suppose that we are using the following details
    |          |                   |
    | -------- | ----------------- |
    | Port     | 22 (default port) |
    | Username | sysadmin          |
    | IP       | 192.168.1.105     |

- For WAN get **External IP** and **External Port**
  - [Get external IP Address](#get-external-ip)
  - **Get the port of the Server machine** - The port should be the same as the one you configured in the section [Configure port forwarding](#configure-port-forwarding) e.g. 1234

  - Suppose that we are using the following details

    |          |                 |
    | -------- | --------------- |
    | Port     | 1234            |
    | Username | sysadmin        |
    | IP       | 113.216.184.151 |

- Open **Git Bash** or **Terminal**

- **Connect to the Server** using ssh command

      ssh -p {{EXTERNAL_PORT}} {{USERNAME}}@{{IP_ADDRESS}}

    **For example:**

    - Via Internet (using custom port for security):

      ```bash
      ssh -p 1234 sysadmin@113.216.184.151
      ```

    - Via Local Network (using default SSH port 22):

      ```bash
      ssh sysadmin@192.168.1.105
      ```


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

          ssh -p 1234 sysadmin@113.216.184.151

      OR

          ssh sysadmin@192.168.1.105

- You should see a password prompt

      sysadmin@113.216.184.151's password:

- After you type in the correct password, you will be remotely logged into the Server

- You can do everything on SSH remote connection as if you were logged in on the physical server computer. You should see a [command line prompt](#command-line-prompt) like this:

  ```bash
  sysadmin@my-server:~ $
  ```

- **Disconnect from the Server** : Hit <kbd>CTRL + D</kbd>

- From this point onwards you can use your Local machine to access the Server and run all the commands on it.
	- Therefore you can disconnect monitor and keyboard from the Server computer if you wish so.

>**NOTE:** On your Local machine, ssh has created a [dot directory](#dot-directories) called **.ssh** inside the [user home directory](#user-home-directory)
- To see the hidden .ssh directory : 
  - Open terminal 
  - CD into userhome directory: `cd ~`
  - List contents of the directory: `ls -la`
  - You should see a hidden folder called **.ssh**

- Go inside the .ssh folder and view contents of it

      cd .ssh
      ls -l

  - You should see the file called **known_hosts**

- Open **known_hosts** file: `nano known_hosts`

- You should see something like:

      [113.216.184.151]:1234 ssh-ed25519 jfY8xUO1cfDAlbbB ...
      [113.216.184.151]:1234 ecdsa-sha2-nistp256 FUbUx1a3sM= ...

  OR if you connected to server on LAN

      [192.168.1.105]:22 ssh-ed25519 jfY8xUO1cfDAlbbB ...
      [192.168.1.105]:22 ecdsa-sha2-nistp256 FUbUx1a3sM= ...

  - These are identifying keys related to your Server. If you connect to any other servers, similar keys will be added to this file to mark those servers/hosts as known hosts.


### Step 3 - Passwordless Connect with RSA


<def>

> **RSA (Rivest-Shamir-Adleman) encryption** - is a public-key encryption used for securing data transmission of e-mail and other digital transactions over the Internet. "RSA" is the acronym for the surnames of Ron Rivest, Adi Shamir and Leonard Adleman, who publicly described the algorithm in 1977.
</def>

SSH protocol uses RSA encryption to enable Local machine to connect remotely to Server computer by use of **public and private key**, instead of having to type in the password.

SSH files are stored inside **".ssh"** folder located in the [User Home directory](#user-home-directory).


- Let's suppose that we have the following set-up:

  - Local machine:

    |                    |               |
    | ------------------ | ------------- |
    | Local machine Name | my-pc         |
    | Operating System   | Windows       |
    | Username           | Jon           |
    | User directory     | C:\Users\Jon\ |

    - Replace Jon above with your username


  - Server computer:

    |                                |                 |
    | ------------------------------ | --------------- |
    | Server machine Name (hostname) | my-server       |
    | Operating System               | Linux Mint      |
    | Username                       | sysadmin        |
    | User directory                 | /home/sysadmin/ |

    - Replace **my-server** above with your server name
  
- On the [Local machine](#definitions) open **Git Bash** (on Windows) or **Terminal** (on Linux and Mac)

- **Create RSA encryption keys on Local machine** using **ssh-keygen** command:
	
  ```bash
  ssh-keygen 
  ```

  - You should see a prompt:

      ```bash
	Generating public/private rsa key pair.
      Enter file in which to save the key (/c/Users/Jon/.ssh/id_rsa):
      ```

      OR if your machine is using ed25519 algorithm

      ```bash
      Generating public/private ed25519 key pair.
      Enter file in which to save the key (/c/Users/Jon/.ssh/id_ed25519):
      ```

    The prompt gives a suggestion to use a default file called **id_rsa** or **id_ed25519** inside **.ssh/** folder to store the private key.
    
    However this is not good enough for us. We want to structure our files with the view that we will **connect to many other servers**.
    
  - **Copy the suggested file path** (/c/Users/Jon/.ssh/id_rsa) or (/c/Users/Jon/.ssh/id_ed25519) and **append the Server name at the end** like:

      `/c/Users/Jon/.ssh/id_rsa_my-server`

      or

      `/c/Users/Jon/.ssh/id_ed25519_my-server`
	
  - Hit <kbd>Enter</kbd>
  - Next you should see passphrase prompts. Just hit <kbd>Enter</kbd> without typing in any text. 

	  Enter passphrase (empty for no passphrase):
	  Enter same passphrase again:

  - The keys will be generated and you will see the following output (where "my-pc" is the name of your Local machine, "Jon" is your username on your Local machine, "my-server" is your Server computer name):

        Your identification has been saved in /c/Users/Jon/.ssh/id_rsa_my-server
        Your public key has been saved in /c/Users/Jon/.ssh/id_rsa_my-server.pub
        The key fingerprint is:
        SHA256:h7FUEfiH8PtvNANPjc7uZM1VH6pGG/5LmI/PNp83M6E Jon@my-pc
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

**NOTE:** your file name will start with **id_rsa** or **id_ed25519** and then the server name. We will refer to it as **{{SSH_KEY_FILE_NAME}}**

- **Copy the public key to the Server machine** using `ssh-copy-id` command. 

  ```bash
  ssh-copy-id -i ~/.ssh/{{SSH_KEY_FILE_NAME}}.pub -p {{SERVER_PORT}} {{SERVER_USERNAME}}@{{SERVER_IP_ADDRESS}}
  ```

  **For example:**

    - Via Internet (using custom port for security):

      ```bash
      ssh-copy-id -i ~/.ssh/id_rsa_my-server.pub -p 1234 sysadmin@113.216.184.151
      ```

    - Via Local Network (using default SSH port 22):

      ```bash
      ssh-copy-id -i ~/.ssh/id_ed25519_my-server.pub sysadmin@192.168.1.105
      ```

    - **NOTE:** 
      - Path **"~/"** is a short notation for your [user home directory path](#user-home-directory) e.g. "/c/Users/Jon/" 
      - When connecting via local network, you can omit the **-p** parameter because SSH uses port 22 by default. For internet connections, we use a custom port (like 1234) for additional security.

	- You will be prompted for password:
    
          /usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/c/Users/Jon/.ssh/id_rsa_my-server.pub"
          /usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
          /usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
          sysadmin@113.216.184.151's password:

    - After you type in the password you will see output:

          Number of key(s) added: 1

          Now try logging into the machine, with:   "ssh -p '1234' 'sysadmin@113.216.184.151'"
          and check to make sure that only the key(s) you wanted were added.

	- **NOTE:** logging into the Server machine by using command `ssh -p 1234 sysadmin@113.216.184.151` (or `ssh sysadmin@192.168.1.105` for LAN) will still ask us for password. This is because we have not created our RSA keys in the default file "id_rsa" but instead we have created it in the custom file named "id_rsa_my-server". Therefore we need to specify the location of the key file when using ssh to connect, for example: **"ssh -i ~/.ssh/id_rsa_my-server -p 1234 sysadmin@113.216.184.151"**

- **Connect to Server without a password**

    - Via Internet (using custom port for security):
  
      ```bash
      ssh -i ~/.ssh/id_rsa_my-server -p 1234 sysadmin@113.216.184.151
      ```

    - Via Local Network (using default SSH port 22):
  
      ```bash
      ssh -i ~/.ssh/id_ed25519_my-server sysadmin@192.168.1.105
      ```

  - **NOTE:** We are using the path to file "id_rsa_my-server" and NOT "id_rsa_my-server.pub" (private key, not public key).

  - This time, you should be logged into Server without a password prompt

- View public key on Server (for the purpose of understanding the private-public key handshake)
  - Navigate to .ssh folder on the server

        cd ~/.ssh
  - View contents of the folder

        ls -l

    - You should see one file **"authorized_keys"**

  - Open **authorized_keys** file

        nano authorized_keys

  - You will see inside the file **authorized_keys**, the copied public key from the Local machine that looks like **"ssh-rsa VMXfGsZk9MmnTFvkwSZ11O ..."**

  - This is how Server authorizes Local machine connection - by making sure that the public RSA key matches the one stored in **authorized_keys** file.

  - Exit nano editor  : <kbd>CTRL + X</kbd>

As you can see, eventhough we now don't need to provide the password, it is still cumbersome to type in all the connection data every time we want to connect to the Server.

In the next section we will learn how to connect to the Server by just providing the Server name in ssh command like: **"ssh my-server"**.

- Disconnect from the Server : <kbd>CTRL + D</kbd>
- Clean the terminal window : <kbd>CTRL + L</kbd>



### Step 4 - Connect with Server Name


- On the Local machine, go into .ssh folder:
  
  ```bash
  cd ~/.ssh
  ```

- If it doesn't exist , create file **"config"** without any file extension

  ```bash
  touch config
  ```

- Open file config

  ```bash
  nano config
  ```

- Paste into it Host connection configuration like:		

	  Host {{SERVER_NAME}}
		HostName {{SERVER_IP_ADDRESS}}
		Port {{SERVER_PORT}}
		User {{SERVER_USERNAME}}

	**For example:**

    - Via Internet (using custom port for security):

      ```bash
      Host my-server
        HostName 113.216.184.151
        Port 1234
        User sysadmin
        IdentityFile  ~/.ssh/id_rsa_my-server
      ```

    - Via Local Network (using default SSH port 22):

      ```bash
      Host my-server
        HostName 192.168.1.105
        User sysadmin
        IdentityFile  ~/.ssh/id_ed25519_my-server
      ```

- Save and exit nano editor : <kbd>CTRL + S</kbd> <kbd>CTRL + X</kbd>
  
- Connect to Server

  ```bash
  ssh my-server
  ```

  - You should be automatically logged into Server without password prompt



## Remotely Connect with RDP - On Windows

**NOTE:** This section is relevant only to Windows Local machine.

<def>

> RDP (Remote Desktop Protocol) is a proprietary protocol developed by Microsoft which provides a user with a graphical interface to connect to another computer over a network connection.
</def>

If we want to access remotely graphical user interface (GUI) of our server, from Windows personal computer, we can use **Remote Desktop Connection** program that comes pre-installed on Windows.

But first we need to install program called **xrdp** on the Linux home server and then we can connect to it from our Windows computer.
Ref: http://www.xrdp.org/

### Configure XRDP on Server

- Open terminal on your Local machine and SSH into your server machine

  ```bash
  ssh my-server
  ```

- Update and upgrade packages

  ```bash
  sudo apt update
  sudo apt upgrade
  ```

- Install **xrdp**

  ```bash
  sudo apt install xrdp
  ```

- Check status of xrdp service

  ```bash
  sudo service xrdp status
  ```

  - You should see something like

    ```bash
    ● xrdp.service - xrdp daemon
           Loaded: loaded (/lib/systemd/system/xrdp.service; enabled; vendor preset: enabled)
           Active: active (running) since Mon 2023-09-04 22:55:13 BST; 4min 18s ago
             Docs: man:xrdp(8)
                   man:xrdp.ini(5)
    ```
**XRDP** listens on port **3389**, so you will need to open that port on your firewall.

- Allow port 3389 in your firewall

  ```bash
  sudo ufw allow 3389
  ```

- Get your server's IP address and note it down

  ```bash
  hostname -I
  ```

  - NOTE: this will give you internal IP address of your server on your local network. If you want to connect to your server from outside your local network, you will need to get your external IP address. See section [Get External IP](#get-external-ip) for more info.

### Connect from Local machine

- On your Windows computer, open **Remote Desktop Connection** program
  - You can find it by typing **"Remote Desktop Connection"** in the search bar at the bottom left of your screen
  - Or you can find it in the Start Menu under **Windows Accessories** folder

- In the **Remote Desktop Connection** window, type the IP address of your server and click **Connect**

  - You will get a warning message about certificate. Click **Yes** to continue
  - You will get a login screen. Enter your username and password and click **OK**
  - You should be logged in and see the desktop of your Linux server on the screen of your Windows computer

That's it. Now you can use your Linux server's GUI from your Windows computer, from anywhere in the world.



## Upload / Download with SCP

### Upload 

- Copy file from your local computer to server 

		scp -P {{SERVER_SSH_PORT}} {{LOCAL_MACHINE_FILE_PATH}} {{USERNAME}}@{{SERVER_IP_ADDRESS}}:/{{SERVER_FILE_OR_FOLDER_PATH}}

	- Example: from directory of the source files
  
			scp -P 1234 file1.json sysadmin@220.237.80.202:/home/my-files/
			scp -P 1234 file2.json file3.txt sysadmin@220.237.80.202:/home/my-files/

	- Example: from any directory using full path

			scp -P 1234 /C/my-files/file1.json sysadmin@220.237.80.202:/home/my-files/
			scp -P 1234 /C/my-files/file2.json /C/my-files/file3.txt sysadmin@220.237.80.202:/home/my-files/

### Download

- Copy file from Raspbery Pi server to local windows computer

	

## Upload / Download with SFTP

- Refs: 
  - [Linuxize - How to Use SFTP Command to Transfer Files](https://linuxize.com/post/how-to-use-linux-sftp-command-to-transfer-files/)
  - [Digital Ocean - How To Use SFTP to Securely Transfer Files with a Remote Server](https://www.digitalocean.com/community/tutorials/how-to-use-sftp-to-securely-transfer-files-with-a-remote-server)
  - [Hostinger - How to use SFTP](https://www.hostinger.com/tutorials/how-to-use-sftp-to-safely-transfer-files/)

Suppose we are deploying the files for **Dev environment**. Then local and remote folder will be the following:

|                   |                                      |
| ----------------- | ------------------------------------ |
| **Local folder**  | ~/dev/mysite1.com/src/Server/bin/Dev |
| **Remote folder** | /var/www/dev.mysite1.com/server/     |


- Open terminal and navigate to project root folder 

  ```bash
  cd ~/dev/mysite1.com/src/Server
  ```

    OR with your project folder open in VS Code, open terminal : <kbd>CTRL + `</kbd>

- SFTP into your remote server machine 

  ```bash
  sftp mysite1.com
  ```

    OR if ssh connection is not stored in `~/.ssh/config`      sftp sysadmin@123.234.345.45

  - You should see a prompt like this: `sftp>`

- Copy local bin/Dev/ folder into remote host's server/ folder _(NOTE: server folder does not exist. It will be created when the command is run)_

  ```bash
  put -r ./bin/Dev/ /var/www/dev.mysite1.com/server
  ```

  - **-r** flag is used to copy directories recursively.

- Exit out of SFTP:  `exit`

Below is a longer way to deploy your files. It explains how to navigate between local and remote folders and how to copy files from local to remote host.

- SFTP to remote : `sftp mysite1`
- Navigate to **remote working directory** 

  ```bash
  cd /var/www/dev.mysite1.com/
  ```
- Print **remote working directory** : `pwd`

- Navigate to **local working directory** - we have to prefix commands to local working directory with `l` to indicate that we are working on local machine.

  ```bash
  lcd ~/dev/mysite1.com/src/Server
  ```

- Print **local working directory** : `lpwd`

- Switch to our local shell by typing `!` . Now you can use local shell commands without prefixing them with `l`.

- Switch back to SFTP shell by typing `exit`

- Copy files from local to remote 

  ```bash
  put -r ./bin/Dev ./server
  ```
    
- Exit SFTP

  ```bash
  exit
  ```


## Upload / Download with Filezilla

- On Local PC - Install [Filezilla](https://filezilla-project.org/)
- Open Filezilla and connect to your Linux remote server using sftp. Type in connection details in the top connection bar as per bellow. 
  - NOTE: you have to put **sftp://** prefix before your server ip in Host field. 

    |          |                       |
    | -------- | --------------------- |
    | Host     | sftp://123.234.345.45 |
    | Username | sysadmin              |
    | Password | ******                |
    | Port     | 22                    |
    |          |                       |

  - Click **Quickconnect**    

- In the right panel - **Remote site** - navigate to your **Dev environment** website directory e.g. **/var/www/dev.mysite1.com/**

- Right-click on the folder and create a new folder called **server**. This is where we will deploy our files.

- Double-click on the **server** folder to open it

- In the left panel - **Local site** - navigate to  your **Dev environment** publish folder, for example:

    | OS      | Path to publish folder                          |
    | ------- | ----------------------------------------------- |
    | Windows | C:\Users\Jon\dev\mysite1.com\src\Server\bin\Dev |
    | Linux   | /home/jon/dev/mysite1.com/src/Server/bin/Dev    |
    | Mac OS  | /Users/jon/dev/mysite1.com/src/Server/bin/Dev   |


- Select all the files on the left side
- Right-click on selected files > Upload

You should be able to repeat above instructions for publishing also **Test** and **Prod** environments to the server. You just have to change the word dev to test or prod in the path of source and target folders.
**NOTE:** For Prod environment our server path does not contain the word prod. It is just /var/www/mysite1.com/htdocs/ . This is because we want to deploy our Prod environment to the root of our domain.





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


When we run command **ls -l** we can see file permissions displayed at the start of each line representing file or folder inside the folder we are currently in.

It looks something like this:

```bash
drwxr-xr-x 5 admin developers 4096 Apr 10 02:21 repos
```


### Symbolic notation

Ten characters that represent symbolic notation of file or folder permisisons are:
```bash
drwxrwxrwx
```

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


## Networking


| Address Type  | Example                   | Used For                    |
| ------------- | ------------------------- | --------------------------- |
| Internal IPv4 | 192.168.1.105             | Local network communication |
| External IPv4 | 113.216.184.151           | Internet communication      |
| Internal IPv6 | fe80::6941:c5ff:fe0f:3033 | Local network communication |
| External IPv6 | 2a02:4780:14:c78a::1      | Internet communication      |

<def>

> **IP Address** (Internet Protocol Address) - is a unique identifier assigned to a device on a network. It is used to identify the device and route data packets to the correct destination.
</def>

<def>

> **IPv4** (Internet Protocol Version 4) - is a numbering system for IP addresses which has 32-bit address space. It allows for 4.3 billion unique addresses.
</def>


<def>

> **IPv6** (Internet Protocol Version 6) - is a newer numbering system for IP addresses which has 128-bit address space. It allows for 3.4 x 10^38 unique addresses.
</def>

<def>

> **Internal IP Address** - is an IP address assigned to a device on a local area network (LAN). It is used to identify the device on the local network.
</def>

<def>

> **External IP Address** - is an IP address assigned to a device on a public network / WAN (Wide Area Network) / Internet. It is used to identify the device or a network of devices globally - on the internet. On your home network typically it is assigned by your router. The External IP Address on home network routers keeps changing all the time. It is dynamically assigned by your ISP (Internet Service Provider). In order to access your home server from outside world, you need to have a **Static External IP Address** which is generally a plan upgrade to your internet connection that will cost you extra money.
</def>



- Print interface configuration using **ifconfig** command

	```bash
	ifconfig
	```

	- You should see your Ethernet, loopback and wireless network interfaces: eth0, lo, wlan0
	- Under wlan0 you should see your Internal IP Address in the line that starts with "inet" like:

      ```bash
      wlan0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
      		    inet 192.168.1.105  netmask 255.255.255.0  broadcast 192.168.1.255
      ```

  - If `ifconfig` not found, install it using:
  
    ```bash
    sudo apt install net-tools
    ```

<def>

> ifconfig - is the old command to print interface configuration. It is deprecated and will be replaced by ip command in newer versions of Linux. However, it is still widely used.
</def>

- Print interface configuration using ip command

  ```bash
  ip addr show
  ```

<def>

> **Network interface** - is a software or hardware endpoint that allows a device to connect and communicate over a network. Think of it as a "door" through which your computer sends and receives data over a network.
</def>

**Types of Network Interfaces:**
1. **Physical Interfaces** – Hardware-based:
- `eth0`, `eth1`: Ethernet (wired) connections
- `wlan0`, `wlan1`: Wireless (Wi-Fi) connections
- `usb0`: USB-based network connections (e.g., tethering)
2. **Virtual Interfaces** – Software-based:
- `lo`: The loopback interface (used to access your own machine, usually with IP 127.0.0.1)
- `tun0`, `tap0`: Used by VPNs
- `docker0`: Used by Docker for container networking
- VLAN interfaces (e.g., eth0.10)

Each interface typically has:
- An IP address (e.g., `192.168.1.5`)
- A MAC address (hardware address for Ethernet/Wi-Fi)
- A netmask (defines the network size)
- Status (UP/DOWN – whether it's active)
- Packet statistics (packets sent/received)




---
_© 2025 VEXIT® — Tomorrow is today...® — [www.vexit.com](https://www.vexit.com)_