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
You should see a display like this if all was successful [This shows an ok status of jenkins](/journey/screenshots/Screenshots/image.md/ok_status.png)








Note: impportant 

If Jenkins fails to start because a port is in use, run systemctl edit jenkins and add the following:

```sh
[Service]
Environment="JENKINS_PORT=8081"

```
Here, "8081" was chosen but you can put another port available.
