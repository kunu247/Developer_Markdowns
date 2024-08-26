>Here is a Windows Batch script command to open CMD in administrator mode:

```batch
runas /user:Administrator cmd
```

>This command uses the `runas` command to run CMD as the Administrator user. Alternatively, you can use:

```batch
powershell.exe -Command "Start-Process cmd -Verb RunAs"
```

>This command uses PowerShell to start a new CMD process with elevated privileges (i.e., as an administrator). You can also use:

```batch
elevate cmd
```

>This command uses the elevate command (available in Windows Vista and later) to run CMD with elevated privileges.

**Note** : You may be prompted to enter the Administrator password or confirm elevation, depending on your system settings.

Save any of these commands in a batch file (e.g., open_admin_cmd.bat) and run it to open CMD in administrator mode.

---

To create a Windows Batch script that opens the Command Prompt (CMD) in administrator mode, you can use the following commands:

1. **Create a Batch File (.bat)**: 
    Open Notepad or any text editor, and paste the following code:

```batch
   @echo off
   :: Check if running as admin
   net session >nul 2>&1
   if %errorLevel% neq 0 (
       echo Requesting administrative privileges...
       powershell -Command "Start-Process cmd -ArgumentList '/k, cd /d %CD%' -Verb RunAs"
       exit
   )

   echo Already running with administrative privileges.
   pause
```

   - **Explanation**:
     - `@echo off`: Turns off command echoing in the batch file.
     - `net session >nul 2>&1`: Checks if the script is running with administrative privileges.
     - `if %errorLevel% neq 0`: If the script is not running as an administrator (`%errorLevel%` is not 0), it executes the PowerShell command to restart the Command Prompt in administrator mode.
     - `powershell -Command "Start-Process cmd -ArgumentList '/k, cd /d %CD%' -Verb RunAs"`: Uses PowerShell to start a new instance of `cmd.exe` with administrator privileges. The `/k` argument keeps the command prompt open after it starts.
     - `exit`: Exits the batch script after requesting administrator mode.
     - `pause`: Pauses the script to allow the user to see the message that the script is already running with administrative privileges.

2. **Save the Batch File**:
   Save the file with a `.bat` extension, for example, `OpenAdminCMD.bat`.

3. **Run the Batch File**:
   Double-click the batch file. If not already running as an administrator, it will request administrative privileges and reopen the Command Prompt with admin rights. If already running as an admin, it will display a message and wait for a keypress before closing.