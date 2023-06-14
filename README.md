# wsl2-port-forward
Script to forward ports to wsl2

Credits to John Wright Stanly
https://jwstanly.com/blog/article/Port+Forwarding+WSL+2+to+Your+LAN/

1. On Demand
The command below can be run as administrator. Replace `[PATH_TO_SCRIPT]` with the directory of the script, like `C:\Users\John\Documents`.
```powershell
powershell.exe -File "[PATH_TO_SCRIPT]\Bridge-WslPorts.ps1"
```
Not only does powershell.exe run in PowerShell and cmd, but also in WSL. Therefore you can try running this in WSL's `.bashrc` to open ports on launch, just as long as you always run WSL as administrator.

2. Scheduled Task
Rather than running the script after every reboot, we can rely on a Windows Task to run the script automatically. Since WSL changes IP during reboots, we'll trigger the task upon logon.

Create the task by running the following commands in PowerShell as administrator. Replace `[PATH_TO_SCRIPT]` with the absolute directory of the script, like `C:\Users\John\Documents`. Now, ports will be forwarded always to the correct WSL IP across reboots.
```powershell
$a = New-ScheduledTaskAction -Execute "powershell.exe" -Argument "-File `"[PATH_TO_SCRIPT]\Bridge-WslPorts.ps1`" -WindowStyle Hidden -ExecutionPolicy Bypass"
$t = New-ScheduledTaskTrigger -AtLogon
$s = New-ScheduledTaskSettingsSet -AllowStartIfOnBatteries -DontStopIfGoingOnBatteries
$p = New-ScheduledTaskPrincipal -GroupId "BUILTIN\Administrators" -RunLevel Highest
Register-ScheduledTask -TaskName "WSL2PortsBridge" -Action $a -Trigger $t -Settings $s -Principal $p
```
If you need to change the port list later on, feel free to change `$ports` and rerun the script using the command above. The task will also use the updated script.

The script's output window will be hidden when running. If you want logs, add to the top of the script `Start-Transcript -Path "C:\Logs\Bridge-WslPorts.log" -Append`. This will record all logs to the file passed in.
