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

