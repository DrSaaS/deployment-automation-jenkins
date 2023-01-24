TASK
----
Our aim here is to set up a continuous Integration pipeline.
We shall configure jenkins to copy files to our NFS Server via SSH
Whenever we push code to our github repository, the webhook will trigger a Jenkins (Publish over SSH plugin installed) build to copy artifacts to our configured NFS Server.


STEP 1-  INSTALL AND CONFIGURE JENKINS SERVER


- Spin up an AWS EC2 server based on Ubuntu Server 20.04 LTS and name it "Jenkins"

- Install JDK (since Jenkins is a Java-based application)
```
sudo apt update
sudo apt install default-jdk-headless
```
Install Jenkins
```
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > \
    /etc/apt/sources.list.d/jenkins.list'

sudo apt update
sudo apt-get install jenkins
```

Verify that Jenkins is up and running
```
sudo systemctl status jenkins
```

![Jenkins Status](https://github.com/deleonab/deployment-automation-jenkins/blob/main/install-jenkins.JPG?raw=true)
We will open port 8080 by creating a new Inbound Rule in our Jenkins server EC2 Security Group

- Next we shall set up= Jenkins in the browser
- 
- From our browser access http://<Jenkins-Server-Public-IP-Address-or-Public-DNS-Name>:8080

http://13.41.72.190:8080/
    
    

You will be prompted to provide a default admin password

 Retrieve password from the jenkins server:

sudo cat /var/lib/jenkins/secrets/initialAdminPassword

 ![Jenkins Set up](https://github.com/deleonab/deployment-automation-jenkins/blob/main/unlock-jenkins.JPG?raw=true)   
 login successfull

![Plugin install](https://github.com/deleonab/deployment-automation-jenkins/blob/main/customise-jenkins.JPG?raw=true)
### Once plugins installation is done – we create an admin user and retrieve Jenkins server address. goldeneyepr Olu...Jen...

http://13.41.72.190:8080/:8080/

The installation is completed!
![Jenkins Ready](https://github.com/deleonab/deployment-automation-jenkins/blob/main/jenkins-ready.JPG?raw=true)

STEP 2 – I will configure Jenkins to retrieve source code from GitHub using Webhooks
    
I will configure a simple Jenkins job/project (these two terms can be used interchangeably). 
This job will will be triggered by GitHub webhooks and will execute a ‘build’ task to retrieve code from GitHub and store it locally on Jenkins server.
    
STEP 3 - Enable webhooks in our GitHub repository settings
- I will access the github and enable Enable webhooks in our GitHub repository settings

http://18.133.75.123:8080/github-webhook/

- Next step is to go to the Jenkins web console, click "New Item" and create a "Freestyle project"
    
- select discard old builds

- Set maximum # of builds to keep to 4

- Change branch specifier to main from master

*/main

-click save

- Select checkbox under Build Triggers - GitHub hook trigger for GITScm polling

- I made some changes in the README.MD file) and pushed the changes to the main branch (commit on git website).

- A new build was launched automatically (by webhook) 
and the results – artifacts, saved on Jenkins server.

![Jenkins Build](https://github.com/deleonab/deployment-automation-jenkins/blob/main/jenkins-build.JPG?raw=true)    

We have now configured an automated Jenkins job that receives files from GitHub by webhook trigger (this method is considered as ‘push’ because the changes are being ‘pushed’ and files transfer is initiated by GitHub). There are also other methods: trigger one job (downstreadm) from another (upstream), poll GitHub periodically and others.

By default, the artifacts are stored on Jenkins server locally
```
ls /var/lib/jenkins/jobs/tooling_github/builds/<build_number>/archive/
```


STEP 4 – I will configure Jenkins to copy files to our NFS server via SSH

Now we have our artifacts saved locally on Jenkins server, the next step is to copy them to our NFS server to /mnt/apps directory.


In Jenkins, we shall Install the "Publish Over SSH" plugin.
- On main dashboard select "Manage Jenkins" and choose "Manage Plugins" menu item.

- On "Available" tab search for "Publish Over SSH" plugin and install it


I will now configure the job/project to copy artifacts over to NFS server.


- On the main dashboard select "Manage Jenkins" and choose the "Configure System" menu item.

- Scroll down to Publish over SSH plugin configuration section and configure it to be able to connect to our NFS server:

- Provide a private key (content of .pem file that you we use to connect to NFS server via SSH/Putty)

- Hostname – Private IP address of our NFS server
- Username – ec2-user (since NFS server is based on EC2 with RHEL 8)
![Jenkins SSH Copy set up](https://github.com/deleonab/deployment-automation-jenkins/blob/main/sshsuccessjenkins.JPG?raw=true)
- Remote directory – /mnt/apps since our Web Servers use it as a mointing point to retrieve files from the NFS server
- Let us test the configuration and make sure the connection returns Success.

- Success displayed


 - Port 22 on NFS server security group must be open to receive SSH connections.

 Make changes to README.md on github

cat /mnt/apps/README.md

# Success changes seen in cat /mnt/apps/README.md
![Jenkins copied](https://github.com/deleonab/deployment-automation-jenkins/blob/main/sshtransfersuccessful.JPG?raw=true)
    
    













