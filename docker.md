# Docker setup (windows 10)
```
# pre-requirements (make sure Processor is compatible with HyperV)

dism.exe /online /Enable-Feature /FeatureName:Microsoft-Hyper-V /All /norestart
dism.exe /online /Enable-Feature /FeatureName:Containers /All /norestart
#then
shutdown /r /t 0
```


# Install docker
```
# install wsl2
curl -o wsl_update_x64.msi https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi
msiexec /q /i wsl_update_x64.msi
wsl --list --all -v

curl -o C:\temp\docker.exe "https://desktop.docker.com/win/stable/Docker%20Desktop%20Installer.exe"
C:\temp\docker.exe install --quiet
shutdown /r /t 0
```


# Configure wsl host
```
echo "export DOCKER_HOST=tcp://127.0.0.1:2375" >> ~/.bashrc
source  ~/.bashrc


sudo apt-get update

sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"

sudo apt-get update
sudo apt-get install docker-ce-cli 
```

# Toggle docker style

```
# list current platform
cmd.exe /c docker buildx ls

"/mnt/c/Program Files/Docker/Docker/DockerCli.exe" -SwitchWindowsEngine
"/mnt/c/Program Files/Docker/Docker/DockerCli.exe" -SwitchLinuxEngine
```


# Expose dockerd
```
# in daemon.json

   "hosts" : ["tcp://0.0.0.0:2375"],

netsh advfirewall firewall add rule name="Allow TCP 2375" dir=in action=allow protocol=TCP localport=2375


# for docker desktop (windows)
# forward external 2375 to internal 2375
netsh interface portproxy add v4tov4 listenport=2375 listenaddress=[public IP] connectaddress=127.0.0.1 connectport=2375

```