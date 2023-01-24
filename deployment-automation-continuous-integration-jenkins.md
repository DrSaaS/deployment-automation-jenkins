
## TOOLING WEBSITE DEPLOYMENT AUTOMATION WITH CONTINUOUS INTEGRATION USING JENKINS

## INSTALL AND CONFIGURE JENKINS SERVER
# Step 1 – Install Jenkins server
###Create an AWS EC2 server based on Ubuntu Server 20.04 LTS and name it "Jenkins"

###Install JDK (since Jenkins is a Java-based application)

sudo apt update
sudo apt install default-jdk-headless

## Install Jenkins
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > \
    /etc/apt/sources.list.d/jenkins.list'

sudo apt update
sudo apt-get install jenkins
## Make sure Jenkins is up and running

sudo systemctl status jenkins

### By default Jenkins server uses TCP port 8080 

### We open it by creating a new Inbound Rule in your EC2 Security Group

### Port 8080 opened in inbound rules of Jenkins Server Security group


## Perform initial Jenkins setup.
### From our browser access http://<Jenkins-Server-Public-IP-Address-or-Public-DNS-Name>:8080

http://13.41.72.190:8080/

### You will be prompted to provide a default admin password

### Retrieve password from the jenkins server:

sudo cat /var/lib/jenkins/secrets/initialAdminPassword

### login successfull


### Once plugins installation is done – we create an admin user and retrieve Jenkins server address. goldeneyepr Olu...Jen...

http://13.41.72.190:8080/:8080/

### The installation is completed!


# Step 2 – We configure Jenkins to retrieve source codes from GitHub using Webhooks
## We configure a simple Jenkins job/project (these two terms can be used interchangeably). 
This job will will be triggered by GitHub webhooks and will execute a ‘build’ task to retrieve codes from GitHub and store it locally on Jenkins server.

## Enable webhooks in our GitHub repository settings

http://18.133.75.123:8080/github-webhook/

## Next step is to go to the Jenkins web console, click "New Item" and create a "Freestyle project"

### select discard old builds

### Set maximum # of builds to keep to 4

### Change branch specifier to main from master

*/main

### click save

### Select checkbox under Build Triggers - GitHub hook trigger for GITScm polling

### I made some changes in the README.MD file) and pushed the changes to the main branch (commit on git website).

### A new build was launched automatically (by webhook) 
and the results – artifacts, saved on Jenkins server.


## We have now configured an automated Jenkins job that receives files from GitHub by webhook trigger (this method is considered as ‘push’ because the changes are being ‘pushed’ and files transfer is initiated by GitHub). There are also other methods: trigger one job (downstreadm) from another (upstream), poll GitHub periodically and others.

### By default, the artifacts are stored on Jenkins server locally

ls /var/lib/jenkins/jobs/tooling_github/builds/<build_number>/archive/


## CONFIGURE JENKINS TO COPY FILES TO NFS SERVER VIA SSH
# Step 3 – Configure Jenkins to copy files to NFS server via SSH

### Now we have our artifacts saved locally on Jenkins server, the next step is to copy them to our NFS server to /mnt/apps directory.


### Install "Publish Over SSH" plugin.
###On main dashboard select "Manage Jenkins" and choose "Manage Plugins" menu item.

###On "Available" tab search for "Publish Over SSH" plugin and install it



# Configure the job/project to copy artifacts over to NFS server.


### On main dashboard select "Manage Jenkins" and choose "Configure System" menu item.

## Scroll down to Publish over SSH plugin configuration section and configure it to be able to connect to your NFS server:

## Provide a private key (content of .pem file that you use to connect to NFS server via SSH/Putty)

### Hostname – Private IP address of your NFS server
### Username – ec2-user (since NFS server is based on EC2 with RHEL 8)

Remote directory – /mnt/apps since our Web Servers use it as a mointing point to retrieve files from the NFS server
### Test the configuration and make sure the connection returns Success.

### Success displayed


 Remember, that TCP port 22 on NFS server must be open to receive SSH connections.

### Make changes to README.md on github

cat /mnt/apps/README.md

# Success changes seen in cat /mnt/apps/README.md














































