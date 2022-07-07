To run linux containers on Windows Server 2019 Hyper-V must be enabled and hyber-v continer created to host the docker instances because Windows Server 2019 does not support WSL2.

A. Enable Hyper-V
https://docs.microsoft.com/en-us/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server
Install-WindowsFeature -Name Hyper-V -ComputerName <computer_name> -IncludeManagementTools -Restart


B. Create the Hyper-V container used in step C
https://stackoverflow.com/questions/58289749/error-running-linux-container-on-windows-2019-server/70619369#70619369
New-VM -Name WinContainerHost -MemoryStartupBytes 1GB

!!OUTDATED - DO NOT USE HERE FOR REFERENE!! C. Install Docker EE on Windows Server 
https://docker-docs.netlify.app/install/windows/docker-ee/
https://computingforgeeks.com/how-to-run-docker-containers-on-windows-server-2019/
~~1. (remove previous version of docker) Uninstall-Package -Name docker -ProviderName DockerMSFTProvider~~
~~2. Get-VM WinContainerHost | Set-VMProcessor -ExposeVirtualizationExtensions $true~~
~~3. Install-Module DockerProvider~~
~~4. Install-Package Docker -ProviderName DockerProvider -RequiredVersion preview~~
~~5. (to enable linux containers and disable windows containers) [Environment]::SetEnvironmentVariable("LCOW_SUPPORTED", "1", "Machine")~~
~~6. Restart-Service docker~~
~~7. docker run -it --rm ubuntu /bin/bash~~
~~8. (from unix prompt) cat /etc/os-release ~~
~~9. (from unix prompt) exit~~
~~10. (to disable linux containers and enable windows containers) [Environment]::SetEnvironmentVariable("LCOW_SUPPORTED", "$null", "Machine")~~
~~11. Reboot machine otherwise the environment variable is not removed. Test existance with [Environment]::GetEnvironmentVariable("LCOW_SUPPORTED")~~
~~12. docker run hello-world:nanoserver~~

C. Install Docker EE on Windows Server 2019
The previous instructions would result in a outdated version of dockeree 1.17.x installed. 
Later when upgraded the non-ee version would overwrite the installation and the ability to run linux containers is lost.
Updated instruction from https://bcthomas.com/2019/02/getting-started-with-linux-containers-on-windows-server-2019/
1. Install-Module DockerMSFTProvider
2. Import-Module -Name DockerMSFTProvider -Force
3. Import-Packageprovider -Name DockerMSFTProvider -Force
4. Find-Package docker
5. Install-Package -Name Docker -Source DockerDefault
7. [Environment]::SetEnvironmentVariable("LCOW_SUPPORTED", "1", "Machine")
8. Create file C:\ProgramData\docker\config\daemon.json 
9. Add content to daemon.json { "experimental": true }
10. Invoke-WebRequest -Uri "https://github.com/linuxkit/lcow/releases/download/v4.14.35-v0.3.9/release.zip" -UseBasicParsing -OutFile release.zip
11. Expand-Archive release.zip -DestinationPath "$Env:ProgramFiles\Linux Containers\."
12. Restart server
13. docker run --rm -it --platform=linux ubuntu bash
14. docker run hello-world:nanoserver

D. To update dockeree to the latest release 
1. Install-Package -Name Docker -ProviderName DockerMSFTProvider -Update -Force
2. Restart-service docker

E. Networking
The docker bridge network is not created on Windows Server 2019 installations.
On Windows Server 2019 the default network is nat
The hyper-v virtual machine that hosts docker needs to be attached to the nat network via the Hyper-V Manager

F. Logging
By default docker container logs locally and logs are lost when a container is removed.
The host needs to configured to send logs to the host.
1. Added "log-driver": "etwlogs" to C:\ProgramData\docker\config\daemon.json 

https://docs.docker.com/config/containers/logging/etwlogs/
https://docs.docker.com/config/containers/logging/configure/