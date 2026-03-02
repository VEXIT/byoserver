# Linux - Cron Jobs


|            |                                                 |
| ---------- | ----------------------------------------------- |
| Copyright: | © 2023 VEXIT ,Tomorow is today ®, www.vexit.com |
| Author:    | Vex Tatarevic                                   |
| Date:      | 2023-11-28                                      |
|            |                                                 |



- Refs:
  - [Wikipedia - cron](https://en.wikipedia.org/wiki/Cron)
  - [Phoenix Nap - How to Set Up a Cron Job in Linux](https://phoenixnap.com/kb/set-up-cron-job-linux)
  - [Redhat - How to schedule jobs using the Linux 'cron' utility](https://www.redhat.com/sysadmin/linux-cron-command)
  - [Hostinger - What Is a Cron Job: Understanding Cron Syntax and How to Configure Cron Jobs ](https://www.hostinger.com/tutorials/cron-job)
  - [Linuxopsys - Cron Cheatsheet](https://linuxopsys.com/topics/cron-cheatsheet-quick-reference-guide)


## What is Cron?

> **Cron** - is a time-based job scheduler program. It is useful for automation of system maintenance and administration, downloading files from the internet at regular intervals and similar repetitive tasks. The name cron comes from the Greek word for time, χρόνος (chronos).

> **Crontab (CRON TABle)** - is a file which contains the schedule of cron entries to be run and at specified times. This can be used to schedule cron jobs.
 
> **crond (Cron Daemon)** - is a background process that executes cron jobs at specified intervals.

> **Cron Job** or **Cron Task** - is a line of text in crontab file. It contains either a direct command or a file-path to a shell script file which is to be executed at a specified time interval. The time interval is defined by using 5 fields separated by spaces. The fields are: minute, hour, day of month, month, day of week. The cron job is executed when the current time matches the time defined in the cron job.

We will further explain the cron job syntax in the section [Cron Job Syntax](#cron-job-syntax).

Here is an example of two lines in a crontab file (2 cron jobs/tasks):

```bash
1 0 * * * printf "" > /var/log/apache/error_log
45 23 * * 6 /home/oracle/scripts/export_dump.sh
```


There are 2 types of crontab files: system-wide and user-specific. 

**System-wide crontab** file is located at **/etc/crontab** 

**User-specific crontab** file is located at **/var/spool/cron/crontabs/crontab/{{USERNAME}}**. You should NOT edit your user crontab directly. We will learn how to do this properly in the next section.



## Crontab Management

### Edit Crontab


- **Edit crontab for current user** - this will create a new crontab file for the **current user** if it does not exist:

      crontab -e

  - You will be prompted to select an editor. Select nano by typing the number next to it and pressing Enter.
  - The crontab file will open in the editor you selected.

  - Copy and paste, at the bottom of the file, the following explanation of basic cron syntax: 

    ```bash
    # Example of job (task) definition:
    #
    # .---------------- minute (0 - 59)
    # |  .------------- hour (0 - 23)
    # |  |  .---------- day of month (1 - 31)
    # |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
    # |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
    # |  |  |  |  |
    # *  *  *  *  * <command to be executed>
    ```

  - Save and exit: **CTRL+S, CTRL+X**

  - List files in the crontab directory:

        sudo ls -l /var/spool/cron/crontabs

  - You should see now that a new file has been created and it has same name as your username e.g. admin

- **Edit crontab for another user** - this will create a new crontab file for the **specified user** if it does not exist:

      crontab -u {{USERNAME}} -e

Generally we want to edit crontab for root user for the tasks that need root permissions to be executed. To do this we use sudo:

- **Edit crontab for the root user**:

      sudo crontab -u root -e


### Read Crontab

- List crontab files for all users:

      sudo ls -l /var/spool/cron/crontabs


- List cron jobs for the current user:

      crontab -l

  - This will show contents of your crontab file. 

- List cron jobs for another user

      crontab -u {{USERNAME}} -l

- List cron jobs for the root user:

      sudo crontab -u root -l

### Delete Crontab      

- Delete crontab for the current user:

      crontab -r
    
  - List files in the crontab directory:

        sudo ls -l /var/spool/cron/crontabs

  - You will see that the file for your user has been deleted.


## Cron Job Structure

Cron Job (the line of text in crontab file) has the following structure:

```bash
* * * * * {{command}}
```

- **5 stars**  - represent five time-fields in the cron job. The fields are separated by spaces. Star sign (*) is a legitimate value for each field but there are many other possible values. We will explain them in the next sections.

- **{{command}}** - is a place holder that can be replaced by either a direct command written in **Bash** (shell scripting language) e.g. `printf "" > /var/log/apache/error_log` or a file-path to a shell script file to execute e.g. `/home/oracle/scripts/export_dump.sh`.

## Cron Job Infographic

The following infographic shows the structure of a cron job and is commented out with Bash script comments. Therefore you can paste it into your crontab file to serve as a reminder of the basic cron job syntax.

```bash
# Example of job (task) definition:
#
# .---------------- minute .........(0 - 59)
# |  
# |  .------------- hour ...........(0 - 23)
# |  |  
# |  |  .---------- day of month ...(1 - 31)
# |  |  |  
# |  |  |  .------- month ..........(1 - 12) 
# |  |  |  |                        OR 
# |  |  |  |                        jan,feb,mar,apr ...
# |  |  |  |
# |  |  |  |  .---- day of week ....(0 - 6) (Sunday=0 or 7) 
# |  |  |  |  |                     OR 
# |  |  |  |  |                     sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  * <command to be executed>
```
- Open your crontab file for editing:

      crontab -e

- Paste the above infographic at the bottom of the file.
- Save and exit: **CTRL+S, CTRL+X**



## Cron Job Syntax

- Cron job syntax consists of 5 fields separated by spaces in the following order:
    1. Minute
    2. Hour
    3. Day of Month
    4. Month
    5. Day of Week

- Each field can contain any of the following type of values:

    | Value <br> Type                                                                                 | Special <br> Character <br> Used | Example | Description                                                                                                                                                                                                                                                                                  |
    | ----------------------------------------------------------------------------------------------- | -------------------------------- | ------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
    | Number                                                                                          |                                  | 5       | 5 mins, if in field 1 <br> 5 AM, if in field 2 <br> 5th of the month, if in field 3 <br> ... and so on ...                                                                                                                                                                                   |
    | Range                                                                                           | - (dash)                         | 1-5     | From 1AM to 5AM, if in field 2 <br> From January to May, if in field 4 <br> From Monday to Friday, if in field 5 <br> ... and so on ...                                                                                                                                                      |
    | List                                                                                            | , (comma)                        | 1,5,10  | Every 1st, 5th and 10th of the month, if in field 3 <br> Every January, May and October , if in field 4                                                                                                                                                                                      |
    | All <br> possible <br> values                                                                   | * (asterisk)                     | *       | Every minute, if in field 1 <br> Every hour, if in field 2 <br> ... and so on ...                                                                                                                                                                                                            |
    | Step                                                                                            | / (slash)                        | */5     | Every 5 minutes, if in field 1 <br> Every 5 hours, if in field 2 <br> ... and so on ... <br> <br> Other examples: <br> 5/15 in the seconds field means "the seconds 5, 20, 35, and 50" <br> 1/3 in the day-of-month field means "Every 3 days starting on the first day of the month"        |
    | No specific value <br><br> *Applies ONLY to : field 3. day-of-month, field 5. day-of-week*      | ? (question mark)                | ?       | For example, if I want the job to be executed on the 10th of every month, but don’t care what day of the week that happens to be, I would put "10" in the day-of-month field, and "?" in the day-of-week field: **"0 0 10 * ?"**                                                             |
    | Last day of month/week <br><br> *Applies ONLY to : field 3. day-of-month, field 5. day-of-week* | L                                | 2L      | Last Tuesday of specified month, if in field 5 (day-of-the-week) <br> <br> Other examples: <br> L by itself in the day-of-month field means last day of every month e.g. **"0 0 L * ?"** <br> L by itself in the day-of-the-week means last Saturday of specified month e.g. **"0 0 ? * L"** |
    | Nth day of the week in a month <br><br> *Applies ONLY to : field 3. day-of-month*               | # (hash)                         | 4#2     | Every second Thursday of the specified month <br> <br> Other examples: <br> **"15 0 * * 5#3"** - Every third Friday of every month at 00:15AM                                                                                                                                                |


- The following special strings can be used instead of the 5 fields:

    | String        | Meaning              | Equivalent to |
    | ------------- | -------------------- | ------------- |
    | **@reboot**   | run once, at startup |               |
    | **@yearly**   | run once a year      | 0 0 1 1 *     |
    | **@annually** | same as @yearly      |               |
    | **@monthly**  | run once a month     | 0 0 1 * *     |
    | **@weekly**   | run once a week      | 0 0 * * 0     |
    | **@daily**    | run once a day       | 0 0 * * *     |
    | **@midnight** | same as @daily       |               |
    | **@hourly**   | run once an hour     | 0 * * * *     |


## Cron Job Examples and Generator

- Run a command every Sunday at midnight: 

      0 0 * * 0 <command>

- Run a command every 1st of the month at midnight:

      0 0 1 * * <command>

- Run a command every 1st of January at midnight: 

      0 0 1 1 * <command>

- Run a command every 10 minutes on weekdays (Monday to Friday):

      */10 * * * 1-5 <command>

- Run a command every 10 minutes on weekends (Saturday and Sunday):

      */10 * * * 6,7 <command>

- Run a command every 10 minutes on weekdays (Monday to Friday) between 9am and 5pm:

      */10 9-17 * * 1-5 <command>

- Perform a system backup every hour:

      0 * * * * /opt/scripts/backup.sh
  
- Check if the remote server is online every 7 minutes:

      */7 * * * * /opt/scripts/ping.sh

- Empty trash every 6 hours: 

      0 */6 * * * /opt/scripts/emptytrash.sh

- Upgrade the system every day at midnight: 

      0 0 * * * /opt/scripts/upgrade.sh

- Clear system temporary files at 11:06 AM every Wednesday:

      6 11 * * 3 /opt/scripts/clear_temp.sh

- Monitor the system at 08:10 AM on the last Thursday of every month:

      10 8 * * 4L /opt/scripts/monitor.sh

- Run system maintenance at 00:15 AM on the second Thursday of every month:

      15 0 * * 4#2 /opt/scripts/maintenance.sh

- Perform a system backup at reboot:

      @reboot /opt/scripts/backup.sh

- Renew SSL certificates weekly

      @weekly /opt/scripts/renewSSLCerts.sh

- Download database backup daily by calling dotnet core app

      @daily /usr/bin/dotnet /opt/apps/backup/BackupApp.dll


You can generate cron jobs on these websites:
- [Crontab Generator](https://crontab-generator.org/)
- [Crontab Guru](https://crontab.guru/)





## Exercise - Build a Scheduled File Downloader

In this exercide we will build an app that calls an api point to download a file from server and save it to a local computer's folder. The app will be executed in regular intervals by a cron job.


### Part 1 - Create Server App - Web Api for File Download 

We will create a Web API app which will serve as a file download service on the server from which we want to download files.

- Open a terminal window

- Create project folder inside your user folder. We will call our project folder **file-downloader**

      mkdir -p ~/dev/file-downloader

  - **-p** flag is used to create parent directories if they do not exist. In this case it will create folder **dev** if it does not exist.
 
- Go inside project folder

      cd ~/dev/file-downloader


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

We will now add a new controller called **DownloadController** which will serve as a file download service.

- In VS Codium, in the terminal window, run the following command:

      dotnet new apicontroller -o Controllers -n DownloadController

  - **-o** - means output folder path/name
  - **-n** - means name of the controller class

This should create a new file **DownloadController.cs** in the **Controllers** folder with a following content:

```csharp
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;

namespace MyApp.Namespace
{
    [Route("api/[controller]")]
    [ApiController]
    public class DownloadController : ControllerBase
    {
        
    }
}
```

- Replace **MyApp.Namespace** with **Server.Controllers**

We will add the code that downloads a file **_files/data.json**.

- Replace the code inside DownloadController.cs with the following code:

```csharp
using System.Reflection;
using Microsoft.AspNetCore.Mvc;

namespace Server.Controllers
{
    [ApiController]
    public class DownloadController : ControllerBase
    {
        //-----------------------------------------------------------------------
        //      Fields
        //-----------------------------------------------------------------------

        private readonly IWebHostEnvironment _hostingEnvironment;

        private const string _containerDirName = "_files";
        private const string _defaultFileName = "data.json";
        private string _baseDirPath = "";

        //-----------------------------------------------------------------------
        //      Constructor
        //-----------------------------------------------------------------------


        public DownloadController(IWebHostEnvironment hostingEnvironment)
        {
            _hostingEnvironment = hostingEnvironment;
            var webRootPath = _hostingEnvironment.WebRootPath;
            var contentRootPath = _hostingEnvironment.ContentRootPath;

            var executingAssemblyFilePath = Assembly.GetExecutingAssembly().Location;
            var executingAssemblyDirPath = !string.IsNullOrEmpty(executingAssemblyFilePath) ? Path.GetDirectoryName(executingAssemblyFilePath) : null;

            _baseDirPath = executingAssemblyDirPath ?? webRootPath ?? contentRootPath;
        }


        [HttpGet("download/{filename?}")]
        public async Task<IActionResult> Download(string filename = _defaultFileName)
        {
            try
            {
                var currentDir = Directory.GetCurrentDirectory();

                // Base Directory not found
                if (!Directory.Exists(_baseDirPath))
                {
                    return NotFound("Base Directory not found: " + _baseDirPath);
                }

                var containerDirPath = Path.Combine(_baseDirPath, _containerDirName);

                var path = Path.Combine(currentDir, containerDirPath, filename);

                // File Not Found
                if (!System.IO.File.Exists(path))
                {
                    return NotFound("File not found: " + path);
                }

                var memory = new MemoryStream();

                using (var stream = new FileStream(path, FileMode.Open))
                {
                    await stream.CopyToAsync(memory);
                }
                memory.Position = 0;

                var contentType = GetContentType(path);
                var fileName = Path.GetFileName(path);
                var timestamp = DateTime.Now.ToString("yyyyMMddHHmmss");
                fileName = $"{timestamp}_{fileName}";

                return File(memory, contentType, fileName);
            }
            catch (Exception ex)
            {
                return BadRequest("Download Failed: " + ex.Message);
            }
        }


        //----------------------------------
        //      UTIL Methods
        //----------------------------------


        private string GetContentType(string path)
        {
            var types = GetMimeTypes();
            var ext = Path.GetExtension(path).ToLowerInvariant();
            return types[ext];
        }

        private Dictionary<string, string> GetMimeTypes()
        {
            return new Dictionary<string, string>
            {
                { ".txt", "text/plain" },
                { ".pdf", "application/pdf" },
                { ".doc", "application/vnd.ms-word" },
                { ".docx", "application/vnd.ms-word" },
                { ".xls", "application/vnd.ms-excel" },
                { ".xlsx", "application/vnd.openxmlformats  officedocument.spreadsheetml.sheet" },
                { ".png", "image/png" },
                { ".jpg", "image/jpeg" },
                { ".jpeg", "image/jpeg" },
                { ".gif", "image/gif" },
                { ".csv", "text/csv" },
                { ".zip", "application/zip" },
                { ".tgz", "application/gzip" },
                { ".json", "application/json" }
            };
        }

    }
}
```

- Create folder _files and file data.json inside it:

      mkdir -p _files \
      && touch _files/data.json

- Paste this sample users data into data.json: 

```json
[
  {
    "id": 1,
    "name": "Keanu Reeves"
  },
  {
    "id": 2,
    "name": "Laurence Fishburne"
  },
  {
    "id": 3,
    "name": "Carrie-Anne Moss"
  },
  {
    "id": 4,
    "name": "Hugo Weaving"
  },
  {
    "id": 5,
    "name": "Gloria Foster"
  },
  {
    "id": 6,
    "name": "Joe Pantoliano"
  },
  {
    "id": 7,
    "name": "Marcus Chong"
  },
  {
    "id": 8,
    "name": "Julian Arahanga"
  },
  {
    "id": 9,
    "name": "Matt Doran"
  },
  {
    "id": 10,
    "name": "Belinda McClory"
  },
  {
    "id": 11,
    "name": "Anthony Ray Parker"
  },
  {
    "id": 12,
    "name": "Paul Goddard"
  },
  {
    "id": 13,
    "name": "Robert Taylor"
  },
  {
    "id": 14,
    "name": "David Aston"
  },
  {
    "id": 15,
    "name": "Marc Aden Gray"
  },
  {
    "id": 16,
    "name": "Ada Nicodemou"
  },
  {
    "id": 17,
    "name": "Deni Gordon"
  },
  {
    "id": 18,
    "name": "Rowan Witt"
  },
  {
    "id": 19,
    "name": "Bill Young"
  },
  {
    "id": 20,
    "name": "Eleanor Witt"
  },
  {
    "id": 21,
    "name": "Tamara Brown"
  },
  {
    "id": 22,
    "name": "Janaya Pender"
  },
  {
    "id": 23,
    "name": "Adryn White"
  },
  {
    "id": 24,
    "name": "Natalie Tjen"
  },
  {
    "id": 25,
    "name": "David O'Connor"
  },
  {
    "id": 26,
    "name": "Jeremy Ball"
  }
]
```

- Run the app in the terminal window:

      dotnet run

- Open a browser and navigate to http://localhost:5167/download .  **_(Replace the port number with the one you see in the termnal output.)_**

The api will download the file **_files/data.json** and save it to your **Downloads** folder.


### Part 2 - Create Client App - Console App to Download Files
  

In this exercise we will build a console app called **download** that calls an api point to download a file from server and save it to a local computer's folder.

This app will be able to be executed by a command that looks like this:

```bash
download https://www.mysite.com/download/db-backup /home/usb/backups
```
where the first argument is the url of the api point and the second argument is the path to the folder where the file will be saved.


- Create folder Clients inside your project folder.

      mkdir -p ~/dev/file-downloader/Clients

We can create different client applications that consume the api point we created in the previous exercise. For example we can create a console app, a web app, a mobile app, etc.

In this exercise we will create a console app called **download**.

- Create a console app called **download** inside the Clients folder:

      dotnet new console -o Clients/download

  - **-o** - means output folder path/name

- Open folder **Clients/download** in VS Codium

      codium Clients/download

- You should see a popup in the bottom-right that says "Required assets to build and debug are missing from 'download'. Add them?"
- Click **Yes**

- Open file **Program.cs** in the **Clients/download** folder and replace its content with the following code:

```csharp

var timestamp = DateTime.Now.ToString("yyyyMMddHHmmss");

if (args.Length < 2)
{
    Console.WriteLine("Please provide arguments: sourceUrl, destinationPath");
    Console.WriteLine(@"Example usage:");
    Console.WriteLine("./download http://www.example.com/download/filename /path/to/save", ConsoleColor.Yellow);

    Log("Arguments not provided: sourceUrl, destinationPath", timestamp);

    return;
}

var sourceUrl = args[0];
var destinationPath = args[1];
try
{
    using var client = new HttpClient();
    var response = await client.GetAsync(sourceUrl);
    var downloadPath = "";
    var message = "";

    // Request SUCCESS
    if (response.IsSuccessStatusCode)
    {
        var serverFileName = response.Content.Headers.ContentDisposition?.FileName;
        var fileName = string.IsNullOrEmpty(serverFileName) ? $"{timestamp}" : $"{timestamp}_{serverFileName}";
        
        downloadPath = Path.Combine(destinationPath, fileName);

        // Create file and copy stream into it
        using (
                    Stream contentStream = await response.Content.ReadAsStreamAsync(),
                    stream = new FileStream(downloadPath, FileMode.Create, FileAccess.Write, FileShare.None))
        {
            await contentStream.CopyToAsync(stream);
        }
    }
    // Request FAIL
    else
    {
        var result = response.Content.ReadAsStringAsync().Result;
        message = $@"Download Failed. Status: {response.StatusCode}, Reason: {response.ReasonPhrase}, Message: {response.RequestMessage}        
        Args provided:
            sourceUrl : {sourceUrl}
            destinationPath : {destinationPath}";
        throw new Exception(message);
    }

    message = $"File downloaded to: {downloadPath}";
    Console.WriteLine(message);
    Log(message, timestamp);
}
catch (Exception ex)
{
    var message = $@"Error when downloading: {ex.Message}
    Args provided:
        sourceUrl : {sourceUrl}
        destinationPath : {destinationPath} 
    ";
    Console.WriteLine(message);
    Log(message, timestamp);
}


/// <summary>
///     Log message to file
/// </summary>
static void Log(string message, string timestamp)
{
    var logPath = Path.Combine(AppDomain.CurrentDomain.BaseDirectory, "log.txt");
    if (!File.Exists(logPath))
    {
        var file = File.Create(logPath);
        file.Close(); //MUST do this otherwise you will get a file used by another process error

        File.AppendAllText(logPath, $"[{timestamp}] Log file created {Environment.NewLine}");
    }
    var logMessage = $"[{timestamp}] {message}";
    File.AppendAllText(logPath, $"{logMessage} {Environment.NewLine}");
}
```

- Open Terminal window in VS Codium:  hit **CTRL + `**
  - You should see terminal panel appear at the bottom of VS Codium

- Publish project to **bin/publish** folder by typing this command inside Terminal:

      dotnet publish -c Release -o ./bin/publish

- Run executable - In Terminal simply call the executable file **download.exe** that has been created inside bin/publish folder. NOTE: you don't need to provide file extension

      ./bin/publish/download

  - You should get output

        Please provide arguments: sourceUrl, destinationPath
        Example usage:
        ./download http://www.example.com/download/filename /path/to/save

Before we provide the arguments to download command, we first must make sure our web api is running

- Go to Web Api app we have open in other VS Codium window
- Run the web api app if it's not already running

      dotnet run

  - In Terminal you will see the url it runs on. Lets suppose it's http://localhost:5167

- Go back to Console app and run download command in Terminal again, but this time with arguments like:

      ./bin/publish/download  http://localhost:5167/download  ~/

  - You should see output like:

        File downloaded to: C:/Users/Admin/20231228103304_20231228103306_data.json

We provided as first argument url to our web api "http://localhost:5167/download", and as a second argument download location "~/" which is a shortcut to our user folder.

As a result the file was downloaded from Server with server timestamp and client timestamp in the name of the file "20231228103304_20231228103306_data.json".

- Go to your user folder and confirm that the downloaded file is there


### Part 3 - Deploy Server App

  - Go inside Web Api VS Codium window
  - Stop the app if it's running by hitting **CTRL + C** in Terminal
  - Publish project to **bin/publish** folder by typing this command inside Terminal:

        dotnet publish -c Release -o ./bin/publish



### Part 4 - Deploy Client App


Refs:
- [Microsoft - Publish .NET apps with the .NET CLI](https://learn.microsoft.com/en-us/dotnet/core/deploying/deploy-with-cli)
- [Microsoft - From .Net 8 Runtime-specific apps no longer self-contained](https://learn.microsoft.com/en-us/dotnet/core/compatibility/sdk/8.0/runtimespecific-app-default)


#### Prepare the Host

We will use username admin and hostname 192.168.1.105 as an example. Replace these with your own values.

- SSH into your remote Linux machine

      ssh -p 22 admin@192.168.1.105

    OR if ssh connection is stored in `~/.ssh/config`

      ssh myhostname 

- Create folder **apps/console**

      sudo mkdir -p /apps/console

- Create group admins

      sudo groupadd admins

- Add user admin to group admins
    
      sudo usermod -a -G admins admin

- Give group **admins** permission to whole folder **/apps**

      sudo chgrp -R admins /apps

- Change the permission of the **apps** folder to **770**. This will allow the group **admins** to read, write and execute files in the folder.

      sudo chmod -R 770 /apps



#### Publish

We want to run our console app on Linux by just calling its name as a command like: **"download"** instead of having to prefix it with **"dotnet"** command. 

To be able to do this, we need to compile the console app for Linux runtime environment. 

The way to publish .Net apps for a specific runtime environments is to specify the runtime flag `-r` with RID (Runtime Identifier). 

Here is the list of common RIDs for Linux variants:

| RID                | Description                                                                                  |
| ------------------ | -------------------------------------------------------------------------------------------- |
| linux-x64          | Most desktop distributions like CentOS, Debian, Fedora, Ubuntu, and derivatives              |
| linux-musl-x64     | Lightweight distributions using musl like Alpine Linux                                       |
| linux-musl-arm64   | Used to build Docker images for 64-bit Arm v8 and minimalistic base images                   |
| linux-arm          | Linux distributions running on Arm like Raspbian on Raspberry Pi Model 2+                    |
| linux-arm64        | Linux distributions running on 64-bit Arm like Ubuntu Server 64-bit on Raspberry Pi Model 3+ |
| linux-bionic-arm64 | Distributions using Android's bionic libc, for example, Termux                               |

For the full list of runtime IDs , see [RID Catalog](https://learn.microsoft.com/en-us/dotnet/core/rid-catalog)

For our example we will use Raspberry Pi 4 and therefore we will use **linux-arm64** RID.

- With your app open in VS Codium, open Terminal: **CTRL + `**
- Publish project to **bin/publish** folder

      dotnet publish -c Release -r linux-arm64 -o ./bin/publish

This will produce file called **download** without file extension in the folder **bin/publish**. This file is executable and can be run on Linux machine by typing `./download` in the terminal.



#### Deploy

- Refs: 
  - [Linuxize - How to Use SFTP Command to Transfer Files](https://linuxize.com/post/how-to-use-linux-sftp-command-to-transfer-files/)
  - [Digital Ocean - How To Use SFTP to Securely Transfer Files with a Remote Server](https://www.digitalocean.com/community/tutorials/how-to-use-sftp-to-securely-transfer-files-with-a-remote-server)
  - [Hostinger - How to use SFTP](https://www.hostinger.com/tutorials/how-to-use-sftp-to-safely-transfer-files/)

- SFTP into your Linux machine

      sftp admin@192.168.1.105

    OR if ssh connection is stored in `~/.ssh/config`

      sftp myhostname 

Quickest way to deploy your files is in one command that transfers all files from your local **"bin/publish"** folder to remote folder **"/apps/console/{{APP_NAME}}"**. {{APP_NAME}} is the name of the app you are deploying. In our case it is **download**.

- Copy binaries from local to remote host

    If app folder (download) not created already - this command will create the folder **download** inside **/apps/console** folder on the remote host and copy files there.

      put -r ./bin/publish/ /apps/console/download

    Else if app folder (download) already exists - this command will copy files to the existing folder

      put -r ./bin/publish/* /apps/console/download

  - **-r** flag is used to copy directories recursively. 



Bellow is a longer way to deploy your files. It explains how to navigate between local and remote folders and how to copy files from local to remote host.

- Navigate to **remote working directory** 

      cd /apps/console

- Confirm **remote working directory**

      pwd

- Navigate to **local working directory** - we have to prefix commands to local working directory with `l` inside SFTP shell

      lcd /path/to/project/root

- Confirm **local working directory**

      lpwd

- Switch to our local shell by typing `!` . Now you can use local shell commands without prefixing them with `l`.

- Switch back to SFTP shell by typing `exit`

- Copy local folder **publish** to remote host folder called **{{APP_NAME}}**. {{APP_NAME}} is the name of the app you are deploying. In our case it is **download**. This will create a new folder called **download** inside **/opt/dotnet** folder on the remote host.

      put -r ./bin/publish download

  - **-r** flag is used to copy directories recursively.

**NOTE:** If {{APP_NAME}} directory **already exists** on the remote machine, above statement will create a new folder "publish" inside {{APP_NAME}} folder and copy files there, which is NOT what we want. Instead we want to copy files directly to {{APP_NAME}} folder. To do that we need to use `mput` command.

- Copy multiple files from local **publish** folder to **existing** **{{APP_NAME}}** folder on remote host

      mput ./bin/publish/  * download
    
- Exit SFTP

      exit

#### Run

- SSH into your server

- Change the permission of the **download** folder to **770**. This will allow the group **admins** to read, write and execute files in the folder.

      chmod -R 770 /apps/console/download

- Navigate to app folder
    
      cd /apps/console/download

- Run the app from inside its folder

    ./download http://www.example.com/download/file /path/to/save

- Run the app from anywhere else on the server

    /apps/console/download/download http://www.example.com/download/file /path/to/save


### Part 5 - Create Cron Job