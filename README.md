# UCCE_Scripts

Scripts for Cisco UCCE Reporting and Automation

---

## UCCE_Track_User_Login

The `UCCE_Track_User_Login` PowerShell script is designed to track Cisco Finesse user login activity via the Finesse REST API and log active agent details to a CSV file.

### Features

- Authenticates to Cisco Finesse using Basic Auth.
- Retrieves all users from the Finesse API.
- Logs details (timestamp, user ID, agent name) for users who are currently logged in (not in LOGOUT state).
- Output is saved as a timestamped CSV file in a specified folder.
- Automatically creates output/log directory and CSV file if they do not exist.
- Error handling with error logs written to `error_log.txt`.

### Configuration

Edit the following variables at the top of the script to match your environment:

- `$BaseURL`: Finesse server FQDN/IP, e.g. `https://finesse.example.com/finesse/api`
- `$Username`: Finesse admin username
- `$Password`: Finesse admin password
- `$OutputFolder`: Path to save output files (e.g. `C:\UCCE_Login_Logs`)
- `$LogFile`: Output CSV file name, customizable with date

### Usage

1. Update configuration variables as described above.
2. Run the script in PowerShell on a system with network access to the Finesse server.
3. The script will:
    - Create the output directory if missing.
    - Create a CSV with headers (`Timestamp,UserID,AgentName`) if missing.
    - Query the Finesse API for users.
    - For each user whose status is not `LOGOUT` or empty, log their details into the CSV.
    - Log errors to `error_log.txt` in the output folder.

### Logging

- **CSV Output:** Each run appends a line for each active user:
    ```
    Timestamp,UserID,AgentName
    2025-08-25 10:33:00,agent123,John Doe
    ```
- **Error Log:** Any API or script errors are appended to `error_log.txt` with timestamp.

### Requirements

- PowerShell
- Network access to Cisco Finesse API
- API credentials with permissions to read user info

---

## Task_Scheduler

The `Task_Scheduler` file is an XML definition for a Windows Scheduled Task that automates the execution of reporting scripts, such as `UCCE_Track_User_Login`.

### Features

- Scheduling of UCCE reporting scripts via Windows Task Scheduler.
- Can repeat every hour, daily, or per specified interval.
- Runs PowerShell scripts with elevated privileges.

### Configuration

- `<Author>` and `<UserId>` fields should be updated to match your domain and user credentials.
- `<Command>` and `<Arguments>` fields specify the PowerShell executable and the script to be run. Example:
    ```xml
    <Command>C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe</Command>
    <Arguments>-ExecutionPolicy Bypass -File "F:\Cisco\UCCE\Trace_Agent_Login_Daily_csv.ps1"</Arguments>
    ```
- `<StartBoundary>` and `<EndBoundary>` define when the schedule starts and ends.
- `<Interval>` specifies the frequency (e.g., PT1H for every hour).

### Usage

1. Edit the XML file to configure user, script path, and schedule as needed.
2. Import the XML file into Windows Task Scheduler:
    - Open Task Scheduler.
    - Select "Import Task".
    - Browse to the XML file and import.
3. The scheduler will now automatically run the reporting script at the specified intervals.

### How it Works

- The task runs the specified PowerShell script (such as `UCCE_Track_User_Login`) using the provided user credentials.
- The script collects and logs agent login data as described above.
- The process is automated and requires no manual intervention once configured.

---

By combining these scripts, you can fully automate daily Cisco UCCE agent login reporting and ensure consistent log collection.
