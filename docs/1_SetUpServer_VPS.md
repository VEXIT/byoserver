# Linux VPS Server (Ubuntu or Debian)


|               |                                                   |
| ------------- | ------------------------------------------------- |
| Copyright:    | © 2022 VEXIT , Tomorow is today ® , www.vexit.com |
| Author:       | Vex Tatarevic                                     |
| Date Created: | 2022-10-18                                        |
| Date Updated: | 2025-09-18                                        |
|               |                                                   |


## Overview

This guide provides step-by-step instructions for setting up a VPS server and will work on either Debian or Ubuntu Linux distributions.

## Prerequisites

On Windows, we will need to install **Git Bash** so we can run bash commands.

- Install Git Bash. You can download Git from [git-scm.com](https://www.git-scm.com/).

On Linux and macOS we will use Terminal which is already installed on the system.

From this point on we will refer to both GitBash on Windows and Terminal on Linux and macOS as **terminal**.

- Open account with company that does Linux hosting and purchase Linux instance. Bellow is a few recommendations:

  1. [Hostinger.com](https://hostinger.com) (Recommended) - Lithuanian hosting company - one of the most popular and affordable offshore hosts
  2. [Vultr.com](https://www.vultr.com/) - based in Florida, USA
  3. [Shinjiru.com](https://www.shinjiru.com/) - Malasian offshore VPS hosting

## Definitions

<def>

> **VPS (Virtual Private Server)** - is a server you can rent from a hosting company. It is not an actual physical server machine, but a virtual one. This means that multiple VPS servers coexist on the same physical machine, but they are isolated from each other. 
> 
> Why use VPS?
> 
> This is the best option for those who want to host their own website or server, but do not want to deal with setting up and maintaining a physical infrastructure of the server which can be very expensive and complex.
> 
> Therefore it is the cheapest option to run your own server with scalable resources.
</def>

| Term                             | Definition                                                                                                    |
| -------------------------------- | ------------------------------------------------------------------------------------------------------------- |
| Server, Remote machine, Remote   | Remote Linux server computer                                                                                  |
| Client, Local machine, Local, PC | Your personal Windows computer that you use to connect remotely into Linux hosted on some third party hosting |

## Example Data Used

We will use the following data for the examples. 

You should replace the sample data with your own data.

|                     |                      |
| ------------------- | -------------------- |
| Company name        | my-com               |
| Username            | admin                |
| Server name         | my-server            |
| Server IPv4         | 123.234.345.45       |
| Server IPv6         | fe80:4780:14:c78a::1 |
| SSH Port            | 22                   |
| Website Domain Name | my-site.com          |
| Host Name           | my-site.com          |

## Open Remote Server Account

- Go to VPS provider's website
- Follow the prompts to register either Linux Ubuntu or Linux Debian instance. This guide works on either.
 

## SSH Connect to Server


<def>

> **SSH**, also known as Secure Shell or Secure Socket Shell, is a network protocol that lets you securely connect to a remote computer.
</def>

- To connect to Server via SSH we need the following details of your VPS server:
    |            |                |
    | ---------- | -------------- |
    | IP Address | 123.234.345.45 |
    | Port       | 22             |
    | Username   | root           |
    |            |                |


    - **IP** should be provided in your Server admin panel. Let's suppose Server IP is **123.234.345.45**.
    - **PORT** for SSH is **22** by default. However your VPS service provider may provide SSH on a different port. In that case you should receive it in the welcome email or be able to get the port in your control panel of the service provider website.
    - **USERNAME** on Linux is **root** by default, when system is first created. This is a super user and should only be used once to set up admin user, and then it should be disabled.


- Open terminal on your local machine 
- Connect to server with SSH, using `root` user

  ```bash
  ssh root@123.234.345.45
  ```
  or if the port is different from the default (22)

  ```bash
  ssh -p 123 root@123.234.345.45
  ```
    - You will see prompt

  ```bash
  Are you sure you want to continue connecting (yes/no/[fingerprint])?
  ```
  - Type **yes** and hit Enter
    - You will be prompted for password. 
  - Type the password and hit Enter
    - You will see the welcome message of the Server machine and a root user prompt

  ```bash
      root@my-server:~#
  ```

  **Troubleshooting:** If you get an error like "ssh: connect to host 123.234.345.45 port 22: Bad file number", try rebooting your local machine or retrying the connection after a few minutes.

You are now connected to the Server machine remotely and can run any commands as if you were sitting in front of it.

### Create Admin User

- Create user **admin**. Type the following command:

  ```bash
  adduser admin
  ```
  - If you get an error like:

    ```bash
    root@my-server:~# adduser admin
    fatal: The group `admin' already exists.
    ```

  - Then run this command instead:

    ```bash
    adduser --ingroup admin admin
    ```

- Provide password: **admin** and Full Name: **Admin**. For all other fields hit **Enter** to continue.
- The last question of the user creation wizard will ask you if the information is correct. Type **Y** and hit **Enter**.

- You should see :
	
  ```bash
  Adding user `admin' ...
  Adding new group `admin' (1002) ...
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
  Is the information correct? [Y/n] Y
  ```

- NOTE: When you create user **admin** with **adduser** command, this will also create a group **admin** and a home directory **/home/admin** 

- Give **admin** user elevated priviledges by assigning them to the **sudo** group:

  ```bash
  usermod -aG sudo admin
  ```

  - Keyword **sudo** stands for: **"superuser do"**      
  - You now have an **admin** account with sudo priviledges. This means that admin account can execute commands which require elevated root priviledges by prefixing those commands with keyword **sudo**.

- Logout from server

  ```bash
  logout
  ```

  - This will disconnect your SSH session from the remote server

- SSH into server again, but this time as **admin** user

  ```bash
  ssh -p 22 admin@123.234.345.45
  ```

### Disable root Account

- To disable **root** account run this command

  ```bash
  sudo passwd -l root
  ```

  - This will prompt you for password

    `[sudo] password for admin:`

  - Type in your password and hit Enter to finish disabling the root user. You will get:

    `passwd: password expiry information changed.`

  - We will later configure sudo not to prompt us for password every time, in the section [Remove sudo password prompt](#remove-sudo-password-prompt)

- Now, Disconnect/close remote connection to Server : **CTRL + D**




### Set up Passwordless Entry - Default way

**I RECOMMEND:** that you set up SSH connections in the way that allows for multiple servers. To do that, **go to** => [Set up Passwordless Entry - for Multiple Servers](#set-up-passwordless-entry---for-multiple-servers)

Below is a simple default configuration for one server. I provide this only for your reference, but do not recommend using it.

<hr>

- Suppose we are using username **MyUser**

- Open terminal (Git Bash on Windows) on your local machine

- Create public and private keys using **ssh-keygen**, command:
	
  ```bash
  ssh-keygen 
  ```

  - You should see:

    ```bash
    Generating public/private ed25519 key pair.
    Enter file in which to save the key (/c/Users/MyUser/.ssh/id_ed25519):
    ```
    - Above is a default path to ssh private key file
  
     **IMPORTANT:** Older versions of ssh-keygen may use RSA algorithm instead of ED25519, which is less secure. If you see **rsa** instead of **ed25519** in the prompt, quit the command <kbd>CTRL + C</kbd> and use the following command instead: `ssh-keygen -t ed25519`

    - Hit **Enter** 3 times, without typing-in any text. 
	
    - You should see these prompts coming up after each time you hit Enter

      ```bash
      Enter passphrase (empty for no passphrase):
      Enter same passphrase again:
      ```


- Copy the public key to Server using **ssh-copy-id**, command:

  ```bash
  ssh-copy-id -i ~/.ssh/id_ed25519.pub -p 22 admin@123.234.345.45
  ```
  - **Troubleshooting:** If you get an error like `/usr/bin/ssh-copy-id: ERROR: failed to open ID file '/home/admin/.ssh/id_ed25519.pub': No such file`, make sure you're running the command from your local machine (not the server) and that the key file exists in your local ~/.ssh/ directory.

- Enter password for admin. You should see:

  ```bash
  Number of key(s) added: 1

  Now try logging into the machine, with: "ssh -i /c/Users/MyUser/.ssh/id_ed25519 -p 22 'admin@123.234.345.45'"
  and check to make sure that only the key(s) you wanted were added.
  ```
- Run the command as prompted above:

  ```bash
  ssh -i /c/Users/MyUser/.ssh/id_ed25519 -p 22 admin@123.234.345.45
  ```
  - This should log you into server without asking for password

- Verify that the key has been copied to the server by checking the authorized_keys file:

    ```bash
    nano ~/.ssh/authorized_keys
    ```
    - You should see a line starting with **ssh-ed25519**. It will contain : algorhythm name , public key, local computer name. For example:

      ```bash
      ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIKyhVWnQ5XJFQjT7RwJU9+Z7VHr9g4QIe2Z030b683U MyUser@my-pc
      ```
    - Close the file **CTRL + X**
    - **NOTE:** .ssh is a hidden folder. If you go inside /home/admin and list all the folders using command **ls -l**, you will not see folder ".ssh" as all folders prefixed with a dot (.) are hidden in Linux. To view the hidden folders you need to use argument "**a**" after **ls** command. So in our example we can do this:

      ```bash
      cd /home/admin # or cd ~/
      ls -la         # this will show all hidden folders as well like .ssh
      ```


- Disconnect from the server <kbd>CTRL</kbd> <kbd>D</kbd>

- On **Client Computer** (your local PC) go into your users's .ssh folder:

  ```bash
  cd ~/.ssh
  ```

- Create **config** file without any file extension

  ```bash
  touch ~/.ssh/config
  ```

- Open config file

- Type into it Host connection configuration like:		

  ```bash
  Host my-site.com            # SERVER_ALIAS - unique identifier for your server like domain name
  	HostName 123.234.345.45   # SERVER_IP_ADDRESS
  	Port 22                   # SERVER_SSH_PORT
  	User admin                # SERVER_LOGIN_USERNAME
  ```

- Save and close the **config** file

- Now you can log into remote Server by typing just:

  ```bash
  ssh my-site.com
  ```




### Set up Passwordless Entry - for Multiple Servers

I will explain how to do this on your local computer whether it is Windows, Linux or Mac.

The only difference is location of user's home directory on each operating system:

|         |                         |
| ------- | ----------------------- |
| Windows | C:\\Users\\{{USERNAME}} |
| Linux   | /home/{{USERNAME}}      |
| Mac     | /Users/{{USERNAME}}     |

When writing the Bash commands, we will use home directory alias `~` (tilde symbol) to refer to the home directory of the user. That way the commands will be the same for all operating systems.

- For the purpose of this exercise, suppose we have the following situation:
  - Our local Windows username is **MyUser**
  - We are planing to use a domain name **my-site.com** for our server. Therefore we will use **my-site.com** as **SERVER_ALIAS** in our ssh config file.


- Open **Terminal** - on **local** machine (On Windows we refer to GitBash command line shell as Terminal)


#### Step 1. Create Keys

- **Create keys** - Create public and private keys using **ssh-keygen**, command:
	
  ```bash
  ssh-keygen -t ed25519
  ```
  - **-t** - flag specifies the type of encryption algorithm
  - We selected **"ed25519"** instead of default **"rsa"** because it is more secure

  You should see:

  ```bash
  Generating public/private ed25519 key pair.
  Enter file in which to save the key (DEFAULT_FILE_PATH):
  ```
  
    | Where      | DEFAULT_FILE_PATH                 |
    | ---------- | --------------------------------- |
    | On Windows | `/c/Users/MyUser/.ssh/id_ed25519` |
    | On Mac     | `/Users/MyUser/.ssh/id_ed25519`   |
    | On Linux   | `/home/MyUser/.ssh/id_ed25519`    |


  - **COPY** the DEFAULT_FILE_PATH and **PASTE** it after the colon into the command prompt. Then **APPEND** your server domain name to it e.g. **"my-site.com"**. 
 
    It should now look like this :

    ```bash
    Enter file in which to save the key (/c/Users/MyUser/.ssh/id_ed25519): /c/Users/MyUser/.ssh/id_ed25519_my-site.com
    ```
  
  - Then hit <kbd>Enter</kbd>
	
  - You should see password prompts after that

    ```bash
    Enter passphrase (empty for no passphrase):
    Enter same passphrase again:
    ```

  - Hit <kbd>Enter</kbd> 2 times, without providing any password 


  - The keys will be generated and you will see the following output (where **"my-pc"** is the name of your local computer, **"MyUser"** is your username on your local computer,**"my-site.com"** is your Server computer alias):

    ```bash
    Your identification has been saved in /c/Users/MyUser/.ssh/id_ed25519_my-site.com
    Your public key has been saved in /c/Users/MyUser/.ssh/id_ed25519_my-site.com.pub
    The key fingerprint is:
    SHA256:h7FUEfiH8PtvNANPjc7uZM1VH6pGG/5LmI/PNp83M6E MyUser@my-pc
    The key's randomart image is:
    +---[ED25519 256]----+
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
    ```
#### Step 2. Copy Public Key to Server

- **Copy pub key** - Copy the public key to Server using **ssh-copy-id**, command:

  **IMPORTANT**: The file path **MUST have .pub** extension

  For example - if using custom port 1234:

  ```bash
  ssh-copy-id -i ~/.ssh/id_ed25519_my-site.com.pub -p 1234 admin@123.234.345.45
  ```
  - **-i** - specifies the path to the private key file
  - **-p** - specifies the port to use for the connection

  Example if you are using default port 22, you can ommit the port

  ```bash
  ssh-copy-id -i ~/.ssh/id_ed25519_my-site.com.pub admin@123.234.345.45
  ```
  - You will be prompted for password

    ```bash
    admin@123.234.345.454's password:
    ```
  - Enter your admin user's password and hit Enter. You will be shown this message and disconnected from server:
  
    ```bash
    Number of key(s) added: 1
    
    Now try logging into the machine, with:   "ssh -p '22' 'admin@123.234.345.45'"
    and check to make sure that only the key(s) you wanted were added.
    ```

  - **NOTE:** If you run command **"ssh -p 22 admin@123.234.345.45"** - it will still ask us for password, because we have not created our keys in the default file "id_ed25519". <br/>
  Instead we have created keys in the custom file named "id_ed25519_my-site.com" . <br/>
  Therefore **we need to specify the location of the key file** when using ssh to connect, for example: <br/>
  `ssh -i ~/.ssh/id_ed25519_my-site.com -p 22 admin@123.234.345.45`

- Connect to VPS using the key file: (**MUST NOT have .pub** extension in the file)

  ```bash
  ssh -i ~/.ssh/id_ed25519_my-site.com -p 22 admin@123.234.345.45
  ```
  - This time you should not be prompted for password
  - **NOTE:** The key has been stored on the remote server inside "**~/.ssh/authorized_keys**" file. To check that it matches a public key on your local computer inside "~/.ssh/id_ed25519_my-site.com.pub", open the authorized_keys file on remote computer:

    ```bash
    nano ~/.ssh/authorized_keys
    ```

  - This will show you contents of the file starting with **ssh-rsa**
  - Close the file <kbd>CTRL + X</kbd>
- **Disconnect** from the server <kbd>CTRL + D</kbd>

#### Step 3. Create Config File

- **Create config** - On your local computer create ssh config file without any file extension

  ```bash
  nano ~/.ssh/config
  ```
  - This will open the file in nano editor.

- **Add server connection** - type the following inside the **config** file:		
 
  ```bash
  Host my-site.com                # SERVER_ALIAS - unique identifier for your server like domain name
  	HostName 123.234.345.45       # SERVER_IP_ADDRESS
  	Port 22                       # SERVER_SSH_PORT
  	User admin                    # SERVER_LOGIN_USERNAME
  	IdentityFile  ~/.ssh/id_ed25519_my-site.com # path to the private key file
  ```

- Save and close the **.ssh/config** file : <kbd>CTRL + S</kbd>, <kbd>CTRL + X</kbd>

#### Step 4. Connect to Server

- **Conect to host** - Now you can simply type this command to connect to remote host (server) without having to provide the password and other details:

  ```bash
  ssh my-site.com
  ```



## SFTP Connect to Server

<def>

> **SFTP** stands for **Secure File Transfer Protocol**. It is a secure way to transfer files between local and remote computers.
</def>


## Configure Server

### Colorize Command Prompt

By default your command prompt on remote server may be not colorized.

To turn on command prompt colorization follow these steps:

- Go inside user home directory

  ```bash
  cd ~/
  ```

- Open .bashrc file

  ```bash
  nano .bashrc
  ```

- Search for words **"colored prompt"** - Hit <kbd>CTRL</kbd> <kbd>W</kbd>, type "colored prompt", hit Enter
- Look for the following section:

      # uncomment for a colored prompt, if the terminal has the capability; turned
      # off by default to not distract the user: the focus in a terminal window
      # should be on the output of commands, not on the prompt
      # force_color_prompt=yes

- Uncomment line: **"# force_color_prompt=yes"** by removing the # symbol

    ```bash
    force_color_prompt=yes
    ```

- Exit file and save changes: <kbd>CTRL</kbd> <kbd>S</kbd>, <kbd>CTRL</kbd> <kbd>X</kbd>

- Refresh .bashrc to see the prompt change: 

  ```bash
  source .bashrc
  ```
  - If the prompt has not changed, then disconnect and connect again to server: 

    <kbd>CTRL</kbd> <kbd>D</kbd>

    ```bash      
    ssh my-site.com
    ```

- Now you should see your prompt colorized. For example

    ```bash
    admin@my-host-name:~$
    ```

  - **admin@my-host-name** should now be green
  - **:** should be white
  - **~** - which is directory path - should be blue
  - **$** - should be white

  - NOTE: **my-host-name** is a random host name assigned by the service provider. We will change this later to be the same as our site's domain name e.g. my-site.com

### Customize Command Prompt

You can further customize the prompt if you like.

We will demonstrate how to change the prompt to look similar to popular Git Bash terminal on Windows: **more colourized and cursor on new line**.

- CD into user home: 

  ```bash
  cd ~
  ```
- Open bash profile : 

  ```bash
  codium .bashrc
  ```
  or 

  ```bash
  nano .bashrc
  ```
- Search for Terminal prompt configuration block
  - If using nano - hit `CTRL + W` to open the search prompt
  - If using VS Code or VS Codium - hit `CTRL + F`
  - Paste this line `if [ "$color_prompt"` and hit `ENTER` to go to the start of the block
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

- Save and Close .bashrc (in nano : `CTRL + S` , `CTRL + X`)
- Reload .bashrc in Terminal 

  ```bash
  source .bashrc
  ```
  - The terminal prompt should change. If not, then restart the Terminal

**NOTE:** To change colours to something else you can go to bash prompt generator website to generate the code for .bashrc.
- Go to: https://robotmoon.com/bash-prompt-generator/ to play with different prompt colours and generate bash code for prompt customization inside .bashrc 


### Customize List Command

We want to customize `ls` command to achieve the following:

1. Sort files and folders by name - By default, when you list files and folders in a directory, they are sorted by date.
2. Use aliases for `ls` command with options to avoid typing the command options every time. For example instead of  typing `ls -l` to show details of files and folders, we can create an alias `ll` that will call `ls -l` command.

- Open **.bashrc** file

      nano ~/.bashrc

- Look for the following section or any lines starting with `#alias` or `alias`:

  ```bash
  # some more ls aliases
  #alias ll='ls -l'
  #alias la='ls -A'
  #alias l='ls -CF'
  ```
  - Those are the command aliases

<def>

> **Command aliases** are shortcuts for commands. For example, instead of typing `ls -l` to show details of files and folders, we can create an alias `ll` that will call `ls -l` command by putting the line `alias ll='ls -l'` in the .bashrc file.
</def>

In order to keep .bashrc file clean, we will put the aliases inside .bash_aliases file which is included in .bashrc file.

- Create or edit the **.bash_aliases** file inside user home directory

  ```bash
  nano ~/.bash_aliases
  ```

- Move all the aliases from .bashrc file to the .bash_aliases file:

  ```bash
  # Aliases
  alias ll='LC_COLLATE=en_US ls -l'
  alias la='ls -A'
  alias l='ls -CF'
  ```
  - NOTE: we prefix the value of ll alias with `LC_COLLATE=en_US` to sort files displayed by ll command, by name.
  - You should add any future aliases you create in this file

- Exit file and save changes: **CTRL + S**,  **CTRL + X**

- Activate changes by running the following command

  ```bash
  source ~/.bashrc ~/.bash_aliases
  ```

<def>

> **.bashrc** - is the main bash configuration file. It is read by the bash shell when it starts. So current user session will use the settings from this file. .bashrc is located in the user home directory.
</def>

### Remove Sudo password prompt

When you want to run priviledged commands you type **sudo** before the command.

By default using sudo asks for password every 15 minutes.

We want to get rid of that annoying password prompt.

There are two methods to do this:
- Method A - Edit main sudoers file (works, not recommended)
- Method B - Recommended: sudoers.d drop‑in

#### Method A — Edit main sudoers (works, not recommended)

- Open **sudoers** file for editing using **visudo** (safe editor for sudoers):

    ```bash
    sudo visudo
    ```

- Set **NOPASSWD** for all users in sudo group
  - Look for the line starting with **%sudo**. It should look like this:

    ```bash
    # Allow members of group sudo to execute any command
    %sudo   ALL=(ALL) ALL
    ```

  - Add keyword **NOPASSWD** and a colon before the last **ALL**. It should now look like this:

    ```bash
    # Allow members of group sudo to execute any command
    %sudo   ALL=(ALL) NOPASSWD:ALL
    ```

- Save and close the file: <kbd>CTRL + S</kbd>, <kbd>CTRL + X</kbd>

**NOTE:** This method works but is not the best practice because it modifies the main `/etc/sudoers` file. Prefer the drop-in method below.

#### Method B — Recommended: sudoers.d drop‑in

Using a dedicated drop-in avoids editing the main file and is the standard best practice.

Drop-in is a file that is used to override the main sudoers file.

- Open a drop-in with visudo for validation:

    ```bash
    sudo visudo -f /etc/sudoers.d/010-nopasswd-sudo
    ```

- Add this single line to the file:

    ```
    %sudo ALL=(ALL) NOPASSWD:ALL
    ```

- Save and close the file: <kbd>CTRL + S</kbd>, <kbd>CTRL + X</kbd>

- Ensure correct permissions and ownership:

    ```bash
    sudo chown root:root /etc/sudoers.d/010-nopasswd-sudo
    sudo chmod 440 /etc/sudoers.d/010-nopasswd-sudo
    ```
    - chown root:root - means that the file is owned by the root user and group.
    - chmod 440 - means that the file is only readable and writable by the root user.

#### Reconnect and test

- Disconnect and reconnect to Server: 

  <kbd>CTRL</kbd> <kbd>D</kbd>

  ```bash      
  ssh my-site.com
  ```

- Test if you can run sudo commands without being prompted for password. Create new directory inside /home/ and then delete it

  ```bash
  cd /home
  sudo mkdir new-dir
  sudo rmdir new-dir
  ```

  - You should not be prompted for password this time


### Change Hostname

Initially the service provider assigns a random name to our VPS host.

Generally we want to change this to a custom name which should normally be the same as the main website we are hosting on this server.

For the purpose of this exercise we will name the website and the host: **my-site.com**

- Check current host name

  ```bash
  hostname
  ```

  - You should see current host name - let's call it for this example **my-host-name**


- Change to new host name: **my-site.com**

  ```bash
  sudo hostnamectl set-hostname my-site.com
  ```

- Update host name inside **/etc/hosts** 

  ```bash
  sudo nano /etc/hosts
  ```

  - Change **my-host-name** to **my-site.com**

- Reboot the server

  ```bash
  sudo reboot
  ```

- Reconnect after waiting couple of minutes

  ```bash
  ssh my-site.com
  ```

  - The command line prompt should now look like:

  ```bash
  admin@my-site
  ```


- Confirm that the host name has been changed - run the following commands one by one:  

  ```bash
  hostnamectl
  hostname
  ```

## Install Standard Tools

<def>

> **APT (Advanced Package Tool)** - is a package management system on Linux used for easy installation of packages for programs used to execute specific functionality on Linux system.
</def>

- Run command **ifconfig** . If it says its missing install net tools

  ```bash
  sudo apt install net-tools
  ```




## Set up Workspace

We will create a working directory inside /home where we will store almost everything we are working with. We will name this directory with a name of our company. For the purpose of this tutorial we will refer to it as **my-com**

- Create directory with your company name

    ```bash
    sudo mkdir /home/my-com
    ```

- Inside **/home/my-com** create the following directories

  | Name      | Description                    |
  | --------- | ------------------------------ |
  | downloads | Installation files and scripts |
  | repos     | Git repository directories     |


- Give ownership and exclusive access to admin user and group for the whole /home/my-com directory. 

  ```bash
  sudo chown admin:admin -R /home/my-com
  sudo chmod -R 770 /home/my-com
  ```

- **NOTE:** From here on you should only create directories and files inside your workspace without using sudo keyword. This is because using sudo will give execute permissions on created files and folders to root user only and so you will have to use sudo keyword before editing or running any files inside your workspace



## Firewall - UFW

<def>

> **UFW** - **Uncomplicated Firewall** is a command-line interface for working with the firewall on Linux.
</def>

### Install and Enable UFW

**NOTE:** This sequence is safe for remote access (e.g., via SSH) because we add critical rules before enabling the firewall.

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

  - You need to reboot the system to fix this error

- Reboot:

  ```bash
  sudo reboot
  ```

- Check status again:

  ```bash
  sudo ufw status
  ```

  - This time you should get: `Status: inactive`

### Add Firewall Rules (Before Enabling)

We add rules while UFW is inactive to ensure your SSH connection isn't disrupted when we enable it later.

- Add port 22 - the standard port for SSH (Secure Shell) connections, which is how we connect to our server remotely.

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

  - You will get

    ```bash
    Command may disrupt existing ssh connections. Proceed with operation (y|n)?
    ```

  - Type **"y"** and hit Enter
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

### Remove Rule

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
	

## Intrusion Prevention - fail2ban

Fail2ban protects SSH and other exposed services by blocking IPs with repeated failed login attempts.

- Install fail2ban

  ```bash
  sudo apt update
  sudo apt install fail2ban
  ```

- Enable and start the service

  ```bash
  sudo systemctl enable fail2ban
  sudo systemctl start fail2ban
  ```

- Check status

  ```bash
  sudo systemctl status fail2ban
  ```

- Create a basic jail configuration

  ```bash
  sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
  ```

- Edit the SSH jail (example settings)

  ```bash
  sudo nano /etc/fail2ban/jail.local
  ```

  - Set:
    - `enabled = true`
    - `port = ssh`
    - `maxretry = 5`
    - `bantime = 1h`
    - `findtime = 10m`

- Restart fail2ban to apply

  ```bash
  sudo systemctl restart fail2ban
  ```

> NOTE: Keep UFW enabled. Fail2ban complements the firewall, it does not replace it.

## Time Synchronization (NTP)

Accurate time is crucial for logs, SSL certificates, cron jobs, and system security. We'll use chrony for NTP synchronization.

- Install chrony

  ```bash
  sudo apt update
  sudo apt install chrony
  ```

- Enable and start chrony

  ```bash
  sudo systemctl enable chrony
  sudo systemctl start chrony
  ```

- Check status

  ```bash
  sudo systemctl status chrony
  ```

- Verify time synchronization

  ```bash
  chronyc tracking
  ```

  - Look for "Leap status: Normal" and small offset values (<0.1 seconds)

- Check sources

  ```bash
  chronyc sources
  ```

  - Should show NTP servers with "^*" indicating good sync

> NOTE: Debian/Ubuntu often has systemd-timesyncd by default, but chrony is more robust for servers.

## Remote Desktop from Windows

Ref: http://www.xrdp.org/


We can connect to our server's Desktop (graphic user interface) from our Windows laptop/desktop easily by using program called **Remote Desktop Connection** that comes pre-installed on Windows.

But first we need to install program called **xrdp** on the Linux home server and then we can connect to it from our Windows computer.

- Update and upgrade packages

  ```bash
  sudo apt update
  sudo apt upgrade
  ```


- Install **xrdp** on Raspberry Pi

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

  - **NOTE:** You'll see 3 addresses. Use the **second address** (the IPv4 address).
  - For example, if you see `127.0.0.1 123.234.345.45 fe80:4780:14:c78a::1` then your IP address is `123.234.345.45`



- On your Windows computer, open **Remote Desktop Connection** program
  - You can find it by typing **"Remote Desktop Connection"** in the search bar at the bottom left of your screen
  - Or you can find it in the Start Menu under **Windows Accessories** folder

- In the **Remote Desktop Connection** window, type the IP address of your server and click **Connect**

  - You will get a warning message about certificate. Click **Yes** to continue
  - You will get a login screen. Enter your username and password and click **OK**

  **Troubleshooting:** If you can see the remote computer login window but fail to connect, try the following:
  - Ensure port 3389 is open in your firewall: 

    ```bash
    sudo ufw allow 3389
    ```
  - Check that xrdp service is running: 

    ```bash
    sudo service xrdp status
    ```
  - Restart xrdp service if needed: 

    ```bash
    sudo service xrdp restart
    ```
  - Try connecting from a different network or computer to isolate the issue

- After your login screen you will see the **Connection Log** window that says:

  ```bash
  Connecting to sesman IP 127.0.0.1 port 3350
  sesman connect ok
  sending login info to session manager, please wait...
  login successful for display 10
  ```


-  Click **OK** to continue



# Appendix

## Directory Size - Disk Usage

### DU

- To print usage for all sub directories

  ```bash
  du -hc --max-depth=0 downloads
  ```

  - **-h** - makes it more readable
  - **-c** - prints a total size
  - **--max-depth=0** - limit the scan to a certain level of subdirectory. 0 will scan size of only the top directory.
  - last argument is the name of the directory you wish the query or an absolute path to directory. If you don't provide this, the command will print out size for all the directories within current directory which is normally not a good idea.

- To read usage manual for this command

  ```bash
  du --help
  ```


## Processes

### TOP

- View a list of all current processes

  ```bash
  top
  ```

- Kill processes directly from the top interface,  press **k** and enter the process ID

- Exit - press **q**

### PS

- List processes

  ```bash
  ps -aux
  ```

    |     |                                                                   |
    | --- | ----------------------------------------------------------------- |
    | -a  | View processes of all users rather than just the current user     |
    | -u  | Provide detailed information about each of the processes          |
    | -x  | Include processes that are controlled not by users but by daemons |

### PGREP

- List processes using pattern like regular expression

  ```bash
  pgrep {{options}} {{pattern}}
  ```

    |     |                                                          |
    | --- | -------------------------------------------------------- |
    | -l  | List both the process names and the PIDs                 |
    | -n  | Return the process that is newest                        |
    | -o  | Return the process that is oldest                        |
    | -u  | Only find processes that belong to a specific user       |
    | -x  | Only find processes that exactly match the given pattern |

- Examples
  - Processes owned by root

    ```bash
    pgrep -u root
    ```

  - Processes owned by root that start with the letter **a**

    ```bash
    pgrep -u root 'a*'
    ```

### PIDOF

- List the ID of a processes - search by name

  ```bash
  pidof {{options}} {{program}}
  ```

    |     |                                                                  |
    | --- | ---------------------------------------------------------------- |
    | -c  | Only return PIDs within a single root directory                  |
    | -o  | Omit certain PIDs (include the processes to omit after the flag) |
    | -s  | Only return a single PID                                         |
    | -x  | Also returns PIDs of shells that are running scripts             |




### KILLALL

- Kill processes by name

  ```bash
  killall {{options}} {{pattern}}
  ```

    |     |                                                                         |
    | --- | ----------------------------------------------------------------------- |
    | -e  | Find an exact match for the process name                                |
    | -I  | Ignore case when trying to find the process name                        |
    | -i  | Ask for additional confirmation when killing the process                |
    | -u  | Only kill processes owned by a specific user                            |
    | -v  | Report back on whether the process has been successfully killed         |
    | -o  | Kill all processes that have been running more than that amount of time |
    | -y  | Kill all processes that have been running less than that amount of time |


- Examples
  - Kill processes running more than 15 minutes

    ```bash
    killall -o 15m
    ```

  - Kill processes running less than 15 minutes

    ```bash
    killall -y 15m
    ```


### PKILL

- Kill processes using pattern like regular expression

  ```bash
  pkill {{options}} {{pattern}}
  ```


    |         |                                                             |
    | ------- | ----------------------------------------------------------- |
    | -n      | Only kill the newest of the processes that are discovered   |
    | -o      | Only kill the oldest of the processes that are discovered   |
    | -u      | Only kill the processes that are owned by the selected user |
    | -x      | Only kill the processes that match the pattern exactly      |
    | -signal | Send a specific signal to the process, rather than SIGTERM  |


### KILL

- Kill process by ID

  ```bash
  kill {{processID}}
  ```

- Kill unresponsive process

  ```bash
  kill -9 {{processID}}
  ```

    OR
      
  ```bash
  kill -SIGKILL {{processID}}
  ```

  - 9 - is a code for SIGKILL signal which is a forceful way to kill a process AKA **"hard-kill"**



## Network

### IFCONFIG

- Show network interfaces

  ```bash
  ifconfig
  ```

- Show host IP address

  ```bash
  ifconfig | grep inet
  ```

  - Your IP will be the first number in the result

### NETSTAT

- Display listening ports

  ```bash
  netstat -nlt
  ```
      
  ```bash
  netstat -nltp
  ```

- Display all active ports

  ```bash
  netstat -ant
  ```


Parameters meaning:

|     |                                                                                                   |
| --- | ------------------------------------------------------------------------------------------------- |
| -lt | List all listening TCP ports                                                                      |
| -n  | Show numerical addresses                                                                          |
| -p  | Display the PID/Program name related to a specific connection                                     |
| -a  | Displays all active TCP connections and the TCP and UDP ports on which the computer is listening. |


### SS

<def>

> **ss (Socket Statistics)** - is a CLI command used to show network statistics. The ss command is a simpler and faster version of the now obsolete netstat command. Together with the ip command, ss is essential for gathering network information and troubleshooting network issues.
</def>


- Basic usage

  ```bash
  ss
  ```
      
The columns show the following details:

|                    |                                                                                                   |
| ------------------ | ------------------------------------------------------------------------------------------------- |
| Netid              | Type of socket. Common types are TCP, UDP, u_str (Unix stream), and   u_seq (Unix sequence).      |
| State              | State of the socket. Most commonly ESTAB (established), UNCONN (unconnected), LISTEN (listening). |
| Recv-Q             | Number of received packets in the queue.                                                          |
| Send-Q             | Number of sent packets in the queue.                                                              |
| Local address:port | Address of local machine and port.                                                                |
| Peer address:port  | Address of remote machine and port.                                                               |

## Troubleshooting

### Errors were encountered while processing

If you get this error while trying to install packages **Errors were encountered while processing:** , try the following sequence to fix it.

- Ensure that the shared libraries are properly registered (also fixes some GCC/G++ errors)

  ```bash
  sudo ldconfig
  ```

- Try to remove the package (replace ‘your-app’ with the name of the dodgy application)

  ```bash
  sudo dpkg -r YOUR-APP
  ```

- Check your package cache

  ```bash
  sudo apt-get check
  ```

- Update your package list

  ```bash
  sudo apt-get update
  ```

- Ensure package downloads were properly completed when the system last updated

  ```bash
  sudo apt-get upgrade --fix-missing
  ```

- Try to upgrade the system (sometimes an updated package version fixes the issue)

  ```bash
  sudo apt-get upgrade
  sudo apt-get dist-upgrade
  ```

- Try to reconfigure all applications that failed to install

  ```bash
  sudo dpkg --configure -a
  ```

- Try to reconfigure specific applications that failed to install

  ```bash
  sudo dpkg --configure YOUR-APP
  ```

- Try to fix broken packages

  ```bash
  sudo apt-get install -f
  ```

- Try to fix a specific broken package

  ```bash
  sudo apt-get install YOUR-APP -f
  ```

- Try to install the dependencies that an application requires for it to install successfully

  ```bash
  sudo apt-get build-dep YOUR-APP
  ```

- Re-try to install the failed apps

  ```bash
  sudo apt-get install
  ```




---
_© 2025 VEXIT® — Tomorrow is today...® — [www.vexit.com](https://www.vexit.com)_
