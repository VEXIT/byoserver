# Home Server Ubuntu Setup


|               |                                                   |
| ------------- | ------------------------------------------------- |
| Copyright:    | © 2025 VEXIT , Tomorow is today ® , www.vexit.com |
| Author:       | Vex Tatarevic                                     |
| Date Created: | 2025-09-18                                        |
| Date Updated: | 2025-09-18                                        |
|               |                                                   |


## Definitions


| Term                             | Definition                   |
| -------------------------------- | ---------------------------- |
| Server / Remote (machine) / Host | Ubuntu Linux server computer |
| PC / Local (machine) / Client    | Your personal computer       |



## Example Data Used

We will use the following data for the examples. 

You should replace the sample data with your own data.

|                             |               |
| --------------------------- | ------------- |
| Company name                | my-com        |
| Personal USername on Server | my-user       |
| Admin Username on Server    | admin         |
| Server name                 | my-server     |
| Server IP                   | 192.168.1.105 |
| SSH Port                    | 22            |
| Website Domain Name         | my-site.com   |
| Host Name                   | my-site.com   |

## Install System

### Prerequisites
- Ensure laptop/PC has minimum 25GB storage space
- Have USB flash drive (12GB or above recommended)
- Backup existing data if reinstalling on used device

Ref: [Ubuntu.com - Overview](https://ubuntu.com/tutorials/install-ubuntu-desktop#1-overview)

### Download Ubuntu Image
- Open browser
- Go to https://ubuntu.com/download/desktop
- Download latest Ubuntu Desktop ISO (24.04 LTS recommended)
- Save ISO to memorable location on your PC

Ref: [Ubuntu.com - Download An Ubuntu Image](https://ubuntu.com/tutorials/install-ubuntu-desktop#2-download-an-ubuntu-image)

### Create Bootable USB
- Download balenaEtcher for your current OS from https://www.balena.io/etcher/
- Install and run balenaEtcher
- Select downloaded Ubuntu ISO file
- Select your USB flash drive
- Click Flash! to write the image

Ref: [Ubuntu.com - Create A Bootable Usb Stick](https://ubuntu.com/tutorials/install-ubuntu-desktop#3-create-a-bootable-usb-stick)

### Boot from USB
- Insert USB drive into target laptop/PC
- Restart device
- Enter BIOS boot menu (usually F12, F2, F10, or ESC key)
- Select USB drive from boot menu
- Choose language when prompted
- Select accessibility options if needed
- Choose keyboard layout
- Connect to network (enables driver downloads)
- Click "Try Ubuntu" to test or "Install Ubuntu" to proceed

Ref: [Ubuntu.com - Boot From Usb Flash Drive](https://ubuntu.com/tutorials/install-ubuntu-desktop#4-boot-from-usb-flash-drive)

### Installation Setup
- Choose "Interactive installation"
- Select installation type:
  - "Erase disk and install Ubuntu" for clean install
  - Or choose alongside existing OS if dual-booting
- Check boxes for third-party software and media formats
- Configure disk partitioning as needed

Ref: [Ubuntu.com - Installation Setup](https://ubuntu.com/tutorials/install-ubuntu-desktop#5-installation-setup)

### User Setup
- Enter your name
- Choose computer name (appears on network)
- Create username and strong password
- Choose login options (automatic or password required)

Ref: [Ubuntu.com - Create Your Login Details](https://ubuntu.com/tutorials/install-ubuntu-desktop#7-create-your-login-details)

### Location and Installation
- Select your timezone/location
- Review installation summary
- Click "Install" to begin installation
- Wait for installation to complete
- Click "Restart Now" when prompted
- Remove USB drive when prompted
- Enter encryption password if set

Ref: [Ubuntu.com - Choose Your Location](https://ubuntu.com/tutorials/install-ubuntu-desktop#8-choose-your-location)


## Start Using Terminal

After the installation you should be logged in as **my-user** into the Server machine.

- **Open Terminal** : <kbd>CTRL + ALT + T</kbd>

  - You should see

    ```bash
    my-user@my-server:~$
    ```

- **Close Terminal** - type command `exit` to close the Terminal 

**IMPORTANT:** From this point on we will ONLY use the Terminal to interact with the Server machine. Whenever you see a command in this document, you should type it into the Terminal. We will not explicitly tell you to do so. You should know that seeing a code block with a command means that you should type it into the Terminal.


## Sudo Disable Password Prompt

To run priviledged commands we must type **sudo** before the commands. By default using sudo asks for password every 15 minutes.

- Run the following command to see how annoying it is. Cancel when it asks you for password by pressing <kbd>CTRL + C</kbd>

  ```bash
  sudo apt update
  ```

Disable sudo password prompt so that you don't have to type it ever again when you want to run a sudo command.

- Open **sudoers** file

  ```bash
  sudo visudo
  ```

- Look for the line starting with **%sudo**. It should look like this:

  ```bash
  # Allow members of group sudo to execute any command
  %sudo   ALL=(ALL) ALL
  ```

- Add keyword **NOPASSWD** and a column symbol before the last **ALL**. It should now look like this

  ```bash
  # Allow members of group sudo to execute any command
  %sudo   ALL=(ALL) NOPASSWD:ALL
  ```

- Save and close the file: <kbd>CTRL + S</kbd> <kbd>CTRL + X</kbd>

## Update Upgrade Reboot

- Update system - this will update the list of available packages

  ```bash
  sudo apt update
  ```

- Upgrade system - this will upgrade all the packages to the latest version

  ```bash
  sudo apt upgrade
  ```

- Restart

  ```bash
  sudo reboot
  ```



## SSH - Remotely Connect to Server

### Set Up SSH on Server (Remote) Machine

On **Server** machine:

- **Install SSH server**

  ```bash
  sudo apt install openssh-server
  ```

- **Print internal IP address** of server machine

  ```bash
  hostname -I
  ```
  
  - You should see address like : `192.168.1.105`
  - **Note the IP address for remote access**

###  Connect to Server from PC (Local) Machine

- Log into your local **PC** computer

- Open Terminal
- Connect to Server using SSH with the server IP address you noted down earlier and your user name on the server

  ```bash
  ssh my-user@192.168.1.105
  ```
  - You will see prompt
  
    ```bash
    Are you sure you want to continue connecting (yes/no/[fingerprint])?
    ```
- Type **yes** and hit Enter
  - You will be prompted for password. 
- Type the password and hit Enter
  - You will see the welcome message of the Server machine

You are now connected to the Server machine remotely and can run any commands as if you were sitting in front of it.

- **Disconnect** from Server: <kbd>CTRL + D</kbd>

FROM THIS POINT ON, YOU WILL ONLY CONNECT TO THE SERVER **REMOTELY** USING **SSH** VIA THE TERMINAL ON YOUR PC.

## Enable root User

- SSH into Server as **my-user**

  ```bash
  ssh my-user@192.168.1.105
  ```

- Generate password

  ```bash
  head -c 8 /dev/urandom | base64 | tr -dc 'a-zA-Z0-9'; echo   
  ```

- Copy the password

- Enable "**root**" user

  ```bash
  sudo passwd root
  ```

- Paste the password and hit Enter

  - You should see prompt: `Retype new password:`

- Paste the password and hit Enter again

**NOTE:** If you make a mistake, exit out of interactive mode by typing <kbd>CTRL + D</kbd> and try again.

**IMPORTANT:** Save the password somewhere safe. You will need it in the next section

**NOTE:** We will be using root user to set up the admin user. This is only temporary. We will disable the root user very soon.

- **Enable password-based login via SSH for root** user  - This is disabled by default in SSH config.

  - Open ssh config file `sshd_config` for editing
  
    ```bash
    sudo nano /etc/ssh/sshd_config
    ```
  - Locate line starting with `#PermitRootLogin`
  - Change its value to `yes`
  - Uncomment the line - remove the `#` from the start
  - It should now look like this: `PermitRootLogin yes`
  - Save and close the file: <kbd>CTRL + S</kbd> <kbd>CTRL + X</kbd>
  - Restart ssh service
  
    ```bash
    sudo systemctl restart ssh
    ```
  - Reload daemon
  
    ```bash    
    sudo systemctl daemon-reload
    ```
  - Verify status of ssh service
  
    ```bash
    sudo systemctl status ssh
    ```
    - You should see `Active: active (running)`  

- Disconnect from Server
  
  ```bash
  exit
  ```

## Create Admin Account

- SSH into Server as **root**

  ```bash
  ssh root@192.168.1.105
  ```

- Create admin user

  ```bash
  adduser admin
  ```
