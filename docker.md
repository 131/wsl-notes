##############

# pre-requirements (make sure Processor is compatible with HyperV)

dism.exe /online /Enable-Feature /FeatureName:Microsoft-Hyper-V /All /norestart
dism.exe /online /Enable-Feature /FeatureName:Containers /All /norestart
#then
shutdown /r /t 0



curl -o wsl_update_x64.msi https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi
msiexec /q /i wsl_update_x64.msi
wsl --list --all -v

curl -o C:\temp\docker.exe "https://desktop.docker.com/win/stable/Docker%20Desktop%20Installer.exe"
C:\temp\docker.exe install --quiet
shutdown /r /t 0

curl -o C:\temp\procexp.zip https://download.sysinternals.com/files/ProcessExplorer.zip
curl -o C:\temp\PSTools.zip https://download.sysinternals.com/files/PSTools.zip
powershell -noprofile -command "Expand-Archive C:\temp\PSTools.zip -Force -DestinationPath C:\apps\bin"
powershell -noprofile -command "Expand-Archive C:\temp\procexp.zip -Force -DestinationPath C:\apps\bin"



for /F "tokens=*" %1 in ('wevtutil.exe el') DO wevtutil.exe cl "%1"

###### On wsl Host

echo "export DOCKER_HOST=tcp://localhost:2375" >> ~/.bashrc
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


#######

