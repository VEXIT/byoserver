# Linux - Web App - .Net Core & Angular


|               |                                                  |
| ------------- | ------------------------------------------------ |
| Copyright:    | © 2022 VEXIT , Tomorow is today ®, www.vexit.com |
| Author:       | Vex Tatarevic                                    |
| Date Created: | 2022-10-27                                       |
| Date Updated: | 2024-12-13                                       |
|               |                                                  |


## Install Local Dependencies

Local computer is our personal computer that we will use to develop our Web App.

Install the following software on your local PC.


|                                                   |                                                                                                                                                                                                                                             |
| ------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [.Net SDK](https://dotnet.microsoft.com/download) | This is a software development kit for .Net. It includes the .Net runtime, command line tools and libraries that you need to build and run .Net apps. It is a free and open source software. It is available for Windows, Linux and Mac OS. |
| [Visual Studio Codium](https://vscodium.com/)     | This is a free and open source version of Visual Studio Code. It is the same as Visual Studio Code, but without Microsoft branding, telemetry and licensing. It is a community-driven fork of Microsoft’s Visual Studio Code.               |
| [Git SCM](https://git-scm.com/) - Only on Windows | This is a command line tool that allows you to run commands in terminal. It is a part of Git version control system. If you are on Linux or Mac you can use your default terminal.                                                          |


## Install Server Dependencies


### Uninstall .Net

- Connect to your remote server in terminal

      ssh my-server

Before you install the latest version of .Net you should make sure that all older versions are completely removed:

- Check if .Net is already installed

      dotnet --version

  - If **NOT installed** you will get `-bash: dotnet: command not found`. In this case **SKIP** to section [Install .Net](#install-net)

  - If installed you will get something like `7.1.100`. In this case you need to uninstall it first - Continue to next step




- Remove .Net package installations

      sudo apt-get remove --purge dotnet*

- Delete **dotnet** folder from all usual installation locations - run the following connected commands as one command

        sudo rm -r ~/.dotnet && \ 
        sudo rm -r /usr/bin/dotnet && \
        sudo rm /usr/bin/dotnet && \
        sudo rm -r /usr/share/dotnet

- Remove paths to dotnet executables from the PATH environment variable
  - View Path

        echo $PATH

    OR better display paths one per line:

          sed 's/:/\n/g' <<< "$PATH"


    - This will show a list of directory paths separated by colons. Those are the paths that lead to executable programs. This allows us to run executable commands like **dotnet** directly at any location in the system.

    - For example if your username is **admin** and dotnet was previously installed at the user's home directory, you may see something like:

          /usr/local/sbin:/usr/lo... /home/admin/.dotnet:/home/admin/.dotnet/tools

  - Remove dotnet/tools dir from PATH

        directory_to_remove=/home/$(whoami)/.dotnet/tools
        PATH=:$PATH:
        PATH=${PATH//:$directory_to_remove:/:}
        PATH=${PATH#:}; PATH=${PATH%:}

  - Remove .dotnet dir from PATH

        directory_to_remove=/home/$(whoami)/.dotnet
        PATH=:$PATH:
        PATH=${PATH//:$directory_to_remove:/:}
        PATH=${PATH#:}; PATH=${PATH%:}



### Install .Net

Ref: https://learn.microsoft.com/en-us/dotnet/core/install/linux

There are multiple options to install .Net on Linux:
- [Option 1 - Microsoft Bash Script](#option-1---install-net---with-bash-script) - We recommend this option - the most reliable option when trying to install the latest version of .Net.
- [Option 2 - APT Package Manager](#option-2---install-net---with-apt) - the most convenient option, but relies on your Linux distribution's repositories. For example if you're on Ubuntu Linux and you try to install the latest version of .Net that just came out, maybe it will not work with **APT** because Ubuntu repositories have not provided the latest version of .Net yet.
- [Option 3 - Manual Installation](#option-3---install-net---manually) - if all else fails, this option will work 100%. It's just a little more effort.

#### Option 1 - (Recommended) - Install .Net - with Bash Script

This is a recommended option for installation of .Net on Linux.

Ref: [Microsoft - Scripted Install](https://learn.microsoft.com/en-us/dotnet/core/install/linux-scripted-manual#scripted-install)

1. **Go inside downloads dir**, for example:

        mkdir ~/downloads \
        cd ~/downloads

2. **Download** the dotnet install script

       wget https://dot.net/v1/dotnet-install.sh -O dotnet-install.sh

3. **Grant permissions** to execute
  
       sudo chmod +x ./dotnet-install.sh

4. **Execute script** -  Install specific version to reliably install the latest version of .Net. In the example below latest version is 8.0
  
       sudo ./dotnet-install.sh --install-dir /usr/share/dotnet --channel 8.0

   - **IMPORTANT:** 
  
      IF you get ERROR:

         sudo: ./dotnet-install.sh: command not found
      
      OR

         -bash: ./dotnet-install.sh: Permission denied

      THEN modify access to allow execution

         sudo chmod 750 dotnet-install.sh

5. **Symlink** standard binaries location **/usr/bin/dotnet** to location of our dotnet installation **/usr/share/dotnet/dotnet**

       sudo ln -s /usr/share/dotnet/dotnet /usr/bin/dotnet

6. **Confirm** that dotnet is installed

       dotnet --version

   - You should see something like

         9.0.101

- Go to section [Verify Installation](#verify-installation)

#### Option 2 - Install .Net - with APT

- Refs: 
  - [Microsoft - Install .NET on Linux](https://learn.microsoft.com/en-us/dotnet/core/install/linux)


- Install SDK using APT (Advanced Packaging Tool) - this will install runtime as well.
    
      sudo apt-get update && \
      sudo apt-get install -y dotnet-sdk-8.0
  

  - PLEASE CHANGE dotnet-sdk-8.0 to whatever is the current version of dotnet - see this guide [Microsoft - Install .NET on Ubuntu Linux](https://learn.microsoft.com/en-us/dotnet/core/install/linux-ubuntu#install-the-sdk)
  - The .NET SDK allows you to develop apps with .NET. If you install the .NET SDK, you don't need to install the corresponding runtime.

- If installation fails, go to [.Net Installation failed](#net-apt-installation-failed) section for possible explanation of causes or just skip to [Manual Installation](#option-3---install-net---manually)


##### .Net APT Installation Failed

- You may get these errors

      E: Unable to locate package dotnet-sdk-8.0
      E: Couldn't find any package by glob 'dotnet-sdk-8.0'
      E: Couldn't find any package by regex 'dotnet-sdk-8.0'

- In this case it is possible that Ubuntu has not updated their repositories to include the latest version of .Net
- Check your Ubuntu version

      lsb_release -a

- You should see something like

      No LSB modules are available.
      Distributor ID: Ubuntu
      Description:    Ubuntu 22.04.1 LTS
      Release:        22.04
      Codename:       jammy

- Note the Codename. That's the name of the Ubuntu distribution.
- Go to [packages.ubuntu.com](https://packages.ubuntu.com/)
- Scroll down to **Search Package directories**
- Enter 
  - Keyword: dotnet
  - Search on: Package names only
  - Distribution: {{NAME_OF_YOUR_DISTRIBUTION}}

- Click **Search** button
- On the next page you should see the version of .Net that you are after if it is available for this Ubuntu Distribution. Otherwise it will not be there.
- If the .Net version is not available then you need to do the manual installation. Go to [Manual Installation](#option-3---install-net---manually)


#### Option 3 - Install .Net - Manually

If installation using package manager did not work, only then you should do the manual installation. Otherwise skip to [Verify Installation](#verify-installation)

Ref: [Microsoft - Dotnet Manual Install on Linux](https://learn.microsoft.com/en-us/dotnet/core/install/linux-scripted-manual#manual-install)

1. **Download installation file**

   - Go to official [.NET Download Page](https://dotnet.microsoft.com/en-us/download/dotnet) 

   - Click the link for .NET latest (at the time of writing this [.NET 8.0](https://dotnet.microsoft.com/en-us/download/dotnet/8.0)). This will take you to download page.

   - Look for section: **SDK > OS Linux > x64**
   - Click on x64 link
     - This will take you to download page with url like: https://dotnet.microsoft.com/en-us/download/dotnet/thank-you/sdk-8.0.101-linux-x64-binaries
     - This will start download, which you can cancel

   - You should see **"Direct link"** field with url like this next to it

          https://download.visualstudio.microsoft.com/download/pr/7fe73a07-575d-4cb4-b2d3-c23d89e5085f/d8b2b7e1c0ed99c1144638d907c6d152/dotnet-sdk-8.0.101-linux-x64.tar.gz

   - Copy direct link url by clicking on **Copy** button 

2. **Go inside downloads dir**, for example:

        cd /home/{{YOUR_USERNAME}}/downloads

3. **Download dotnet** tar.gz file from copied url

        wget https://download.visualstudio.microsoft.com/download/pr/7fe73a07-575d-4cb4-b2d3-c23d89e5085f/d8b2b7e1c0ed99c1144638d907c6d152/dotnet-sdk-8.0.101-linux-x64.tar.gz

     - This will download the file named like : **dotnet-sdk-8.0.101-linux-x64.tar.gz**

4. [Uninstall Previously Installed .NET](#uninstall-net) if you haven't done so already


5. **Extract binaries** into user's home directory **"~/dotnet"** - To do that, copy-paste the following script and hit Enter (make sure to replace the name of tar.gz file with the one that you have downloaded in the download step above):

        DOTNET_FILE=dotnet-sdk-8.0.101-linux-x64.tar.gz
        export DOTNET_ROOT=/home/$(whoami)/dotnet
      
        mkdir -p "$DOTNET_ROOT" && tar zxf "$DOTNET_FILE" -C "$DOTNET_ROOT"
      
6. **Copy to /usr/share/** directory

        sudo mv ~/dotnet /usr/share/dotnet

7. **Symlink** standard binaries location **/usr/bin/dotnet** to location of our dotnet installation **/usr/share/dotnet/dotnet**

        sudo ln -s /usr/share/dotnet/dotnet /usr/bin/dotnet



### Verify Installation

- To verify successfull .Net installation, run command:

      dotnet --list-runtimes

  - You should see something like
    
        Microsoft.AspNetCore.App 9.0.0 [/usr/share/dotnet/shared/Microsoft.AspNetCore.App]
        Microsoft.NETCore.App 9.0.0 [/usr/share/dotnet/shared/Microsoft.NETCore.App]



- For any more details - Visit official .Net download page which should be located at: https://dotnet.microsoft.com/download

### Enable Tab command completion for the .NET CLI

- Open ~/.bashrc : `nano ~/.bashrc`
- Add following lines at the bottom of the file

      # .NET CLI Tab Command Completion
      function _dotnet_bash_complete()
      {
        local cur="${COMP_WORDS[COMP_CWORD]}" IFS=$'\n'
        local candidates
      
        read -d '' -ra candidates < <(dotnet complete --position "${COMP_POINT}" "$     {COMP_LINE}" 2>/dev/null)
      
        read -d '' -ra COMPREPLY < <(compgen -W "${candidates[*]:-}" -- "$cur")
      }

      complete -f -F _dotnet_bash_complete dotnet

- Reconnect to Server to see changes
- Check if tab completion is enabled for dotnet commands. Type this

      dotnet a

    - then hit TAB
    - The command should be completed to read this

            dotnet add


## Verify .NET Working

We will check if .Net is working properly by creating a simple console app. on our server and running it.

Generally you will never put development files on the server, but for the purpose of this test we will do it.

- **Create dev folder** inside your user folder

      mkdir ~/dev

- **Create dotnet console app** called HelloWorld inside dev folder

      dotnet new console -o ~/dev/HelloWorld

  - This will create folder **HelloWorld** and inside it project file **HelloWorld.csproj** with all other application files.

Ref: Check out what other type of applications you can create using [dotnet new command](https://learn.microsoft.com/en-us/dotnet/core/tools/dotnet-new)

- **Go inside the project** folder and list files

      cd ~/dev/HelloWorld && \
      ls -l
    
    - You should see something like this
    
          total 12
          -rw-r--r-- 1 admin admin  252 Nov 23 12:50 HelloWorld.csproj
          drwxr-xr-x 2 admin admin 4096 Nov 23 12:50 obj
          -rw-r--r-- 1 admin admin  105 Nov 23 12:50 Program.cs

- **Run the app**

      dotnet run

  - You should see output

        Hello World!

- Open Program.cs file in nano to see the code:

      nano Program.cs
    
    - You will only see one line of code and a comment above it

          // See https://aka.ms/new-console-template for more information
          Console.WriteLine("Hello, World!");


NOTE: By default when we create console app, there is just one line of code that prints **"Hello World!"** to the console. Notice that all the Object Oriented Programming syntax of C# like namespace, class and methods are missing. This is because Program.cs is using new feature of C# called **top-level statements**. It is a new way of writing C# code that is more concise and easier to read. It is a good choice for simple console apps. However, we do not recommend using it for more complex apps. For more info on top-level statements [click here](https://learn.microsoft.com/en-us/dotnet/csharp/whats-new/tutorials/top-level-statements).

We will not develop on our server machine from this point on. This was just a test to make sure that .Net is installed and working properly.

 
## 1. CREATE APP

Ref: [Microsoft - Host and deploy Linux with Nginx](https://learn.microsoft.com/en-us/aspnet/core/host-and-deploy/linux-nginx?view=aspnetcore-9.0&tabs=linux-ubuntu)
    

In the context of web-application / website, when we talk about serverside app, we are talking about Web API app. Web API app is a backend app that provides data to the clientside/frontend app by means of API end points. The frontend app can be a web app, mobile app or any other type of app that can make HTTP requests to web api end points. In our case the frontend app will be a web app built using Angular framework.

Together the frontend and backend apps form a full stack web application. The frontend app is also called clientside app because it runs on the client's machine. The backend app is also called serverside app because it runs on the server.

But first let's create a simple Web API app and deploy it to our server. We will use this app to test our server configuration and make sure that our website is accessible on the internet. 


In the following section we will work on our client machine to create a Web API app using .Net Core. We will then publish the app and deploy it to our server.

### Create Solution

Ref: https://learn.microsoft.com/en-us/dotnet/core/tools/dotnet-sln



- Open Terminal (on Linux/Mac OS) or Git Bash (on Windows)

- Create project folder inside your user folder. We will call our project folder **mysite1.com**. (**_replace "mysite1.com" with your own website domain name_**)

      mkdir -p ~/dev/mysite1.com

  - **-p** flag is used to create parent directories if they do not exist. In this case it will create folder **dev** if it does not exist.
 
- Go inside project folder

      cd ~/dev/mysite1.com

- Create folder **docs**. We will use it to store all documentation files related to our project.

      mkdir docs

- Create folder **src**. We will use it to store all source code files related to our project.

      mkdir src

- Go inside src folder

      cd src

- Check list of available project templates in dotnet for your own reference

      dotnet new list

  - We will use Solution File template

- Create solution file called Server

      dotnet new sln --name Server

  - This will create file **Server.sln**
  - A solution is a container to organize one or more related code projects. We may have other projects in the future that we will add to this solution.

- **Folder structure** should now look like this:

      dev
      └── mysite1.com
          ├── src
          │   ├── Server.sln
          │   └── Server
          └── docs

  - **src** folder - where we store source code
  - **docs** folder - where we keep all other files related to our project, such as documentation, diagrams, images, etc.


- **Full path** to your project should look like this on each OS: (**_replace "admin" with your own administration username_**)

  | OS      | Path to project folder         |
  | ------- | ------------------------------ |
  | Windows | C:\Users\admin\dev\mysite1.com |
  | Linux   | /home/admin/dev/mysite1.com    |
  | Mac OS  | /Users/admin/dev/mysite1.com   |


### Create Web API App


> **API** (Application Programming Interface) - is a software that provides a way for two or more computer programs to communicate with each other. It is a software that offers a service to other pieces of software. 

>  **Web API** - is an application programming interface for a web server. Web API is accessed over the web using HTTP protocol. It is a concept and not a technology. We can build Web API using different technologies such as Java, .NET etc. Web API contains **endpoints** that can be **called by clientside apps** to perform some action. For example, if we want to build a Web API for a website that will allow users to register and login, we can create endpoints like /register and /login. The client will call these endpoints to register and login. The Web API will perform the action and return the result to the client. The client can be a web browser, mobile app or any other application that can make HTTP requests.

Ref: https://learn.microsoft.com/en-us/dotnet/core/tools/dotnet-new-sdk-templates#webapi

We will create a Web API app which in context of a full stack web application represents a **backend or a serverside** of the application. Serverside app will provide data to the **frontend or a clientside app**. The client side app will be a separate app that we will create later on, using Angular framework.

- Create .Net project of type ASP.NET Core Web API, called Server - in your open terminal run command

      dotnet new webapi --use-program-main --use-controllers -o Server

  - **--use-program-main** - If specified, an explicit Program class and Main method will be used (which is a traditional structure) instead of top-level statements which is new feature used by default since .Net 6. For more info on top-level statements [click here](https://learn.microsoft.com/en-us/dotnet/csharp/whats-new/tutorials/top-level-statements). We do not recommend using top-level-statements if you don't have much experience with .Net.
  - **--use-controllers** - to use controllers instead of minimal APIs. Since .Net 8 minimal APIs are the default. We do not recommend using minimal APIs if you don't have much experience with .Net. For more info on **minimal APIs** [click here](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/minimal-apis/overview).
  - **-o** - means output folder path/name.  
  - For full reference of the **dotnet new webapi** command [click here](https://learn.microsoft.com/en-us/dotnet/core/tools/dotnet-new-sdk-templates#webapi).

 
This will create a folder **Server** and inside it project file **Server.csproj** with all other application files.

We will refer to this project as Server App.
      
 
- Add the project Server to the solution Server.sln that we created previously

      dotnet sln add Server


### Open in VS Codium 

- Open current folder **src** in VS Codium

      codium .

- You should see a popup in the bottom-right that says "Required assets to build and debug are missing from 'Server'. Add them?"

  - Click **Yes**

### Open Terminal

- Open Terminal window in VS Codium:  hit **CTRL + `**
  - You should see terminal panel appear at the bottom of VS Codium

- The small toast-alert should pop up at the bottom right corner of VS Codium (If the toast alert does not pop up, click on the **bell icon** in the bottom right corner of VS Codium and the toast-alert will come out). It will show the message: 
      
      Required assets to build and debug are missing from 'src'. Add them?

  - Click **Yes** to add the missing assets.


- If you are on Windows and Git Bash is not set as default terminal :
  - Hit `CTRL + SHIFT + P` to open Command Palette  (you can also hit `F1` key)
  - Start typing: **"terminal select default"** until you see option **Terminal: Select Default Profile**. Then click on it.
  - Click **Git Bash** to select it a a default terminal. Now when you open terminal in VS Codium, it will open Git Bash terminal instead of Powershell terminal.

- Go inside project Server folder - in terminal execute cd command in terminal

      cd Server


### Configure launchSettings.json

- Open **Server/Properties/launchSettings.json**
- Remove section **iisSettings** - as we will not use IIS web server. The app will run on inbuilt Kestrel server
- Inside section **profiles** :
  - Remove **IIS Express** profile
- Inside section **profiles** : 
    - Change **launchBrowser** value to **false** - to prevent automatic opening of browser when we run the app
    - Remove **"launchUrl": "swagger"** - to prevent opening swagger page when we run the app (even though this is only relevant if running the app in Visual Studio and not in VS Codium)

- The content of launchSettings.json should now look like this:

      {
        "$schema": "https://json.schemastore.org/launchsettings.json",
        "profiles": {
          "http": {
            "commandName": "Project",
            "dotnetRunMessages": true,
            "launchBrowser": false,
            "applicationUrl": "http://localhost:5123",
            "environmentVariables": {
              "ASPNETCORE_ENVIRONMENT": "Development"
            }
          },
          "https": {
            "commandName": "Project",
            "dotnetRunMessages": true,
            "launchBrowser": false,
            "applicationUrl": "https://localhost:7216;http://localhost:5123",
            "environmentVariables": {
              "ASPNETCORE_ENVIRONMENT": "Development"
            }
          }
        }
      }



### Run App Locally

- In VS Codium Terminal, CD into Server folder if not there already

      cd Server

- Build project 

      dotnet build

- Run project Server from inside the project folder src/Server

      dotnet run

    OR if outside in the solution folder src/, specify project to run

      dotnet run --project Server

- You should see something like this in terminal

      Building...
      info: Microsoft.Hosting.Lifetime[14]
            Now listening on: http://localhost:5123
      info: Microsoft.Hosting.Lifetime[0]
            Application started. Press Ctrl+C to shut down.
      info: Microsoft.Hosting.Lifetime[0]
            Hosting environment: Development

- Open browser and navigate to http://localhost:5123/swagger
  - You should see swagger page with list of available endpoints
  - You should see only one endpoint : GET /WeatherForecast
  - Click on /WeatherForecast endpoint > Try it Out > Execute
  - You should see inside Response body textbox the sample data in json format returned from the server api 

> **Swagger** - is a tool that reads the structure of your API and generates documentation page from it. It also allows you to test your API endpoints directly from the browser. Swagger library is included in the .Net Web API project by default. Go to [Swagger](https://swagger.io/) website to learn more about it.

- Change address in browser to http://localhost:5123/weatherforecast
  - You should see the following response in the browser

        [{"date":"2024-01-04","temperatureC":-16,"temperatureF":4,"summary":"Freezing"},{"date":"2024-01-05","temperatureC":51,"temperatureF":123,"summary":"Hot"},{"date":"2024-01-06","temperatureC":-17,"temperatureF":2,"summary":"Warm"},{"date":"2024-01-07","temperatureC":-6,"temperatureF":22,"summary":"Hot"},{"date":"2024-01-08","temperatureC":8,"temperatureF":46,"summary":"Mild"}]

- Close the browser
- Stop the app by hitting **CTRL + C** in the terminal

Weatherforecast endpoint is a sample endpoint that is included in the .Net Web API project by default.

You can find the code for this endpoint in the file **Server/Controllers/WeatherForecastController.cs**. We will remove this file later on.

> Controller - is a class that contains Web API endpoints. It handles incoming HTTP requests and send response back to the caller. By default controller classes are placed inside Controllers folder, but you can place them anywhere you want in the project.


### Explaining Program.cs

We will only explain this file briefly here, just to give you basic idea what it is for.

- Open Program.cs file. It should look like this

      namespace Server;
      
      public class Program
      {
          public static void Main(string[] args)
          {
              var builder = WebApplication.CreateBuilder(args);
      
              // Add services to the container.
      
              builder.Services.AddControllers();
              // Learn more about configuring Swagger/OpenAPI at https://aka.ms/aspnetcore/swashbuckle
              builder.Services.AddEndpointsApiExplorer();
              builder.Services.AddSwaggerGen();
      
              var app = builder.Build();
      
              // Configure the HTTP request pipeline.
              if (app.Environment.IsDevelopment())
              {
                  app.UseSwagger();
                  app.UseSwaggerUI();
              }
      
              app.UseHttpsRedirection();
      
              app.UseAuthorization();
      
      
              app.MapControllers();
      
              app.Run();
          }
      }

- Program class is the entry point of our app. This is where the app starts.

- The **Main()** method is the first method that is called when the app starts.

There are 2 distinct code blocks inside Main() method:

1. **Configure Services** - Starts from the line `var builder = WebApplication.CreateBuilder(args);` and ends with the line `var app = builder.Build();`.
This code block is where we configure services that our app will use. For example, we can configure services like database, authentication, authorization, etc. We can also configure services like Swagger, which is a tool that generates documentation page for our API. We can add our custom services and they get added to the Dependency Injection so we can esily consume them in our app. We will learn more about Dependency Injection later on.

1. **Configure HTTP Pipeline** - Starts from the line `if (app.Environment.IsDevelopment())` and ends with the line `app.Run();`. This is where we configure how our app will handle incoming HTTP requests. For example, we can configure our app to use HTTPS protocol, we can configure our app to use authentication and authorization, we can configure our app to use Swagger, etc.


## 2. PUBLISH

### Publish Locally

Before we can deploy our app to the server, we need to publish it. Publishing will compile our code and prepare it for deployment.

When compiling for deployment, we need to compile with **Release configuration** option. This will optimize the code for deployment.

- Open terminal in VS Codium if not already open: hit **CTRL + `**

- Make sure that you are inside project folder **src/Server**. If not, navigate to it using cd command 

      cd Server

- Publish with Release configuration option

      dotnet publish -c Release

	- This will compile the code ready for deployment inside directory: **~/dev/mysite1.com/src/Server/bin/Release/net8.0/publish/**

Ref: [Microsoft - Publish to a folder](https://docs.microsoft.com/en-us/dotnet/core/deploying/deploy-with-cli#publish-to-a-folder)

- **Run the compiled Server.dll** - to confirm that published .ddl works run the following command in terminal

      dotnet ./bin/Release/net8.0/publish/Server.dll

  - This should print something like this in terminal

        info: Microsoft.Hosting.Lifetime[14]
              Now listening on: http://localhost:5000
        info: Microsoft.Hosting.Lifetime[0]
              Application started. Press Ctrl+C to shut down.
        info: Microsoft.Hosting.Lifetime[0]
              Hosting environment: Production

  - This means that our app is running on Kestrel server at url http://localhost:5000/ .

  - Paste http://localhost:5000/weatherforecast into browser and you should see the api response like

        [{"date":"2022-10-27T14:21:12.188435+10:00","temperatureC":23,"temperatureF":73,"summary":"Sweltering"},{"date":"2022-10-28T14:21:12.1891098+10:00","temperatureC":43,"temperatureF":109,"summary":"Sweltering"},{"date":"2022-10-29T14:21:12.1891209+10:00","temperatureC":-14,"temperatureF":7,"summary":"Freezing"},{"date":"2022-10-30T14:21:12.189122+10:00","temperatureC":-12,"temperatureF":11,"summary":"Sweltering"},{"date":"2022-10-31T14:21:12.1891228+10:00","temperatureC":37,"temperatureF":98,"summary":"Sweltering"}]

- Stop the app in terminal **CTRL + C**

> Kestrel is a lightweight, open-source, and cross-platform web server for ASP.NET Core. Kestrel is included by default in ASP.NET Core project templates. It is a good choice for hosting ASP.NET Core apps on Windows, Linux, or macOS. 

**NOTE:** Port **5000** is the default port for Kestrel server's **Release configuration**. We will change this port to something else in the section [Configure Release Urls](#configure-release-urls) .




### Set Up Environments

#### Preset Environments

.Net has 3 preset environments that come wired into framework: **Development**, **Staging** and **Production**. 
To ask .Net in the code what is the current environment we can use flags available in **IHostEnvironment** interface. It has **IsDevelopment()**, **IsStaging()** and **IsProduction()** methods.

For example in Program.cs when we are configuring http pipeline we can do something like this:

    if (app.Environment.IsDevelopment())
    {
      // do something
    }
    else if (app.Environment.IsStaging())
    {
      // do something
    }
    else if (app.Environment.IsProduction())
    {
      // do something
    }

In .Net we have **appsettings** files that are used to specify values for settings that apply to specific environment.

When we create Web Api project, it comes with two appsettings files by default: **appsettings.Development.json** and **appsettings.json**. 

> **appsettings.json** file stores the default configuration settings. These settings are applied to all environments. If we want to override particular settings for a specific environment, we need to create a separate appsettings file named like **appsettings.{{ENVIRONMENT_NAME}}.json**. For example if we want to override some settings for **Staging** environment, we need to create a separate appsettings file named **appsettings.Staging.json**.

#### Custom Environments

We will not use any of the .Net default environment names. Instead we will create our own custom environment names. We will have 4 environments: **Local**, **Dev**, **Test** and **Prod**. 

| Our Env Name | Default .Net Name | Settings File          | Description                                 |
| ------------ | ----------------- | ---------------------- | ------------------------------------------- |
| Local        | Development       | appsettings.Local.json | Local development environment               |
| Dev          |                   | appsettings.Dev.json   | Server developer testing environment        |
| Test         | Staging           | appsettings.Test.json  | Server user acceptance testing  environment |
| Prod         | Production        | appsettings.json       | Production environment / Final product      |

- Change existing environment name **Development** to our custom environment name **Local**
  - Rename the file **appsettings.Development.json** to **appsettings.Local.json**

        mv appsettings.Development.json appsettings.Local.json

  - Change environment name to **"Local"** inside **launchSettings.json** file by changing the environment variable line `"ASPNETCORE_ENVIRONMENT": "Development"` to `"ASPNETCORE_ENVIRONMENT": "Local"`

        sed -i 's/"ASPNETCORE_ENVIRONMENT": "Development"/"ASPNETCORE_ENVIRONMENT": "Local"/g' Properties/launchSettings.json

- Create appsettings.Dev.json file - for server developer testing environment

      touch appsettings.Dev.json

- Create appsettings.Test.json file - for user acceptance testing environment

      touch appsettings.Test.json

To ask .Net in the code what is the current environment, we can use method **IsEnvironment("Test")** available in **IHostEnvironment** interface. It takes environment name as a parameter and returns true if the current environment matches the environment name passed in.

Instead of passing a hardcoded string as the name of our environment we should create a constants file and store the environment names there. This way we can avoid typos and we can easily change the name of the environment if we need to. Also **you should never hardcode strings in your code. Always use constants.**

- Create file /Constants/Env.cs

      mkdir Constants && \
      touch Constants/Env.cs

- Open Env.cs file and add the following content

      namespace Server.Constants
      {
          public static class Env
          {
              public const string Local = "Local";
              public const string Dev = "Dev";
              public const string Test = "Test";
              public const string Prod = "Prod";

              public static bool IsLocal(IHostEnvironment env)
              {
                  return env.EnvironmentName == Local;
              }
              public static bool IsDev(IHostEnvironment env)
              {
                  return env.EnvironmentName == Dev;
              }
              public static bool IsTest(IHostEnvironment env)
              {
                  return env.EnvironmentName == Test;
              }
              public static bool IsProd(IHostEnvironment env)
              {
                  return env.EnvironmentName == Prod;
              }
          }
      }


- Now if we want to **check if the current environment is Local**, we can do it in one of 2 ways:

  - **Method 1** - Using **IsEnvironment()** method available in **IHostEnvironment** interface

        if (app.Environment.IsEnvironment(Environments.Local))
        {
          // do something
        }

      OR better

  - **Method 2** - Using **IsLocal()** method available in **Env** class that we created

        if (Env.IsLocal(app.Environment))
        {
          // do something
        }


- **Update Program.cs** - change the line that checks for **IsDevelopment()** to use our custom environment called "Local":

  Replace this line

      if (app.Environment.IsDevelopment())

  with this line

      if (Env.IsLocal(app.Environment))


### Configure Custom Urls


- Refs:
  - [URL Formats](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/endpoints?view=aspnetcore-8.0#url-formats)
  - [Kestrel Server](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel?view=aspnetcore-8.0)


By default when we publish our app using Release configuration, it will run on url http://localhost:5000/ 

**NOTE: We MUST change app url to set a different port for each serverside app we deploy to our server, because once we have one app running on one port, we can't start another app on the same port on the same server.**

Since we will deploy a separate instance of this Web Api app for each environment (Dev, Test and Prod) we need to set different port for each of those environments.

The easiest way to change Kestrel server url for the .Net app is inside appsettings files, by using **"Urls"** property. 

We will set different port for each environment by setting **"Urls"** property of appsettings file related to that environment.


- **Configure Dev Url** - Open **appsettings.Dev.json** file and add the following content

      {
        "Urls": "http://localhost:6379"
      }

  - We used number to spell **dEVP (6379)** to make it easier to remember the port number for Dev environment. You can use any port number you want as long as it is not used by any other app on your server and preferably in the range of 3000-9999.

- **Configure Test Url** - Open **appsettings.Test.json** file and add the following content

      {
        "Urls": "http://localhost:7327"
      }
  - We used number to spell **TEST (7327)** to make it easier to remember the port number for Test environment.

- **Configure Prod Url** - Open **appsettings.json** file and **add the Urls** field. It should look like this now:

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

  - We used numbers to spell **PROd (9406)** to make it easier to remember the port number for Prod environment.

- **Configure Local Url** - Because we set Urls field inside appsettings.json, ASP.Net will no longer read local url from **launchSettings.json** but instead, it will use the one from appsettings.json that we have set for prod environment. Therefore we need to add Urls field inside appsettings.Local.json and assign it a distinct url. We will use **"http://localhost:7067"**. We also need to supply the https url for local environment because in Program.cs we have configured https redirection for local environment. We will use **"https://localhost:7068"**. 

  - Add this line to appsettings.Local.json file
      
        "Urls": "https://localhost:7068,http://localhost:7067"
      
  - We again used a number to spell **LOCL (7067)** to make it easier to remember the port number for local environment.
  - **NOTE:** It is not really necessary to change local development port as we will not compile it in Release configuration and we will not deploy local build. However, we will change the port to word representation anyway for consistency and ease of remembering the port number.

Finally, we should now have the following Url configuration for each environment:

| Environment | Settings File Name     | Property Name | Url                                          | Host Location    |
| ----------- | ---------------------- | ------------- | -------------------------------------------- | ---------------- |
| Local       | appsettings.Local.json | Urls          | https://localhost:7068,http://localhost:7067 | local computer   |
| Dev         | appsettings.Dev.json   | Urls          | http://localhost:6379                        | dev.mysite1.com  |
| Test        | appsettings.Test.json  | Urls          | http://localhost:7327                        | test.mysite1.com |
| Prod        | appsettings.json       | Urls          | http://localhost:9406                        | mysite1.com      |

### Deployment Problems

**NOTE:** If you are not interested in the explanation of the problems and just want to see the solution code, you can skip to the section [Deployment Solution](#deployment-solution).

When we deploy our app to the server, we will face two problems:

Previously we have published our app using Release configuration without specifying the environment. This will publish the app using default settings.

We want to be able to produce a **separate build for each environment**. This way we can deploy a build for each environment to its specific location on the server. Also at the later stage we will be able to automate the deployment process.

Let's first try to publish Release build for Dev environment.

- Make sure you are inside project folder **src/Server**. If not, navigate to it using cd command 

      cd Server

- Publish **Release** build for **Dev** environment into folder **bin/Dev**

      dotnet publish -c Release -o bin/Dev -p:EnvironmentName=Dev

  - -c - means configuration
  - -o - means output folder
  - -p - means property

You should now see a new folder Dev inside the bin folder. 

Inside Dev folder you should see the published app files.

Notice **"web.config"** file. This file is used by IIS web server. We will not use IIS web server but we will use web.config at runtime to read the **environment name** from it dynamically. We need environment name in order to set app configuration to use correct appsettings file for the given environment.


- Open web.config file 
  - You should see environment variable property **"ASPNETCORE_ENVIRONMENT"** with a value of **"Dev"**

        <environmentVariables>
          <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Dev" />
        </environmentVariables>

  - This has been generated as a result of passing the property **EnvironmentName=Dev** in the publish command.

- Run the compiled **Release build for Dev environment** by running its Server.dll

      dotnet ./bin/Dev/Server.dll

  We are expecting the app to run on Dev port 6379 as we have configured it in appsettings.Dev.json file.

  We should see something like this in terminal right ?

      info: Microsoft.Hosting.Lifetime[14]
            Now listening on: http://localhost:6379

  But no. Instead we see this:
      
      info: Microsoft.Hosting.Lifetime[14]
            Now listening on: http://localhost:9406

  **The Dev build is running on Prod port 9406 . Why?**

  This happens because if ASPNETCORE_ENVIRONMENT environment variable is not set, .Net will only use default appsettings.json to load settings. We have configured `"Urls": "http://localhost:9406"` in appsettings.json file so that's the one that will be used. 
  
  **NOTE:** Microsoft requires that ASPNETCORE_ENVIRONMENT must be set in the computer's environment variables in order for .Net to use environment other than default Production in the Release build. Refer to [Microsoft - Aspnet Environments](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/environments?view=aspnetcore-8.0) for more info. 

#### PROBLEM 1: Getting Environment Name

If we want .Net to use correct appsettings file for the given environment, .Net expects us to set ASPNETCORE_ENVIRONMENT environment variable of the host machine to the name of the environment we want to use. 

Microsoft's solution is not acceptable for us because we want to run multiple instances of the same app on the same server. We can't set the environment variable to different value for each instance.

**Solution Description:**

To avoid above problem we will not set the environment variable on the host machine but instead we will read it at runtime from web.config and dynamically set the environment in WebApplicationBuilder when it is being first initialized. We will read the value of property **&lt;environmentVariable name="ASPNETCORE_ENVIRONMENT" value="{{CURRENT_ENVIRONMENT}}" /&gt;** from **web.config** file.

This will allow us to run as many environments as we want on the same server without having to set ASPNETCORE_ENVIRONMENT environment variable on the server.

We will demonstrate the implementation of this solution in the section [Implementing Solution](#implementing-solution). But before that let's discuss in the next section the second problem that we will face when we try to run the app on Linux server.

#### PROBLEM 2: Appsettings Files Not Found on Linux Server

Let's say we have fixed the problem with getting environment name in runtime and then we publish and deploy the app to the server. You will notice that it will again try to run on **default port 5000**. Why? 

This is because when code in Program.cs is run, it can't find the appsettings files and load the custom url defined in the property **"Urls"** of the appsettings file. 

**Solution Description:**

In order for .Net to find the appsettings files, we need to explicitly configure the WebApplicationBuilder to add configuration settings from appsettings files by using `builder.Configuration.AddJsonFile(PATH_TO_APPSETTINGS_FILE);` method. We need to do this before we call `builder.Build();` method.


### Deployment Solution

We will update Constants/Env.cs to do a lot more than just store constants. We will add methods to :
1. Read environment name from web.config file 
2. Add configuration settings from appsettings files into WebApplicationBuilder

Therefore we will move the Env.cs file out of Constants folder and into the root folder of the project. And we will delete the Constants folder.

- **Move Env.cs to root** folder (/Server)

      mv Constants/Env.cs Env.cs

- Delete Constants folder

      rm -rf Constants

- Update **Env.cs** file - replace the code with the following :

```csharp
/******************************************
 *  Author:         Vex Tatarevic
 *  Date Created:   2023-10-24
 ******************************************/

namespace Server
{
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
}
```

**NOTE:** Environment Name is read from web.config in the constructor of Env class. 

Next we need to update Program.cs to use the new Env class. We will initialize Env object and call its AddConfiguration method to add appsettings files for the current environment to WebApplicationBuilder. Also we will print details of the code execution as it happens so that we can better understand what is going on in the code when we start our app in terminal.


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



**NOTE:** We are using a dotted line to indicate what code was added to the Program.cs.

**IMPORTANT** : Please note that `app.UseHttpsRedirection();` line should only be called in **Local Development** environment. So we have wrapped it in **if (env.IsLocal)** condition

  - This is because our app will run on a Kestrel server which is running on localhost url 127.0.0.1 . The www url for the app (e.g. www.mysite.com) will access Nginx server which will proxy/redirect the call to our app running on Kestrel server's localhost url.
    
  - If UseHttpsRedirection is run on environment other than Local, this line will cause problem as it will be redirecting our app url to **https://** and that will return no response.

**NOTE:** We also wrapped Swagger code in **if (env.IsLocal)** condition. This is because we don't want Swagger page to be available on our server and we don't want to expose our API documentation to the public. We only want to use Swagger for local development and testing.




### Publish All Environments

Ref: https://learn.microsoft.com/en-us/dotnet/core/tools/dotnet-publish

Now let's publish all environments and run them to make sure they are running on correct ports that we have configured in appsettings files.

- Make sure you are inside project folder **src/Server**. If not, navigate to it using cd command 

      cd Server

- Publish **Release** build for **Dev environment** into folder **bin/Dev**

      dotnet publish -c Release -o bin/Dev -p:EnvironmentName=Dev

- Publish **Release** build for **Test environment** into folder **bin/Test**

      dotnet publish -c Release -o bin/Test -p:EnvironmentName=Test

- Publish **Release** build for **Prod environment** into folder **bin/Prod**

      dotnet publish -c Release -o bin/Prod -p:EnvironmentName=Prod


**NOTE:** We are not building for Linux specifically by using `-r linux-x64` or `--os linux` because that will not produce web.config file which is needed for our Program.cs to recognize what is the current environment. However the build will still work on Linux.



- Run the compiled **Release build for Dev environment** by running its Server.dll and verify that it is running on Dev port **6379**

      dotnet ./bin/Dev/Server.dll

- Run the compiled **Release build for Test environment** by running its Server.dll and verify that it is running on Test port **7327**

      dotnet ./bin/Test/Server.dll

- Run the compiled **Release build for Prod environment** by running its Server.dll and verify that it is running on Prod port **9406**

      dotnet ./bin/Prod/Server.dll


## 3. DEPLOY

### Get Remote Server IP

- On Server - get the IP : `hostname -I`

  - If your server is on the local network you will see LAN IP address e.g. 192.168.1.105 - note it down

  - If your server is outside of your LAN for example on the VPS hosting or cloud hosting, you will see three IP addresses 

        127.0.0.1 123.234.345.45 12ab:1234:12:cd56:0:0:0:7

    |                           |                      |
    | ------------------------- | -------------------- |
    | 127.0.0.1                 | **loopback** address |
    | 123.234.345.45            | **IPv4** address     |
    | 12ab:1234:12:cd56:0:0:0:7 | **IPv6** address     |

    - Note down the IPv4 address


### Option 1: Deploy With SFTP

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

      cd ~/dev/mysite1.com/src/Server

    OR with your project folder open in VS Code, open terminal : **CTRL + `**

- SFTP into your remote server machine 

      sftp mysite1.com

    OR if ssh connection is not stored in `~/.ssh/config`

      sftp admin@123.234.345.45

  - You should see a prompt like this: `sftp>`

- Copy local bin/Dev/ folder into remote host's server/ folder _(NOTE: server folder does not exist. It will be created when the command is run)_

      put -r ./bin/Dev/ /var/www/dev.mysite1.com/server

  - **-r** flag is used to copy directories recursively.

- Exit out of SFTP:  `exit`

Below is a longer way to deploy your files. It explains how to navigate between local and remote folders and how to copy files from local to remote host.

- SFTP to remote : `sftp mysite1`
- Navigate to **remote working directory** 

      cd /var/www/dev.mysite1.com/

- Print **remote working directory** : `pwd`

- Navigate to **local working directory** - we have to prefix commands to local working directory with `l` to indicate that we are working on local machine.

      lcd ~/dev/mysite1.com/src/Server

- Print **local working directory** : `lpwd`

- Switch to our local shell by typing `!` . Now you can use local shell commands without prefixing them with `l`.

- Switch back to SFTP shell by typing `exit`

- Copy files from local to remote 

      put -r ./bin/Dev ./server
    
- Exit SFTP

      exit


### Option 2: Deploy With Filezilla

- On Local PC - Install [Filezilla](https://filezilla-project.org/)
- Open Filezilla and connect to your Linux remote server using sftp. Type in connection details in the top connection bar as per bellow. 
  - NOTE: you have to put **sftp://** prefix before your server ip in Host field. 

    |          |                       |
    | -------- | --------------------- |
    | Host     | sftp://123.234.345.45 |
    | Username | admin                 |
    | Password | ******                |
    | Port     | 22                    |
    |          |                       |

  - Click **Quickconnect**    

- In the right panel - **Remote site** - navigate to your **Dev environment** website directory e.g. **/var/www/dev.mysite1.com/**

- Right-click on the folder and create a new folder called **server**. This is where we will deploy our files.

- Double-click on the **server** folder to open it

- In the left panel - **Local site** - navigate to  your **Dev environment** publish folder, for example:

    | OS      | Path to publish folder                            |
    | ------- | ------------------------------------------------- |
    | Windows | C:\Users\admin\dev\mysite1.com\src\Server\bin\Dev |
    | Linux   | /home/admin/dev/mysite1.com/src/Server/bin/Dev    |
    | Mac OS  | /Users/admin/dev/mysite1.com/src/Server/bin/Dev   |


- Select all the files on the left side
- Right-click on selected files > Upload

You should be able to repeat above instructions for publishing also **Test** and **Prod** environments to the server. You just have to change the word dev to test or prod in the path of source and target folders.

**NOTE:** For Prod environment our server path does not contain the word prod. It is just /var/www/mysite1.com/htdocs/ . This is because we want to deploy our Prod environment to the root of our domain.



## 4. Configure Site as Reverse Proxy

### Prerequisites

To proceed to next section you should have already completed course **Linux - Web Server - NGINX** and have already configured a simple static website with SSL certificate on Nginx.

### Configure Reverse Proxy

- Refs: 
  - [Cloudflare - Reverse Proxy](https://www.cloudflare.com/en-gb/learning/cdn/glossary/reverse-proxy/)
  - [Microsoft Configuring Nginx](https://learn.microsoft.com/en-us/aspnet/core/host-and-deploy/linux-nginx?view=aspnetcore-8.0#configure-nginx)
  - [Nginx Request Processing](https://nginx.org/en/docs/http/request_processing.html)


> **Kestrel** - is a lightweight, cross-platform, and open-source web server for ASP.NET Core

> **Reverse Proxy** - is a server that sits in front of web servers and forwards client requests (e.g. from browser) to those web servers. Reverse proxies are typically implemented to help increase security, performance, and reliability.

When deployed and started, our Web app will run on Kestrel server at the localhost. It will listen on the port that we have configured previously in appsettings files.
However this app will not be directly reachable from the internet. Instead we will configure our Nginx site to forward the requests to this Web app running on Kestrel - thereby acting as a reverse proxy.


- Connect to your remote server in terminal

      ssh my-server 

- Open site configuration file for your dev environment site

      sudo nano /etc/nginx/sites-available/dev.mysite1.com

- Replace the content with the one below. 

      server {
      
              # Listen for http requests
              listen          80;
              listen          [::]:80;
      
              # Listen for https requests
              listen          443 ssl;
              listen          [::]:443 ssl;
      
              root /var/www/dev.mysite1.com/htdocs;
      
              server_name dev.mysite1.com www.dev.mysite1.com;
      
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

 
  - We have set the proxy_pass url to same as our app url in appsettings.Dev.json file. This is because we want to forward all requests to our Web app running on Kestrel server at the localhost, port 6379.
  
- Save and close : **CTRL + S** > **CTRL + X**

- Verify config and reload nginx server

      sudo nginx -t && sudo nginx -s reload 

From now on all the requests to dev.mysite1.com will be forwarded to Kestrel server running on http://127.0.0.1:6379


## 5. RUN on Server

- Run the compiled **Release build for Dev environment** by running its Server.dll and verify that it is running on Dev port **6379**

      dotnet /var/www/dev.mysite1.com/server/Server.dll

- The app will run and you should see output like this:


      Starting app ...
      
      ---------------------------------------------
      => Initializing ...
      ---------------------------------------------
      .. Environment: Dev
      .. Adding configuration ...
      .. - Adding default settings from: /var/www/dev.mysite1.com/server/appsettings.json ...
      .. - Adding environment settings from: /var/www/dev.mysite1.com/htdocs/appsettings.Dev.json ...
      
      ----------------------------------
      => Adding services to dependency injection ...
      -----------------------------
      .. Adding controllers ...
      
      > BUILD
      
      -----------------------------------
      => Configuring the HTTP request pipeline ...
      -----------------------------
      .. UseAuthorization ...
      .. MapControllers ...
      
      > RUN
      
      nfo: Microsoft.Hosting.Lifetime[14]
           Now listening on: http://localhost:6379
      nfo: Microsoft.Hosting.Lifetime[0]
           Application started. Press Ctrl+C to shut down.
      nfo: Microsoft.Hosting.Lifetime[0]
           Hosting environment: Dev
      nfo: Microsoft.Hosting.Lifetime[0]
           Content root path: /home/admin


## 6. TEST on Client

We will now test our app on client computer (our local PC). 


### Test with Browser

- Open browser like Chrome or Edge

- Type url of your dev environment site in browser's address bar **dev.mysite1.com**, then hit **Enter**

  - You should get a response like this:

        This dev.mysite1.com page can’t be found
        No webpage was found for the web address: https://dev.mysite1.com/
        HTTP ERROR 404

        Reload

We get 404 error because this app is Web API app and so does not contain any clientside pages like index.html. Its purpose is to serve api endpoints. When root url of the site is resolved, it will try to find index.html page and since it does not exist, it will return 404 error.

We need to call the api endpoint of this app in order to test it.

The app only has one api endpoint which is **/weatherforecast** that is defined in **Controllers/WeatherForecastController.cs** file.

So we can test our app by calling the default endpoint of this controller.

- Call weatherforecast api endpoint by typing **dev.mysite1.com/weatherforecast** in browser's address bar , then hit **Enter**

  - You should get a response like this:

        [{"date":"2023-10-24T00:00:00+00:00","temperatureC":0,"temperatureF":32,"summary":"Freezing"},{"date":"2023-10-25T00:00:00+00:00","temperatureC":0,"temperatureF":32,"summary":"Bracing"},{"date":"2023-10-26T00:00:00+00:00","temperatureC":0,"temperatureF":32,"summary":"Freezing"},{"date":"2023-10-27T00:00:00+00:00","temperatureC":0,"temperatureF":32,"summary":"Freezing"},{"date":"2023-10-28T00:00:00+00:00","temperatureC":0,"temperatureF":32,"summary":"Freezing"}]


### Test with CURL


> CURL - Client URL Request Library - is command-line tool for transferring data using various protocols.

- Open Git bash. Run curl command to call watherforecast api endpoint of our api application

      curl -v http://dev.mysite1.com/weatherforecast

  - You should get a response like this

        *   Trying 123.234.345.45:80...
        * Connected to dev.mysite1.com (123.234.345.45) port 80 (#0)
        > GET /weatherforecast HTTP/1.1
        > Host: dev.mysite1.com
        > User-Agent: curl/7.81.0
        > Accept: */*
        >
        * Mark bundle as not supporting multiuse
        < HTTP/1.1 200 OK
        < Server: nginx/1.18.0 (Ubuntu)
        < Date: Fri, 28 Oct 2022 03:27:57 GMT
        < Content-Type: application/json; charset=utf-8
        < Transfer-Encoding: chunked
        < Connection: keep-alive
        <
        * Connection #0 to host dev.mysite1.com left intact
        [{"date":"2022-10-29T03:27:57.6027958+00:00","temperatureC":25,"temperatureF":76,       "summary":"Mild"},{"date":"2022-10-30T03:27:57.6030689+00:00","temperatureC":-2,    "temperatureF":29,"summary":"Bracing"},{"date":"2022-10-31T03:27:57.603072+00:00",   "temperatureC":34,"temperatureF":93,"summary":"Mild"},{"date":"2022-11-01T03:27:57.   6030722+00:00","temperatureC":-11,"temperatureF":13,"summary":"Freezing"},       {"date":"2022-11-02T03:27:57.6030724+00:00","temperatureC":-9,"temperatureF":16,       "summary":"Chilly"}]


 ## 7. Create Service - Background Process

- Ref: 
  - [Microsoft - Create the service file](https://learn.microsoft.com/en-us/aspnet/core/host-and-deploy/linux-nginx?view=aspnetcore-8.0#create-the-service-file)
  - [Linuxhandbook - Systemd Service](https://linuxhandbook.com/create-systemd-services/)

> In Linux, **service** is a "**background process**" that is started or stopped automatically based on certain circumstances/events.

> Services are located in **/etc/systemd/system/**

### Create Service

- Create a service that runs as a background process to monitor your site's Web API app and keep it alive.

- We will call the background service **web-api_{{SITE_DOMAIN}}.service** . Replace {{SITE_DOMAIN}} with your site's domain name e.g. **dev.mysite1.com**

- Create and open service file

      sudo nano /etc/systemd/system/web-api_{{SITE_DOMAIN}}.service

  - For our example dev.mysite1.com we should use command:

        sudo nano /etc/systemd/system/web-api_dev.mysite1.com.service


- Paste this code inside it

      [Unit]
      Description=.NET Web API App for site {{SITE_DOMAIN}}
      
      [Service]
      WorkingDirectory=/var/www/{{SITE_DOMAIN}}/htdocs
      ExecStart=/usr/bin/dotnet /var/www/{{SITE_DOMAIN}}/htdocs/{{APP_NAME}}.dll
      Restart=always
      # Restart service after 10 seconds if the dotnet service crashes:
      RestartSec=10
      KillSignal=SIGINT
      SyslogIdentifier=web-api_{{SITE_DOMAIN}}
      User={{USER}}
      Environment=ASPNETCORE_ENVIRONMENT={{ENVIRONMENT_NAME}}
      Environment=DOTNET_PRINT_TELEMETRY_MESSAGE=false
      
      [Install]
      WantedBy=multi-user.target

    - In above code replace placeholders with your own values. For example:

    | Placeholder          | Value           | Description                                                          |
    | -------------------- | --------------- | -------------------------------------------------------------------- |
    | {{SITE_DOMAIN}}      | dev.mysite1.com | Fully qualified domain name that points to your web site             |
    | {{USER}}             | admin           | User that has ownership access to site's files                       |
    | {{ENVIRONMENT_NAME}} | Dev             | ASP.NET Environment name                                             |
    | {{APP_NAME}}         | Server          | Name of the .Net app who's dll we want to execute on service startup |

 

- Save and close : **CTRL + S** > **CTRL + X**

- If user is other than root, give proper ownership of the app's files to owner

      sudo chmod 755 /var/www

- Enable the service

      sudo systemctl enable web-api_{{SITE_DOMAIN}}.service

- Start the service

      sudo systemctl start web-api_{{SITE_DOMAIN}}.service

- Verify that service is running

      sudo systemctl status web-api_{{SITE_DOMAIN}}.service

  - This should produce something like

        ● web_mysite1.com.service - .NET Web API App for site web_mysite1.com
             Loaded: loaded (/etc/systemd/system/web_mysite1.com.service; enabled;  vendor preset: enabled)
             Active: active (running)

- Make a request to web api to confirm service works - On your local PC open Git bash and run curl command to call watherforecast api endpoint of our api application

      curl -v http://dev.mysite1.com/weatherforecast

- Reboot the server

      sudo reboot

- Call the sample api url again to confirm that the service automatically started the app after reboot

      curl -v http://mysite1.com/weatherforecast

### List Services

- List all running services

  ```bash
  systemctl --type=service --state=running
  ```
  - You should see in the list the service we created earlier : web_mysite1.com.service

### Watch Live Log



- To watch the live log of what's happening on the server inside the web app while it is being called, use the following command:

  ```bash
  journalctl --follow -u web_mysite1.com.service
  ```
- To get out of live watch: hit `CTRL + C`


For more details about logging proceeed, to next section

### View Service Logs

Since the web app using Kestrel is managed using systemd, all events and processes are logged to a centralized journal. However, this journal includes all entries for all services and processes managed by systemd.

We can filter the journal by service name to view only the entries for our app:

    sudo journalctl -fu {{MY_SERVICE_NAME}}.service

We can also filter by time. You can use options such as --since {{TODAYS_DATE_DateString}}, --until {{1_HOUR_AGO_DateTimeString}}, or a combination of these can reduce the number of entries returned. For example

    sudo journalctl -fu {{MY_SERVICE_NAME}}.service --since "2016-10-18" --until "2016-10-18 04:00"

- View the journal entries for web-api_dev.mysite1.com.service

      sudo journalctl -fu web-api_dev.mysite1.com.service

  - To exit the journal: **CTRL + C**

- Show entries for the last 10 minutes

      sudo journalctl -fu web-api_dev.mysite1.com.service --since "10 minutes ago"

- Show entries for today

      sudo journalctl -fu web-api_dev.mysite1.com.service --since today

- Show entries for the last 1 hour

      sudo journalctl -fu web-api_dev.mysite1.com.service --since "1 hour ago"

- Show entries until 1 hour ago

      sudo journalctl -fu web-api_dev.mysite1.com.service --until "1 hour ago"

- Exit the journal

      CTRL + C


#
# >>>>> CLEAN UP <<<<<
#



#### Add Proxy Configuration to Web API

- Ref:  
  - [Microsoft - Proxy and load balancer config](https://learn.microsoft.com/en-us/aspnet/core/host-and-deploy/proxy-load-balancer?view=aspnetcore-8.0)
  - [Mozilla - Header - X-Forwarded-For](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Forwarded-For)
  - [Mozilla - Header - X-Forwarded-Proto](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Forwarded-Proto)


When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.

Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.

ForwardedHeadersMiddleware, reads these headers and fills in the associated fields on HttpContext.

The forwarded headers are named X-Forwarded-For and X-Forwarded-Proto.

> The **X-Forwarded-For (XFF)** request header is a de-facto standard header for identifying the originating **IP address** of a client connecting to a web server through a proxy server.

> The **X-Forwarded-Proto (XFP)** header is a de-facto standard header for identifying the **protocol (HTTP or HTTPS)** that a client used to connect to your proxy or load balancer.


- Open **Program.cs** file
- Add `builder.Services.Configure<ForwardedHeadersOptions>` code-block before calling `builder.Build()` method


        Console.WriteLine($"... Adding header forwarding ...");
        builder.Services.Configure<ForwardedHeadersOptions>(options =>
        {
            options.ForwardedHeaders =
                ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
        });
        /*
          Header forwarding is required if we are using Nginx web server as a reverse proxy to forward requests to .Net Web App.
          An app receives a request from the proxy and not its true source on the Internet or corporate network.
          So the originating client IP address must also be forwarded in a header.
          - XForwardedFor (XFF) - forwards IP
          - XForwardedProto (XFP) - forwards protocol (HTTP or HTTPS)
        */
        //...............................................................

        Console.WriteLine("\n > BUILD");
        var app = builder.Build();

- Add `app.UseForwardedHeaders();` as a first line inside "Configure the HTTP request pipeline" code section:

        // Configure the HTTP request pipeline.
        Console.Write("\n ----------------------------------- \n => Configuring the HTTP request pipeline ... \n ----------------------------- \n");

        //............... Added Header Forwarding .......................
        Console.WriteLine($"... UseForwardedHeaders ...");
        app.UseForwardedHeaders();
        //...............................................................

- Publish and Deploy to server

      

### 4. Run Web App
 
 - Connect to server in terminal if you haven't already

       ssh my-server

 - Run the Web API app we have published to server by executing a dotnet command on published Web.dll

       dotnet /var/www/mysite1.com/htdocs/Web.dll

### 5. Call API App with CURL

> CURL - Client URL Request Library - is command-line tool for transferring data using various protocols.

- Open Git bash. Run curl command to call watherforecast api endpoint of our api application

      curl -v http://mysite1.com/weatherforecast

  - You should get a response like this

        *   Trying 123.234.345.45:80...
        * Connected to mysite1.com (123.234.345.45) port 80 (#0)
        > GET /weatherforecast HTTP/1.1
        > Host: mysite1.com
        > User-Agent: curl/7.81.0
        > Accept: */*
        >
        * Mark bundle as not supporting multiuse
        < HTTP/1.1 200 OK
        < Server: nginx/1.18.0 (Ubuntu)
        < Date: Fri, 28 Oct 2022 03:27:57 GMT
        < Content-Type: application/json; charset=utf-8
        < Transfer-Encoding: chunked
        < Connection: keep-alive
        <
        * Connection #0 to host mysite1.com left intact
        [{"date":"2022-10-29T03:27:57.6027958+00:00","temperatureC":25,"temperatureF":76,       "summary":"Mild"},{"date":"2022-10-30T03:27:57.6030689+00:00","temperatureC":-2,    "temperatureF":29,"summary":"Bracing"},{"date":"2022-10-31T03:27:57.603072+00:00",   "temperatureC":34,"temperatureF":93,"summary":"Mild"},{"date":"2022-11-01T03:27:57.   6030722+00:00","temperatureC":-11,"temperatureF":13,"summary":"Freezing"},       {"date":"2022-11-02T03:27:57.6030724+00:00","temperatureC":-9,"temperatureF":16,       "summary":"Chilly"}]

- To exit curl **CTRL + C**

### 5.1 Call API App in Browser

- Paste the url **mysite1.com/weatherforecast** into any browser address bar and the browser should return the weatherforecast response

        [{"date":"2022-10-29T03:29:16.9089948+00:00","temperatureC":12,"temperatureF":53,       "summary":"Freezing"},{"date":"2022-10-30T03:29:16.9090207+00:00",  "temperatureC":36,"temperatureF":96,"summary":"Sweltering"},     {"date":"2022-10-31T03:29:16.9090212+00:00","temperatureC":29,"temperatureF":84,    "summary":"Mild"},{"date":"2022-11-01T03:29:16.9090214+00:00","temperatureC":12,   "temperatureF":53,"summary":"Warm"},{"date":"2022-11-02T03:29:16.9090216+00:00",  "temperatureC":42,"temperatureF":107,"summary":"Bracing"}]

 ### 7. Secure the App

#### Install AppArmor

>>>>> TODO: Fix bad installation

Refs: 
- [Ubuntu Wiki](https://wiki.ubuntu.com/AppArmor)
- https://unixcop.com/how-to-setup-apparmor-on-ubuntu-20-04-22-04-lts/

> Linux Security Modules (LSM) is a framework that's part of the Linux kernel.
LSM implements a Mandatory Access Control system, which allows confining the program to a limited set of resources. 

- Ensure AppArmor is enabled and properly configured.

      sudo apparmor_status

- If you get `sudo: apparmor_status: command not found` then install apparmor

- Install **apparmor**

      sudo apt update -y && sudo apt upgrade -y

      sudo apt install apparmor-notify apparmor-utils apparmor-profiles-extra apparmor-profiles -y

- Confirm that apparmor is installed

      sudo systemctl status apparmor

#### Configure the firewall

Close off all external ports that aren't in use. Uncomplicated firewall (ufw) provides a front end for iptables by providing a CLI for configuring the firewall. Refer to [Configure Firewall](#configure-firewall).

- Example minimum implementation

      sudo ufw allow 22/tcp
      sudo ufw allow 80/tcp
      sudo ufw allow 443/tcp

#### Secure Nginx from clickjacking

> Clickjacking, also known as a UI redress attack, is a malicious attack where a website visitor is tricked into clicking a link or button on a different page than they're currently visiting. 

Use X-FRAME-OPTIONS to secure the site.

- Edit the nginx.conf file

      sudo nano /etc/nginx/nginx.conf

- Add this line to http section

        # Security - Prevent Clickjacking
        add_header X-Frame-Options SAMEORIGIN always;

- Restart Nginx

      sudo systemctl restart nginx


- Verify X-Frame-Options header is propagated.
    - Open Chrome browser > Hit **F12** to open Developer Tools > In Developer Tools windows click on **Network** tab
    - In your browser address bar, type in your server IP and default port 80 to navigate to your default page e.g. **123.234.345.45:80**
    - In network list click on default page named same as your IP e.g. 123.234.345.45
    - Click on Headers tab
    - You should see **Response Headers** section that should contain **X-Frame-Options: SAMEORIGIN** like this

          Content-Encoding: gzip
          Content-Type: text/html
          Date: Tue, 01 Nov 2022 00:03:07 GMT
          ETag: W/"634e39ae-264"
          Last-Modified: Tue, 18 Oct 2022 05:29:18 GMT
          Server: nginx/1.18.0 (Ubuntu)
          X-Frame-Options: SAMEORIGIN

### 8. Configure HTTPS

- Ref: 
  - [Microsoft - Linux Nginx - HTTPS Config](https://learn.microsoft.com/en-us/aspnet/core/host-and-deploy/linux-nginx?view=aspnetcore-8.0#https-configuration)
  - [Mozilla - SSL Config Generator](https://ssl-config.mozilla.org/#server=nginx)
  - [How to use Cloudflare SSL Origin Certificates with Nginx](https://kb.virtubox.net/knowledgebase/cloudflare-ssl-origin-certificates-nginx/)
  - [Developers Cloudflare - Origin CA Certificate](https://developers.cloudflare.com/ssl/origin-configuration/origin-ca)
  - [Nginx - Configuring HTTPS servers](https://nginx.org/en/docs/http/configuring_https_servers.html)
  - [Nginx - http upstream Module ](https://nginx.org/en/docs/http/ngx_http_upstream_module.html)


#### 8.1. Point Domain to Cloudflare

- We will use Cloudflare as a proxy for a completely **FREE** of charge SSL/HTTPS implementation. Refer to this: [Use Cloudflare.com as a domain name proxy](#8-use-cloudflarecom-as-proxy-to-point-domain-name)

#### 8.2. Create Cloudflare SSL Origin Certificate

We will use Origin Certificate Authority (CA) certificates to encrypt traffic between Cloudflare and your origin web server and reduce origin bandwidth consumption. 

 - Go to [Cloudflare Dashboard](https://dash.cloudflare.com/)
 - Click on your domain name e.g. mysite1.com
 - On left side menu > SSL/TLS > Origin Server
 - Click button **Create Certificate** > On the next page, leave all thedefaults > Click **Create**
 - You should now see **Origin Certificate** and **Private Key** textboxes. Copy both Certificate and Private Key text into a Notpad and save it to your Desktop temporarily

#### 8.3. Install SSL Certificate on Server

Default location on Linux for certificates is **/etc/ssl/certs** , but we will not use this location. Instead we will place it in the root of our website inside new directory ssl/

- Create directory ssl inside your site

      mkdir /var/www/{{SITE_DOMAIN}}/ssl

- Create **.crt** file inside **ssl/** directory in the root of your site directory and paste into it certificate text including the BEGIN and END lines

      nano /var/www/{{SITE_DOMAIN}}/ssl/{{SITE_DOMAIN}}.crt

- Create **.key** file inside **ssl/** directory in the root of your site directory and paste into it certificate text including the BEGIN and END lines

      nano /var/www/{{SITE_DOMAIN}}/ssl/{{SITE_DOMAIN}}.key

#### 8.4. Update Site Config

- Edit site config 

      sudo nano /etc/nginx/sites-available/{{SITE_DOMAIN}}

- Paste the following configuration. **MAKE SURE TO REPLACE** {{SITE_DOMAIN}} with your site domain e.g. mysite1.com.

      # Ref: https://learn.microsoft.com/en-us/aspnet/core/host-and-deploy/linux-nginx?view=aspnetcore-8.0#https-configuration
      
      # http {
      
          #--------------------------
          # Proxy Configuration
      
          proxy_redirect          off;
          proxy_set_header        Host $host;
          proxy_set_header        X-Real-IP $remote_addr;
          proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
          proxy_set_header        X-Forwarded-Proto $scheme;
          client_max_body_size    10m;
          client_body_buffer_size 128k;
          proxy_connect_timeout   90;
          proxy_send_timeout      90;
          proxy_read_timeout      90;
          proxy_buffers           32 4k;
      
          #--------------------------
      
      
          limit_req_zone $binary_remote_addr zone=one:10m rate=5r/s;
          server_tokens  off;
      
          # sendfile on; # This setting should already be inside nginx.conf. Uncomment if it's not in nginx.conf.
      
          # Adjust keepalive_timeout to the lowest possible value that makes sense for your use case.
          keepalive_timeout   29;
          client_body_timeout 10; client_header_timeout 10; send_timeout 10;
      
          # local .Net app runnning on Kestrel server
          upstream {{SITE_DOMAIN}}_local{
              server 127.0.0.1:5000;
          }
      
          server {
              listen                    443 ssl http2;
              listen                    [::]:443 ssl http2;
      
              server_name               {{SITE_DOMAIN}} *.{{SITE_DOMAIN}};
      
              ssl_certificate           /var/www/{{SITE_DOMAIN}}/ssl/{{SITE_DOMAIN}}.crt;
              ssl_certificate_key       /var/www/{{SITE_DOMAIN}}/ssl/{{SITE_DOMAIN}}.key;
              ssl_session_timeout       1d;
              ssl_protocols             TLSv1.2 TLSv1.3;
              ssl_prefer_server_ciphers off;
              ssl_ciphers               ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384;
              ssl_session_cache         shared:SSL:10m;
              ssl_session_tickets       off;
              ssl_stapling              off;
              
      
              add_header X-Frame-Options DENY;
              add_header X-Content-Type-Options nosniff;
      
              # Redirects all traffic        
              location / {
                  proxy_pass http://{{SITE_DOMAIN}}_local;
                  limit_req  zone=one burst=10 nodelay;
              }
          }
      
      # }


- Check syntax and Reload Nginx

      sudo nginx -t && service nginx reload




#### 8.5. Configure Cloudflare SSL 

Your Cloudflare origin certificate is now installed on your server, so you can change the SSL settings to “Full (strict)” in your Cloudflare dashboard.

- Configure Cloudflare to use Full SSL mode : SSL/TLS > Overview > Set encrypton mode to **Full (strict)**

- Configure Cloudflare to always redirect to HTTPS : SSL/TLS > Edge Certificates > Always Use HTTPS
 
- Now when you type in your app url in the browser e.g. mysite1.com, the browser will show a secure SSL url for that domain e.g. https://mysite1.com




## Deploy Clientside App - Angular SPA

### On Server - Install Dependencies

- SSH into your remote Linux server

#### 1. Install Node.js

- Ref: 
  - [Node.js Home](https://nodejs.org) > [Package Manager Downloads](https://nodejs.org/en/download/package-manager/) > [Debian/Ubuntu Linux Distributions](https://nodejs.org/en/download/package-manager/#debian-and-ubuntu-based-linux-distributions) > [Latest Linux Distributions Installation](https://github.com/nodesource/distributions/blob/master/README.md)


-  Install Node.js  - easiest way is to call apt install command

        sudo apt update  \
        && sudo apt upgrade \
        && sudo apt install nodejs

- Confirm Node.js version

      node -v

- Fixing bad installation - if you get error **Errors were encountered while processing** while trying to install, you need to first uninstall completely nodejs

      sudo apt-get remove nodejs

  - Then try steps in this [troubleshooting](#errors-were-encountered-while-processing)

#### 2. Install NPM

- Check if NPM - Node Package Manager has been installed with Node.js

      npm -v

  - If this shows version number it means NPM is installed , so you can skip installing it

- If not installed - Install Node package manager NPM

      sudo apt install npm
      

#### 3. Install Angular

- Install Angular CLI

      sudo npm install -g @angular/cli

- Verify Angular version

      ng v

### On Local PC - Create Angular App

- On your local computer create Angular app

      ng new ClientApp --style=scss --routing

    - **--style=scss** changes default css file to scss which is a better styling language
    - **--routing**  creates a default routing module app-routing.module.ts

- Open the ClientApp with [Visual Studio Code](https://code.visualstudio.com/) : 
  - Right-Click ClientApp folder > Open with Code
- Open terminal inside VS Code : **CTRL + `**
- Run the app :

      ng serve -o

  - This will open the app in your browser
- Build the app for Production

      ng build

  - This will publish all required files into a **/dist/client-app/** directory in the root of ClientApp/
  - Angular app provides ability to have multipple projects in one application and that's why by default it creates a separate sub-directory for the main app inside the dist/ directory

> dist/ directory is the standard directory in Nodejs-based applications, where the code for deployment is published. Dist is short for distributable.
  
- Change OutputPath - Let's publish all the deployable code directly into dist directory
  - Delete **dist** directory
  - Open **angular.json** 
  - Search for **"outputPath": "dist/client-app"**
  - Remove "/client-app" from the output path
  - Run **ng build**
  - This time all the code will be published directly into **dist/** directory

### Deploy from Local to Server
- On the Server you should have an existing site that you have set up in  [Create a Site on Nginx](#4-create-a-site-on-nginx) section of this course, e.g. mysite1.com
- Open Filezilla
- Connect to Server in Quickconnect bar at the top.
- In the left **Local site** panel, navigate to ClientApp angular app we have created, inside dist directory e.g. c:/dev/ClientApp/dist/
- In the right **Remote site** panel naigate to mysite1.com we have created on server e.g. /var/www/mysite1.com/htdocs/
- Copy file from local dist/ directory into server htdocs/ directory
- Navigate to domain name of your website in the browser e.g. mysite1.com
- You should see now the published default Angular app inside the browser at your domain name


## Deploy Full-Stack App - .Net & Angular


### Create Full Stack Web App

Ref: [Full Stack Web App](../../Software_Development/Web/WebFullStackApp_Bootstrap_Angular_NetCore_EF_PostgreSQL.md)


### Add Proxy Config inside App

Ref: [Add Proxy Config](#add-proxy-configuration-to-web-api)

### Deploy Full Stack Web App as a single unit

- Repeat all the steps as in [Deploy Serveside App](#deploy-serverside-app---net-web-api)

### Update Site config file - change location setting

- Open site config file e.g. mysite1.com

      sudo nano /etc/nginx/sites-available/mysite1.com

- Change location routing
  - **location /** - root should point to index file in wwwroot where Angular app is located
  - **location /api/** - should be added that points to web api app

- If your domain name is **mysite.com** then the whole code should look like this

      # Ref: https://learn.microsoft.com/en-us/aspnet/core/host-and-deploy/linux-nginx?view=aspnetcore-8.0#https-configuration
      
      # http {
      
          #--------------------------
          # Proxy Configuration
      
          proxy_redirect          off;
          proxy_set_header        Host $host;
          proxy_set_header        X-Real-IP $remote_addr;
          proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
          proxy_set_header        X-Forwarded-Proto $scheme;
          client_max_body_size    10m;
          client_body_buffer_size 128k;
          proxy_connect_timeout   90;
          proxy_send_timeout      90;
          proxy_read_timeout      90;
          proxy_buffers           32 4k;
      
          #--------------------------
      
      
          limit_req_zone $binary_remote_addr zone=one:10m rate=5r/s;
          server_tokens  off;
      
          # sendfile on; # This setting should already be inside nginx.conf. Uncomment if it's not in nginx.conf.
      
          # Adjust keepalive_timeout to the lowest possible value that makes sense for your use case.
          keepalive_timeout   29;
          client_body_timeout 10; client_header_timeout 10; send_timeout 10;
      
          # local .Net Web API app runnning on Kestrel server
          upstream mysite_api{
              server 127.0.0.1:5000;
          }
      
          server {

              listen                    443 ssl http2;
              listen                    [::]:443 ssl http2;
      
              server_name               mysite.com *.mysite.com;
      
              ssl_certificate           /var/www/mysite.com/ssl/mysite.com.crt;
              ssl_certificate_key       /var/www/mysite.com/ssl/mysite.com.key;
              ssl_session_timeout       1d;
              ssl_protocols             TLSv1.2 TLSv1.3;
              ssl_prefer_server_ciphers off;
              ssl_ciphers                   ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHAC    HA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384;
              ssl_session_cache         shared:SSL:10m;
              ssl_session_tickets       off;
              ssl_stapling              off;
      
      
              add_header X-Frame-Options DENY;
              add_header X-Content-Type-Options nosniff;
      
              # Clientside app
              location / {
                      root /var/www/mysite.com/htdocs/wwwroot/;
                      index index.html;
                      # First attempt to serve request as file, then
                      # as directory, then fall back to displaying a 404.
                      try_files $uri $uri/ =404;
              }
      
              # Serverside app - Redirect api traffic to API app
              location /api/ {
                      proxy_pass http://mysite_api;
                      limit_req  zone=one burst=10 nodelay;
              }
          }
      
      # }


### Reload NGINX

- Reload Nginx service

      sudo nginx -s reload
 

 ### Publish App

- Publish app locally - in Visual Studio open terminal **CTRL + `**
- CD into Web project

      cd Web

- Run publish command

      dotnet publish -c Release

  - This will publish into /Server/bin/Release/net8.0/publish/

- Publish to server
  - Open Filezilla
  - Transfer files from your local **"publish/"** folder **C:/dev/MySite/Web/bin/Release/net8.0/publish/** onto remote server's **"htdocs/"** folder **/var/www/mysite.com/htdocs/**

**>>> IMPORTANT** - PLEASE NOTE: You need to upload the files from inside the **"publish/"** directory - and NOT from it's parent directory **"net8.0"**

After your file-upload has finished, do the following on your Linux server:

- Restart web app service

      sudo systemctl restart web_{{SITE_DOMAIN}}.service

- Reload Nginx web server

      sudo nginx -s reload
