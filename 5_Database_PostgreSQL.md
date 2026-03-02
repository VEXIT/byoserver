# Linux Database - PostgreSQL


|               |                                                   |
| ------------- | ------------------------------------------------- |
| Copyright:    | © 2022 VEXIT , Tomorow is today ® , www.vexit.com |
| Author:       | Vex Tatarevic                                     |
| Date Created: | 2022-11-07                                        |
| Date Updated: | 2024-12-13                                        |



- Ref: 
  - [PostgreSQL Home](https://www.postgresql.org) > [Linux Ubuntu Download](https://www.postgresql.org/download/linux/ubuntu/)
  - [Ubuntu - PostgreSQL Setup](https://ubuntu.com/server/docs/databases-postgresql)
  - [PostgreSQL - Server Administration](https://www.postgresql.org/docs/current/admin.html)
  - [PostgreSQL 17 Documentation](https://www.postgresql.org/docs/17/index.html)

## Uninstall PostgreSQL

If you already have installed PostgreSQL, make sure to uninstall it before installing the latest version. 

- Uninstall PostgreSQL

      sudo apt --purge remove postgresql
      sudo apt purge postgresql*
      sudo apt --purge remove postgresql postgresql-doc postgresql-common

- Check if there are any remaining packages

      dpkg -l | grep postgres

  - If found, remove them

        sudo apt-get --purge remove {{POSTGRESS_PACKAGE_NAME}}

- Remove all of the PostgreSQL data and directories

      sudo rm -rf /var/lib/postgresql/
      sudo rm -rf /var/log/postgresql/
      sudo rm -rf /etc/postgresql/

## Install PostgreSQL

 On Linux Ubuntu OS you should be able to install the PostgreSQL just by running `sudo apt install postgresql` . See [Ubuntu.com - PostgreSQL Setup](https://ubuntu.com/server/docs/databases-postgresql).

However this may cause installation of the older version of PostgreSQL.

**To install the latest version of PostgreSQL**, follow the walkthrough from the official installation page for Ubuntu Linux

- Go to: https://www.postgresql.org/download/linux/ubuntu/

- Scroll down to the sentence "**To manually configure the Apt repository, follow these steps:**" . You should see the following code snippet. Make sure the below code is up to date by checking the official website.

    ```bash
    # Import the repository signing key:
    sudo apt install curl ca-certificates
    sudo install -d /usr/share/postgresql-common/pgdg
    sudo curl -o /usr/share/postgresql-common/pgdg/apt.postgresql.org.asc --fail https://www.postgresql.org/media/keys/ACCC4CF8.asc

    # Create the repository configuration file:
    sudo sh -c 'echo "deb [signed-by=/usr/share/postgresql-common/pgdg/apt.postgresql.org.asc] https://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'

    # Update the package lists:
    sudo apt update

    # Install the latest version of PostgreSQL:
    # If you want a specific version, use 'postgresql-16' or similar instead of 'postgresql'
    sudo apt -y install postgresql
    ```

- Copy and paste the above code into your terminal and run it. This will install the latest version of PostgreSQL on your Linux server.


- Verify the PostgreSQL service is running

      sudo systemctl status postgresql 

  - You should see the green circle and the keyword **active** in the output like this:

    ```
    ● postgresql.service - PostgreSQL RDBMS
         Loaded: loaded (/lib/systemd/system/postgresql.service; enabled; vendor preset: enabled)
         Active: active (exited) since Thu 2024-12-12 23:07:00 UTC; 8min ago
    ```

- Check PostgreSQL version with psql
    
      psql --version
    
    - You should see the version of PostgreSQL you have installed like:
    
          psql (PostgreSQL) 17.2 (Ubuntu 17.2-1.pgdg22.04+1)

- Check PostgreSQL version with pg_config

      pg_config --version

  - You should see the version of PostgreSQL you have installed like:

        psql (PostgreSQL) 17.2 (Ubuntu 17.2-1.pgdg22.04+1)


## Enter / Exit Postgres

NOTE: In PostgreSQL, a USER account is referred to as a ROLE. Word user and role are used interchangably.

By default, PostgreSQL uses ident authentication.

It means that PostgreSQL will associate its roles with the system accounts of Linux. If a role exists in PostgreSQL, the same Linux user account with the same name is able to log in as that role.

When you installed PostgreSQL, the installation process created a user account called postgres associated with the default postgres role.

### Enter Postgres Context 

To access PostgreSQL CLI we need to enter the context of postgres user account.

- Switch user to postgres account with **su** command

      sudo su - postgres

- Or

      sudo -i -u postgres
    - You will see postgres user prompt instead of your currently logged in server account

          postgres@my-server:~$

    - Now you are inside the postgres user role and able to execute PostgreSQL **CLI (command line interface)** functions

### Enter PSQL          

- While in your postgres role, execute psql command 

      psql

  - You will see the prompt: 
  
        postgres=#
  - This prompt is the name of the current database you are inside. The default database is called postgres. If you don't specify the name of the database as argument after psql command, then you will enter the default database **postgres**.
  - Now you are inside the PSQL prompt and able to execute SQL queries and psql specific commands related to databases. 

### Exit PSQL

- Exit out of PSQL

      \q

### Exit Postgres Context

- Return to regular system user 

      exit

### Directly Enter PSQL

- If you want to call psql without switching to postgres account first, you can do it like this

      sudo -u postgres psql

- To exit use **exit** or **\q**
  - This will return to your currently logged in server user


## Set password for user postgres

    sudo -u postgres psql
    \password    

- Exit Postgres and Restart the service to apply security changes

      \q
      exit

      sudo systemctl restart postgresql 

## Check connection info

      sudo -u postgres psql
      \conninfo


## Databases

- Ref:
  - [Create Database](https://www.postgresql.org/docs/current/sql-createdatabase.html)
  - [Drop Database](https://www.postgresql.org/docs/current/sql-dropdatabase.html)

 
### Create Database


- Switch to postgres user if you have not already

      sudo su - postgres

You can create database in 2 ways:

1. Create inside postgres context
          
   - Call `createdb` command
  
         createdb my_db

2. Create inside PSQL

   - Enter PSQL

          psql

   - Call `CREATE DATABASE` sql command

          CREATE DATABASE my_db;

      - You will get a confirmation message

            CREATE DATABASE

NOTE: psql is not case sensitive. You can write commands all in lowercase if easier. For example this is a valid command:

    create database my_db;


**NOTE:**
- SQL commands **MUST** end with **semi-colon ;** - if you don't put a semicolon at the end of the command, it will silently fail
- You should not use dashes in the name of the database e.g. **my-db**. You will be able to create the database, but it will throw an error when you try to delete the database with SQL DROP statement. Use underscores instead e.g. **my_db**.
- In case you have created database with dashes in the name, you will still be able to delete it in command line util with dropdb command.


### Access Database

Before you can get inside different database you need to exit the context of the current database.

If you are currently inside postgres database you will see the prompt like this: `postgres=#`

- Exit out of the current database

      \q

- Enter the newly created database

      psql my_db

  - You should see a prompt with the name of current database you are in

        my_db=#

  - Now you are inside PSQL so Postgres CLI commands will not work. You need to exit PSQL with **\q** to get back to CLI

### List Databases

If you are outside of database context, you will see prompt like this: `postgres@my-server:~$` and list database command will not work.

To list databases you must first enter PSQL

- Enter PSQL

      psql

  - This puts you by default in context of postgres database `postgres=#` 

- List databases

      \l

  - You should see 

                                                 List of databases
           Name    |  Owner   | Encoding |   Collate   |    Ctype    | ICU Locale | Locale Provider |   Access privileges
        -----------+----------+----------+-------------+-------------+------------+-----------------+-----------------------
         my_db     | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 |            | libc            |
         postgres  | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 |            | libc            |
         template0 | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 |            | libc            | =c/postgres          +
                   |          |          |             |             |            |                 | postgres=CTc/postgres
         template1 | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 |            | libc            | =c/postgres          +
                   |          |          |             |             |            |                 | postgres=CTc/postgres
        (4 rows)

  - Notice your newly created database **my_db** is at the top of the list.

### Directly enter database from system account

- Exit PSQL. Exit postgres account

      \q
      exit

- Directly Acces my_db from system account in one command

      sudo -u postgres psql my_db
  

- Exit to system account. Enter Postgres CLI

      \q

### Delete Database

You can delete database in 2 ways:

1. Delete inside command line util. Switch to postgres user and then call dropdb command.

        sudo su - postgres
        dropdb my_db

2. Delete inside database. Switch to postgres user, enter database, call DROP DATABASE sql command

        sudo su - postgres
        psql
        DROP DATABASE my_db;


### List tables

    \dt




## Users

- Ref: 
    - [Create User](https://www.postgresql.org/docs/current/sql-createuser.html)
    - [Alter User](https://www.postgresql.org/docs/current/sql-alteruser.html)
    - [Drop User](https://www.postgresql.org/docs/current/sql-dropuser.html)

### Create User

- Enter PSQL

      sudo -u postgres psql

- Create new user

      CREATE ROLE myuser WITH CREATEDB LOGIN ENCRYPTED PASSWORD 'mypassword';
    OR
    
      CREATE USER myuser WITH CREATEDB ENCRYPTED PASSWORD 'mypassword';
  
  - NOTE: dashes in username (e.g. my-user) are not allowed

### List Users

    \du

- You should see

                                         List of roles
       Role name |                         Attributes                         | Member of
      -----------+------------------------------------------------------------+-----------
       myuser    | Create DB                                                  | {}
       postgres  | Superuser, Create role, Create DB, Replication, Bypass RLS | {}


### Change Password

    ALTER USER myuser WITH ENCRYPTED PASSWORD 'StrongPass#!~';

  - You will get a confirmation message

        ALTER ROLE

### Change Username

    ALTER USER myuser RENAME TO my_user;

### Log in With Non-Default User

- Exit out of PSQL 

      \q

- Try to log in with new user **my_user**

      psql -U my_user

  - you should get

        psql: error: connection to server on socket "/var/run/postgresql/.s.PGSQL.5432" failed: FATAL:  Peer authentication failed for user "my_user"

  - Peer authentication failed error happens when you try to login to your PostgreSQL user but authentication fails because by default psql connects over UNIX sockets using peer authentication instead of password authentication.
  - peer authentication tries to log in the given user with system login instead of PostgreSQL login.
  - Because there is no user **my_user** created on our server, the peer authentication will fail
  - To fix this we will have to change pg_hba.conf

- Exit postgres user: 

      exit

- Find location of pg_hba.conf

      sudo find / -name "pg_hba.conf"

- Open config file **pg_hba.conf**

      sudo nano /etc/postgresql/17/main/pg_hba.conf

- Change line

      local   all             all                                peer

  - to

        local   all             all                                md5

  - **peer** means it will trust the authenticity of UNIX user hence does not prompt for the password.
  - **md5** means it will always ask for a password, and validate it after hashing with MD5.

- Restart postgresql service

      sudo service postgresql restart

- Switch UNIX account to postgres

      sudo su - postgres

- Try log in with my_user

      psql -U my_user

  - This time you should get

        psql: error: connection to server on socket "/var/run/postgresql/.s.PGSQL.5432" failed: FATAL:  database "my_user" does not exist

  - This is because by default, postgresql tries to connect to a database with the same name as your user. So we have to specify what database we are connecting to

- Log in with my_user, this time specifying the database you want to log into

      psql -U my_user -d my_db

  - You will be prompted for password and on succesful login, you will get a database prompt like this:

        my_db=>

### Show current user

Because the prompt shows what is the current database but not who is the current user, we may want to verify this info.

- Verify current user

      select current_user;

  - You should get

         current_user
        --------------
         my_user
        (1 row)

### Delete User

- Make sure you exit database and enter as super user postgres

      \q
      psql
  
- Delete user with check if it exists

      DROP USER IF EXISTS my_user;

  - You will see a response **DROP ROLE** which confirms that user has been deleted. 
  - In database delete is called DROP, user is called ROLE.

  - If you run the same command again you will get the response:

        NOTICE:  role "my_user" does not exist, skipping
        DROP ROLE




## Help

NOTE: These commands will not work inside Postgres CLI, or they will perform a diferent function.

### SQL Commands Help

- Show a list of all SQL commands you can get help on

      \h

  - You can then use \h with that command as argument to get usage description for the given command
  - Press **q** to exit help

- Show help for specific command

      \h CREATE USER

  - Note: At the end of each command's help text there is a url to PostgresQL website page for that command which contains more details and examples

        URL: https://www.postgresql.org/docs/17/sql-createuser.html

### PSQL Commands Help

- Show a list of all PSQL commands you can get help on

      \?



## pgAdmin - Web GUI for PostgreSQL


- Ref: [PG Admin Home](https://www.pgadmin.org) > [Ubuntu Download](https://www.pgadmin.org/download/pgadmin-4-apt/)

### Install pgAdmin

- Run the below commands one by one

      #
      # Setup the repository
      #
      
      # Install the public key for the repository (if not done previously):
      curl -fsS https://www.pgadmin.org/static/packages_pgadmin_org.pub | sudo gpg --dearmor -o /usr/share/keyrings/packages-pgadmin-org.gpg
      
      # Create the repository configuration file:
      sudo sh -c 'echo "deb [signed-by=/usr/share/keyrings/packages-pgadmin-org.gpg] https://ftp.postgresql.org/pub/pgadmin/pgadmin4/apt/$(lsb_release -cs) pgadmin4 main" > /etc/apt/sources.list.d/pgadmin4.list && apt update'
      
      #
      # Install pgAdmin
      #
      
      # Install for both desktop and web modes:
      sudo apt install pgadmin4
      
      # Install for desktop mode only:
      sudo apt install pgadmin4-desktop
      
      # Install for web mode only: 
      sudo apt install pgadmin4-web 
      
      # Configure the webserver, if you installed pgadmin4-web:
      sudo /usr/pgadmin4/bin/setup-web.sh

### Configure Web App

Installation script for pgAdmin4 configures the web app to run on Apache web server so that web admin is accessible on http://localhost/pgadmin4. 

However we have configured Nginx to be our primary front web server and Apache to be our secondary back web server and run on non-default port 8080.

In order to access pgAdmin4 from outside through our server IP like http://123.234.345.45/pgadmin4 we need to write a proxy setting inside our Nginx default server block.

- Open Nginx default server block

      sudo nano /etc/nginx/sites-available/default

  - Look for section starting with words: **location /**
  - Above it add a new location section that resolves **/pgadmin4** by redirecting it to Apache address. It should now look like this  

        location /pgadmin4 {
                # Redirect to Apache Web Server we configured to listen on port 8080
                proxy_pass http://localhost:8080/pgadmin4;
        }

        location / {
                # First attempt to serve request as file, then
                # as directory, then fall back to displaying a 404.
                try_files $uri $uri/ =404;
        }

- Restart Nginx

      sudo service nginx restart


- Navigate to pgAdmin4 on your server from anywhere like http://{{YOUR_SERVER_IP}}/pgadmin4

- Log in with email and password you have set up during the pgAdmin4 installation.

### Register Local Server

- Right-Click on **Servers** in the left panel > Register > Server
- Fill out tab **General** fields

    | General |           |
    | ------- | --------- |
    | Name    | localhost |
    |         |           |

- Fill out tab **Connection** fields. Use default **postgres** user and its password.

    |                      | Connection |
    | -------------------- | ---------- |
    | Host name/address    | localhost  |
    | Port                 | 5432       |
    | Maintenance database | postgres   |
    | Username             | postgres   |
    | Password             | ******     |
    |                      |            |

- Click button **Save**
  - Note - If session has expired because you took too long to submit the form, the server registration fill fail silently. In that case go back to **/pgadmin4** page and re-login.

- Your localhost server should now appear in the list of servers. You can now expand the databases and use graphic user interface to execute operations on database objects.


## Allow Remote Connections

- Check status of ports that server is listening on

      netstat -nlt

  - You should see something like

        Active Internet connections (only servers)
        Proto Recv-Q Send-Q Local Address           Foreign Address         State
        tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN
        tcp        0      0 127.0.0.1:5432          0.0.0.0:*               LISTEN
        tcp        0      0 0.0.0.0:443             0.0.0.0:*               LISTEN
        tcp        0      0 127.0.0.1:5000          0.0.0.0:*               LISTEN
        tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN
        tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN
        tcp6       0      0 :::8080                 :::*                    LISTEN
        tcp6       0      0 ::1:5432                :::*                    LISTEN
        tcp6       0      0 :::443                  :::*                    LISTEN
        tcp6       0      0 ::1:5000                :::*                    LISTEN
        tcp6       0      0 :::22                   :::*                    LISTEN
        tcp6       0      0 :::80                   :::*                    LISTEN

  - Notice Postgres port 5432 is only open for local host connections : **127.0.0.1:5432**

- We need to open port 5432 for connections from outside.


### Configure Allowed IPs in postgresql.conf

- Get the location of config file

      sudo -u postgres psql -c "SHOW config_file;"

  - You should get something like

        /etc/postgresql/17/main/postgresql.conf

- Open config file **postgresql.conf**

      sudo nano /etc/postgresql/17/main/postgresql.conf

- Search for **listen_addresses** . Use  **CTRL + W** to search.

- Allow any IP to access database, change this setting to

      listen_addresses = '*'

  - Optionally you can allow specific IPs. To do that, assign comma separated list of IPs

        listen_addresses = '157.177.247.140,194.225.99.213,221.143.139.210'

- Save and exit: **CTRL + S**, **CTRL + X**

- Restart PostgreSQL service

      sudo systemctl restart postgresql

- Verify that port 5432 is open for outside connections

      netstat -nlt

- Notice this time port 5432 is prefixed with **0.0.0.0:** which means any IP can connect

      tcp        0      0 0.0.0.0:5432            0.0.0.0:*               LISTEN


### Configure Host Authentication in pg_hba.conf

Ref: [pg_hba.conf File documentation](https://www.postgresql.org/docs/17/auth-pg-hba-conf.html)

> Client authentication is controlled by a configuration file named pg_hba.conf. HBA stands for host-based authentication

- Find location of pg_hba.conf

      sudo find / -name "pg_hba.conf"

  -  You should see a list of locations. We are interested in the one that is inside the PostgreSQL installation folder. It should be the very last line of the results. It should look something like this `/etc/postgresql/17/main/pg_hba.conf`

- Open config file **pg_hba.conf**

      sudo nano /etc/postgresql/17/main/pg_hba.conf

- Scroll to the bottom of the file : PAGE-DOWN

- Accept remote connections from any IP by adding the following lines at the bottom of the file

      #------------------------------------------------------------------
      # Accept remote connections from any IP
      #------------------------------------------------------------------  
      # IPv4 Addresses
      host    all             all             0.0.0.0/0               md5  
      # IPv6 Addresses
      host    all             all             ::0/0                   md5

- Restart PostgreSQL

      sudo systemctl restart postgresql

### Open PostgreSQL Port in Firewall

Default port is set to 5432 which can be changed in the postgresql.conf

- Open the port **5432** in the firewall

      sudo ufw allow 5432

- Confirm that the port is open

       sudo ufw status


## Local PC PostgreSQL 

### Install PostgreSQL

- Install PostgreSQL on local PC. GO to [PostgreSQL Download](https://www.postgresql.org/download/) for instructions and download link. Download and run installer.

- This will also install pgAdmin4 - UI client for administration of PostgreSQL databases

- On Windows operating system, PostgreSQL should be installed at C:\Program Files\PostgreSQL\17, where 17 is the current version of PostgreSQL at the time of writing. This could be a different version at the time you install it.

### Add the Path to Environment Variable

- Add the path to PostgreSQL binaries to environment variable Path
  - Click on Windows icon bottom left corner and start typing **"Env"** > click on **Edit the system environment variables** > Environment variables > **System variables** list > Select item **Path** > Click button **Edit** > Click **New** > Paste the path like **C:\Program Files\PostgreSQL\17\bin** > Click **OK**
  - Now you can call **psql** command line tool

### Log into PSQL

- Open Git Bash and type in **psql** 
  - You will be prompted to provide password for current user

        Password for user Admin:

  - This user does not exist yet in PostgreSQL yet, but psql uses the name of the operating system user as default user to log in.
  - The only user created in PostgreSQL is super user **postgres** that was created during the installation.

- Log into psql as user **postgres**

      psql -U postgres

  - Use the password you provided during the installation
  - You should now be inside psql prompt and able to execute any psql commands

        postgres=#

- To exit 

      \q

### Set up pgAdmin

#### Rename local server

By default server is named like **PostgreSQL 17**.

We should rename it for clarity

- Right-click server name  **PostgreSQL 17** > Properties > Name
- Change name to : localhost_{{YOUR_COMPUTER_NAME}} e.g. **localhost_my-pc**



#### Set path to binaries

- Open pgAdmin4 and log into it with admin password you provided on installation
- Set up path to binaries: 
  - Navigate to : **File > Preferences > Paths > Binary paths**
  - Inside section PostgreSQL Binary Paths > PostgreSQL 17 
  - Check radio button before your version of Popstgres e.g. PostgreSQL 17
  - Inside Binary Path field paste path to binaries e.g. **C:\Program Files\PostgreSQL\17\bin**

#### Show/Hide Nodes in Browser tree

pgAdmin can be hard to browse because of so many nodes displayed in the Browser.

We can configure what nodes we want to hide in the Browser tree for higher visibility.

- Go inside: **File > Preferences > Browser > Nodes**

- To show only **Tables**, inside each schema, turn **OFF** the switch on the following items and click Save
    - Aggregates
    - Collations
    - Domains
    - FTS Configurations
    - FTS Dictionaries
    - FTS Parsers
    - FTS Templates
    - Foreign Tables
    - Functions
    - Materialized Views
    - Operators
    - Procedures
    - Sequences
    - Trigger Functions
    - Types
    - Views

- To show only **Schemas**, inside each database, turn **OFF** the switch on the following items and click Save
    - Casts
    - Catalogs
    - Event Triggers
    - Extensions
    - Foreign Data Wrappers
    - Languages
    - Publications
    - Subscriptions


- To show only **Columns**, inside each table, turn **OFF** the switch on the following items and click Save
    - Indexes
    - Constraints
    - RLS Policies
    - Rules
    - Triggers

- Refresh Browser tree : right-click on **Databases > Refresh**

### Connect to Server with CLI

**NOTE:** You must first set up PostgreSQL on remote server to allow remote connections. See section [Allow Remote Connections](#allow-remote-connections)

- Open Git Bash

You can connect in different ways:

1. Connect using defaults

        psql -U postgres -h 123.234.345.45

    |     |         |
    | --- | ------- |
    | -U  | user    |
    | -h  | host IP |
 
3. Connect with connection string. Explicitly define sslmode is required - this is not really necessary in new version of PostgreSQL because SSL is enabled by default.

        psql "sslmode=require host=123.234.345.45 dbname=postgres user=postgres"

2. Connect defining all the parameters

        psql -h 123.234.345.45 -p 5432 -d my_db -U postgress -W

    |     |               |
    | --- | ------------- |
    | -h  | host IP       |
    | -p  | port          |
    | -d  | database name |
    | -U  | user          |
    |     |               |


- You should see

      WARNING: Console code page (437) differs from Windows code page (1252)
               8-bit characters might not work correctly. See psql reference
               page "Notes for Windows users" for details.
      SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384,    compression: off)
      Type "help" for help.
      
      postgres=#

  - You can ignore the warning. If you want it to go away you can try solution to type in **"chcp.com 1252"** into Git Bash before connecting
  - Second line "**SSL connection (protocol: ...**". This just means that SSL is used to connect

- Type the command to list all the databases on remote server

      \list


### Connect to Server with pgAdmin4


**NOTE:** You must first set up PostgreSQL on remote server to allow remote connections. See section [Allow Remote Connections](#allow-remote-connections)

- Open **pgAdmin** on your local PC
- Right-Click on **Servers** in the left panel > Register > Server
- Fill out tab **General** fields

    | General |           |
    | ------- | --------- |
    | Name    | localhost |
    |         |           |

- Fill out tab **Connection** fields. Use default **postgres** user and its password.

    |                      | Connection |
    | -------------------- | ---------- |
    | Host name/address    | localhost  |
    | Port                 | 5432       |
    | Maintenance database | postgres   |
    | Username             | postgres   |
    | Password             | ******     |
    |                      |            |

- Click button **Save**

- Your remote database server should now appear in the list of servers on the left. You can now expand the databases and use graphic user interface to execute operations on database objects.


### Backup & Restore with pgAdmin

#### Backup

- In pgAdmin right-click on your remote server database e.g.  my-server/Databases/**my_db** 
- Backup > Filename : Click folder to open file browser > Select folder location > Name file **my-db.backup** > Click button **Backup**

#### Restore

- Make sure that database with the given name already exists e.g.my_db. Otherwise create it: right-click localhost_my-pc/Databases > Create 

- right-click on your local server database e.g. localhost_my-pc/Databases/my_db > Restore > FIlename > Select backup file we downloaded from remote server e.g. **my-db.backup**

- Click Restore

## Other Database Management Tools

While pgAdmin is a great tool for PostgreSQL database management, it can seem a bit complex. For example to get to tables one needs to expand 4 levels of hierarchy on the Browser panel. Then if your tables are under different Schemas for different modules, you have to expand Schemas to see tables for each.

- Tools we recommend for simpler view of tables are:

    | Tool                                                                         | Notes                                                                       |
    | ---------------------------------------------------------------------------- | --------------------------------------------------------------------------- |
    | [Libre Ofice](https://www.libreoffice.org/)                                  | Strict Connection string format: host=123.234.345.45 port=5432 dbname=my_db |
    | [Azure Data Studio](https://azure.microsoft.com/en-au/products/data-studio/) | Must install Extension for PostgreSQL                                       |
    |                                                                              |                                                                             |
    |                                                                              |                                                                             |


