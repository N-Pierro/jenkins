# Jenkin Installation and configuration 
This project integrated with the aws-bootcamp project 

For setting up jenkins navigate to the jenkins website [jenkins installation doc](https://www.jenkins.io/doc/book/installing/linux)

# Jenkins Installation and configuration 

Launch an ec2 install, for this task use RedHat linux server

### Install the following commands, `wget unzip tree git`

```sh 
sudo yum install -y wget unzip tree git 
```





Note: impportant 

If Jenkins fails to start because a port is in use, run systemctl edit jenkins and add the following:

```sh
[Service]
Environment="JENKINS_PORT=8081"

```
Here, "8081" was chosen but you can put another port available.
