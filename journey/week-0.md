# Jenkin Installation and configuration 
This project integrated with the aws-bootcamp project 

For setting up jenkins navigate to the jenkins website [jenkins installation doc](https://www.jenkins.io/doc/book/installing/linux)

# Jenkins Installation and configuration 

Launch an ec2 install, for this task use RedHat linux server

### Install the following commands, `wget unzip tree git`

```sh 
sudo yum install -y wget unzip tree git 
```
Install Prerequisite 

```sh
sudo yum install fontconfig java-17-openjdk
```
Run the following commands 

```sh 
sudo wget -O /etc/yum.repos.d/jenkins.repo \
    https://pkg.jenkins.io/redhat-stable/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key
sudo yum upgrade
sudo yum install jenkins
sudo systemctl daemon-reload
```
## Breakdown of the above command 

1. `sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo`:
   - This command downloads the Jenkins repository configuration file (`jenkins.repo`) from the specified URL (`https://pkg.jenkins.io/redhat-stable/jenkins.repo`).
   - The `-O` option is used to specify the output file (`/etc/yum.repos.d/jenkins.repo`). This ensures that the downloaded file is saved with the correct name and location.
   - By placing the file in the `/etc/yum.repos.d/` directory, it allows `yum` (the package manager for Red Hat-based systems) to recognize and use the Jenkins repository for installing and updating Jenkins packages.

2. `sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key`:
   - This command imports the GPG key used to sign the Jenkins packages.
   - The `rpm --import` command is used to import public keys into the RPM database.
   - The specified URL (`https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key`) points to the GPG key file.

3. `sudo yum upgrade`:
   - This command upgrades all installed packages on the system to their latest versions.
   - The `yum upgrade` command retrieves package information from all enabled repositories and installs any updates available for the installed packages.
   - Running this command ensures that your system is up-to-date before installing or updating Jenkins.

In summary, these commands prepare the system for installing Jenkins by adding the Jenkins repository, importing the GPG key, and ensuring that all installed packages are up-to-date. After executing these commands, you can proceed with installing Jenkins using `yum`.

## Start Jenkins

```sh
sudo systemctl enable jenkins
sudo systemctl start jenkins
```
To check the jenkins status 

```sh
sudo systemctl status jenkins
```
You should see a display like this if all was successful [This shows an ok status of jenkins](/journey/screenshots/Screenshots/image.md)

Run the command to get the public-ip of the instance

```sh
curl ifconfig.me
```
copy this ip and add 8080 ex `public-ip:8080` 

8080 comes by default, in case this a different port was used, replace the port number with it

## copy initial admin Password 

```sh
sudo cat /var/lib/jenkins/secretes/initialAdminPassword
```

copy this password and past on the jenkins loging page







Note: impportant 

If Jenkins fails to start because a port is in use, run systemctl edit jenkins and add the following:

```sh
[Service]
Environment="JENKINS_PORT=8081"

```
Here, "8081" was chosen but you can put another port available.



# Docker-Jenkins installation script

```sh
#!/bin/bash

set -e  # Exit immediately if a command fails

# Check if script is run with root privileges
if [ "$(id -u)" -ne 0 ]; then
    echo "Error: This script must be run with root privileges."
    exit 1
fi

# Update hostname (optional)
# sudo hostnamectl set-hostname docker

# Install Docker
sudo apt update
sudo apt install docker.io -y

# Add current user to docker group
if ! sudo usermod -aG docker ubuntu; then
    echo "Error: Failed to add user 'ubuntu' to the 'docker' group."
    exit 1
fi

# Install Java (dependency for Jenkins)
sudo apt install fontconfig openjdk-17-jre

# Add Jenkins repository and install Jenkins
if ! sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key; then
    echo "Error: Failed to add Jenkins repository key."
    exit 1
fi

if ! sudo sh -c 'echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null'; then
    echo "Error: Failed to add Jenkins repository to sources list."
    exit 1
fi

sudo apt-get update

if ! sudo apt install jenkins -y; then
    echo "Error: Failed to install Jenkins."
    exit 1
fi

# Enable Jenkins service to start at reboot
if ! sudo systemctl enable jenkins; then
    echo "Error: Failed to start Jenkins service."
    exit 1
fi

# Start Jenkins service
if ! sudo systemctl start jenkins; then
    echo "Error: Failed to start Jenkins service."
    exit 1
fi

# Grant specific sudo permissions to Jenkins (if needed)
echo "jenkins  ALL=(ALL) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/jenkins

# Change Jenkins user password
if ! echo "jenkins:admin" | sudo chpasswd; then
    echo "Error: Failed to change Jenkins user password."
    exit 1
fi

# Enable password authentication for SSH
if ! sudo sed -i "/^[^#]*PasswordAuthentication[[:space:]]no/c\PasswordAuthentication yes" /etc/ssh/sshd_config; then
    echo "Error: Failed to enable password authentication for SSH."
    exit 1
fi

if ! sudo systemctl restart ssh; then
    echo "Error: Failed to restart SSH service."
    exit 1
fi

# Add Jenkins user to docker group (if needed)
sudo usermod -aG docker jenkins

# Additional steps...
```
## Jenkins-docker server instegration with other toos

Intergration with sonarqubue

run the command 

To create a sonaqube container

```sh
docker run --name sonar -d -p 5000:9000 sonaqube
```
the above command does the following 

- creates a sonaqube container named sonar using a sonaqube image from docker repo
- associates the sonarqube port(9000) with the server(docker-jenkins server) port (5000) FOR PORT FORWARDING

To create a nexus container

```sh
docker run -d -p 5500:8081 --name nexus sonatype/nexus3
```
execute command in nexus container to get the initial password

```sh
docker exec nexus cat /nexus-data/admin.password
```
what the above command does

- exec; used to execute command in a container
- nexus is the name of the contianer where we intend to execute a command
- /nexus-data/admin.password; is the path in the container where the initial password is stored

When trying to upload artifacts to jenkins the job fails because of unauthorized access

to archieve this vi into the */settings.xml file in jenkins 

```sh
vi tools/hudson.tasks.Maven_MavenInstallation/maven3.9.6/conf/settings.xml
```
- look for the servers tag
- add the following before the servers closing tag

 ```sh
    <server>
      <id>nexus</id>
      <username>admin</username>
      <password>passwordForNexus</password>
    </server>
```
- save and exit




  
