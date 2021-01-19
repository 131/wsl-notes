# Configure poweroff

```
powercfg.exe /hibernate off
powercfg -x -standby-timeout-ac 0
```


# Install misc tools
```
curl -o C:\temp\procexp.zip https://download.sysinternals.com/files/ProcessExplorer.zip
curl -o C:\temp\PSTools.zip https://download.sysinternals.com/files/PSTools.zip
powershell -noprofile -command "Expand-Archive C:\temp\PSTools.zip -Force -DestinationPath C:\apps\bin"
powershell -noprofile -command "Expand-Archive C:\temp\procexp.zip -Force -DestinationPath C:\apps\bin"


# clear events logs
for /F "tokens=*" %1 in ('wevtutil.exe el') DO wevtutil.exe cl "%1"
```
