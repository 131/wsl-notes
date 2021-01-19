# Install WSL
```
dism.exe /online /Enable-Feature /FeatureName:NetFx3 /All
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
shutdown /r /t 0

curl.exe -L -o ubuntu-1804.appx https://aka.ms/wsl-ubuntu-1804
powershell -noprofile -command "Add-AppxPackage .\ubuntu-1804.appx"
%localappdata%\Microsoft\WindowsApps\ubuntu1804.exe install



wsl --list --verbose
#to remove
wsl --unregister 
```


# Install openssh-server
```
netsh firewall add portopening TCP 22 "Open Port 22"
```


```
sudo apt-get install openssh-server
sudo dpkg-reconfigure openssh-server


#change userdir to USERPROFILE -outside wsl)
NEWHOME=$(wslpath $(wslvar USERPROFILE))/ivs
mkdir -p $NEWHOME
sudo sed -i /etc/passwd -e "s#:$HOME:#:$NEWHOME:#"
sudo mv $HOME $NEWHOME
exit


mkdir ~/.ssh
chmod 700 ~/.ssh
curl https://github.com/131.keys >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys

echo "ivs ALL=NOPASSWD: /etc/init.d/wsl-init" | sudo tee /etc/sudoers.d/wsl-init
cat <<EOF | sudo tee /etc/init.d/wsl-init
#!/bin/sh

PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"

IFS=";"
for word in \$(wslvar PATH); do
  PATH=\$PATH:\$(wslpath -u "\$word")
done

echo PATH=\"\${PATH}\" > /etc/environment
echo DOCKER_HOST=\"tcp://127.0.0.1:2375\" >> /etc/environment

#Setting WSLENV is pointless, but you can FW custom var this way
#echo WSLENV=\"\$(wslvar WSLENV)\" > /etc/environment


if [ \$(id -u) != 0 ]; then
    sudo "\${BASH_SOURCE[0]}"
else
  service ssh start
  #service postgresql start
fi
EOF

cat <<EOF | sudo tee /etc/wsl.conf
#Let’s enable extra metadata options by default
[automount]
enabled = true
root = /mnt/
options = "metadata,umask=22,fmask=11"
mountFsTab = false
EOF

sudo chmod +x /etc/init.d/wsl-init


sudo sed -i 's/UsePrivilegeSeparation.*/UsePrivilegeSeparation no/m'  /etc/ssh/sshd_config
sudo service ssh restart
```






# autostart wsl using [dispatcher](https://github.com/131/dispatcher)

## Grand user right to log as service
```
## download resource kit
curl -o rktools.exe https://web.archive.org/web/20200803205217if_/https://download.microsoft.com/download/8/e/c/8ec3a7d8-05b4-440a-a71e-ca3ee25fe057/rktools.exe
rktools.exe /c /t:c:\temp
msiexec /q /i c:\temp\rktools.msi

set PATH=%PATH%;"C:\Program Files (x86)\Windows Resource Kits\Tools\"
ntrights +r SeServiceLogonRight -u %USERNAME% -m \\%COMPUTERNAME%
```

## Configure service (see [ci-boot.xml files](boots))
```
sc delete wslauto

sc create wslauto binPath= C:\apps\bin\ci-boot.exe type= own obj= "%computername%\%USERNAME%"
sc config wslauto start=auto
sc failure wslauto actions= restart/1000/restart/1000/restart/1000// reset= 30

sc.exe config wslauto obj= ".\%USERNAME%" password= "[password]"
```



## Configure git

```
git config --global user.name "François Leurent"
git config --global user.email "131.js@leurent.email"


git config --global push.default simple
git config --global core.filemode false
```

# Configure agent
## Windows style
```
REM Looking up pageant socket name

dir /w \\.\pipe\\|find "pageant" > %temp%\SSH_AUTH_NAME
set /P SSH_AUTH_NAME=<%temp%\SSH_AUTH_NAME
(SET SSH_AUTH_SOCK=//./pipe/%SSH_AUTH_NAME%) && setx SSH_AUTH_SOCK %SSH_AUTH_SOCK%
```


## Unix style

```
export SSH_AUTH_SOCK=
export SSH_PAGEANT_SOCK_NAME=$(cmd.exe /c "dir /w \\\\.\pipe\\\\" | grep pageant | sed -e 's/[[:blank:]]//g' -e 's/\r$//g')

if [ ! -z "$SSH_PAGEANT_SOCK_NAME" ]; then
    export SSH_AUTH_SOCK=/tmp/$SSH_PAGEANT_SOCK_NAME.sock

   if [ -z "$(pgrep -f socat.*npiperelay)" ]; then
    rm -f $SSH_AUTH_SOCK
    (setsid socat UNIX-LISTEN:$SSH_AUTH_SOCK,fork EXEC:"npiperelay.exe -ei -s //./pipe/$SSH_PAGEANT_SOCK_NAME",nofork &) > /dev/null 2>&1
   fi
else
  echo "No pageant running"
fi

```


