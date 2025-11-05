# Custom SSL image build files for NextCloud container
- Author: James Spickard
- School: Regis University
- Class: MSSE695 - Software Engineering Res & Dev
- Semester: Fall 2022 8 Week 2
- Professor: Doctor Kevin Pyatt
- Due date: 12/11/2022

## Description
This repo is being used to aid in the Research and Development (R&D) of containerization by using the NextCloud Free Open Source Software (FOSS) as the main service of installation. This repo adds a self-signed certificate to implement SSL and the https protocol upon application launch to the existing content described in [Reference](https://github.com/jspickard/MSSE695-2022FAL8W2#references) 1.

## What's Required
- A test computer connected to a local network with an internet browser (i.e.: Windows 10 Home Version 21H2 with Edge 64-bit Version 107.0.1418.52 was used for the main test.)
- A server with the following:
1.  An Operating System (OS) that can install Docker.
2.  A known pingable IP address on a local network.
```shell
# Ubuntu Server 18.04.6 LTS 64-bit
# Fedora 37 Server x86_64 
ip a    # get IP here then ping from test computer (i.e.: Windows 10 command prompt "ping 192.168.238.131")

```
3.  If the server has port restrictions, allow 8081 at a minimum.
4. Docker and Docker-Compose is installed.
```shell
# Ubuntu Server 18.04.6 LTS 64-bit
sudo apt-get install curl

```
```shell
# Ubuntu Server 18.04.6 LTS 64-bit
# Fedora 37 Server x86_64 
sudo curl -fsSL get.docker.com | sudo sh
sudo systemctl start docker    # Fedora it did not start automatically

```
```shell
# Ubuntu Server 18.04.6 LTS 64-bit
sudo apt  install docker-compose

```
```shell
# Fedora 37 Server x86_64 
dnf install docker-compose

```
5. Other installations may be required throughout (i.e.: git); install as needed.
```shell
# Fedora 37 Server x86_64 
dnf install git-all

```

## Installation instructions
#### Notes: 
- Example commands have been provided. Use what is relative to your OS. 
### Steps
1. Make the required directories.
- These directories store the files (data) and user info (datadb).  
```shell
# Ubuntu Server 18.04.6 LTS 64-bit
# Fedora 37 Server x86_64
#export cloudDir="/home/nextcloud"    #orig local dir
#export cloudDir="/media/CloudDrive"    #mounted drive dir
export cloudDir="/media/CloudDrive2025"    #new mounted drive dir
sudo mkdir -p ${cloudDir}/mygit
sudo mkdir -p ${cloudDir}/data
sudo mkdir -p ${cloudDir}/datadb
sudo chmod 775 -R ${cloudDir}
```
- Note 1: Other directories can be used (i.e.: if you were to use a seperate drive with a lot of storage); if that is desired, replace those directories here and the volumnes directories in the nextcloud-js/docker-compose.yml file.
- Note 2: For using external drives (MUST BE ext4, not FAT), the following must be done manually: the drive identified, mount locataion created, mounted to that location, verified, then permanently mounted (recommend verifying through restart). The following site is a useful reference: https://linuxconfig.org/howto-mount-usb-drive-in-linux

2. Enter into new dir and clone this git.
```shell
# Ubuntu Server 18.04.6 LTS 64-bit
# Fedora 37 Server x86_64 
cd ${cloudDir}/mygit
sudo git clone https://github.com/jspickard32/MSSE695-2022FAL8W2-Clone.git


```
3. Enter into dockerfile dir and change permissions on dependent script.
```shell
# Ubuntu Server 18.04.6 LTS 64-bit
# Fedora 37 Server x86_64 
sudo chmod 700 ${cloudDir}/mygit/MSSE695-2022FAL8W2/nextcloud-js/dockerfile/nextcloud-ssl-js.sh

```
4. Build docker image.
```shell
# Ubuntu Server 18.04.6 LTS 64-bit
# Fedora 37 Server x86_64 
cd ${cloudDir}/mygit/MSSE695-2022FAL8W2/nextcloud-js/dockerfile
sudo docker build -t nextcloud-js .

```
5. Run container from image.
```shell
# Ubuntu Server 18.04.6 LTS 64-bit
# Fedora 37 Server x86_64 
cd ${cloudDir}/mygit/MSSE695-2022FAL8W2/nextcloud-js/
sudo docker compose up    # add -d to ignore details, old command was "docker-compose"

```

## Operating instructions
- Open a browser on your test computer and goto the following link (replace ###.###.###.### with the OS's known IP address)
https://###.###.###.###:8081
- Acknowledge the "NET::ERR_CERT_AUTHORITY_INVALID" self-signed cert warning and proceed.
- Follow the NextCloud prompts for proper setup (additional features may require more ports to be open).

## Copyright and licensing information.
The owner of this repo does not own NextCloud; these files are intended to aid in the R&D of containerization only.

## Contact information for the distributor or programmer.
James Spickard, jspickard@regis.edu

## Known bugs/issues.
1.  The Non-SSL site http://###.###.###.###:8080 was originally disabled for this demonstration, but was re-enabled for experimenting with the buddy CI/CD tool. Recommend disabling port 8080:80 traffic again to keep SSL only. Redirection with containers proved difficult using the existing port route configuration. The following guide should be referenced for information on redirect.
https://www.namecheap.com/support/knowledgebase/article.aspx/9821/38/apache-redirect-to-https/

## Troubleshooting.
1. If necessary, delete the git repo dir, stop and remove all containers, then restart from step 2 in the instructions.
```shell
# Ubuntu Server 18.04.6 LTS 64-bit
# Fedora 37 Server x86_64
## Docker containers
sudo docker stop $(sudo docker ps -a -q)    # stop all containers
sudo docker rm $(sudo docker ps -a -q)    # remove all containers
```
```shell
## git local clone
sudo rm -r ${cloudDir}/mygit/MSSE695-2022FAL8W2    # delete copied git dir
```
```shell
## Docker images
sudo docker image prune -a -f    # remove all images
```
```shell
## Volumes
# !!!WARNING!!! Deletes Volume content! Only do this if not trying to preserve data yet!!! (i.e.: troubleshooting initial setup)
#sudo rm -R ${cloudDir}/datadb # !!!WARNING!!! Deletes Volume content! (i.e.: user stored database schema). Only run when needed to delete admin account and redo setup, delete/rename datadb and data dirs from Step 1
#sudo rm -R ${cloudDir}/data # !!!WARNING!!! Deletes Volume content! (i.e.: user stored cloud files). Only run when needed to delete admin account and redo setup, delete/rename datadb and data dirs from Step 1
```

2. If changing IPs is necessary, make changes to file /var/www/html/config/config.php (global IP should be added here too).
```shell
# example of inside config.php
  'trusted_domains' =>
  array (
    0 => '192.168.1.182:8081',
    1 => 'raspberrypi.local:8081',
  ),
```

3. Issues with config.php (mine was at /var/www/html/config/config.php)
- Note: These changes are in the docker container. You will need an editor like nano to change.
```shell
# install nano
apt-get update
apt-get install nano
```
- If the following error is shown after setting up admin account and logging out "Your data directory is readable by other users" "Please change the permissions to 0770 so that the directory cannot be listed by other users", if changing permissions of the data location (both local volume dir and inside container, recommend reboot to test) does not work, the config.php can be revised again (with caution) as advised per https://github.com/nextcloud/server/blob/c364b0cb193f66ad15e2950c27113b40037d1bf6/config/config.sample.php#L747-L757
```shell
# example at end of config.php
'check_data_directory_permissions' => false,
```
- To disable file locking, add the following:
```shell
# example at end of config.php
'filelocking.enabled' => false,
```

## References
1. Open Source Tech Training. (2021, July 27). Nextcloud in Docker! opensourcetechtrn.blogspot.com. https://opensourcetechtrn.blogspot.com/2021/07/nextcloud-in-docker.html 
