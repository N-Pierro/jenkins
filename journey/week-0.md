# Jenkin Installation and configuration 
This project integrated with the aws-bootcamp project 

For setting up jenkins navigate to the jenkins website [jenkins installation doc](https://www.jenkins.io/doc/book/installing/linux)

Note: impportant 

If Jenkins fails to start because a port is in use, run systemctl edit jenkins and add the following:

```sh
[Service]
Environment="JENKINS_PORT=8081"

```
Here, "8081" was chosen but you can put another port available.
