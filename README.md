# Jenkins-Ansible-AWS Deployment pipeline setup

1.	Architecture 
 
 
2.	Create four EC2 Linux t2 micro server in AWS
 
3.	Configure Jenkins server
 
 sudo su
cd
 
Follow steps on below link or search Jenkins download steps in google
 https://pkg.jenkins.io/redhat-stable/
 
References:
https://www.jenkins.io/doc/book/installing/linux/#red-hat-centos
Jenkins Installation on Linux EC2 Instance| How to Install Jenkins on AWS |
 
 
service jenkins status
service jenkins start
chkconfig jenkins on - start jenkins at boot time
 
AdminUser/Password@2022
 
http://3.21.55.150:8080/
 
 
4.	Install ansible
 
yum install ansible
sudo amazon-linux-extras install ansible2
vim /etc/ansible/hosts
 
Add below entry in ansible hosts file
 
[web]
Ip address - web server
 
5.	Install Apache on web server 
 
yum install httpd
systemctl start httpd
systemctl enable httpd
 
6.	Create password less ssh authentication between ansible and web server
 
On ansible server
 
Create key - press enter, enter 
ssh-keygen 
 
ssh-copy-id -i root@172.31.41.51
 
On web server - set password
 
passwd root
set password - and save it somewhere
 
Enable password authentication on web server
vim /etc/ssh/sshd_config
PermitRootLogin yes
PasswordAuthentication yes
systemctl restart sshd
 
On ansible server
 
ssh-copy-id -i root@privateIPAddress
 
Enter password of webserver 
 
We can ssh to web server from ansible server without entering password 
 
ssh root@privateIPAddress
 
On Jenkins server
 
Create key - press enter, enter 
ssh-keygen 
 
On ansible server 
 
passwd root
set password - and save it somewhere
 
Enable password authentication on ansible server
vim /etc/ssh/sshd_config
PermitRootLogin yes
PasswordAuthentication yes
systemctl restart sshd
 
On jenkins server 
 
ssh-copy-id -i root@privateIPAddress

Enter ansible server password- after it we should be able to ssh on ansible server trough Jenkins server 
 
ssh root@privateIPAddress
 
 
7.	Create playbook on ansible server 
 
Vim playbook.yml
 
- hosts: all
  tasks:
    - copy:
        src: /opt/index.html
        dest: /var/www/html
 
8.	Create git hub repository
 
Add index.html file in it
Add git-hub webhook using jenkins url in payload 
http://3.21.55.150:8080/github-webhook/
Select application/json
 
Create API token in Jenkins
Save changes and paste the Jenkins token to GitHub webhook 
 
9.	Install publish over ssh plugin in Jenkins that will help us to push file from Jenkins server to ansible using ssh
Install this plugin from available plugins  without restart and select restart button
 
10.	Create freestyle project named as demo-project
11.	Install Git on Jenkins server
 
 yum install git
 
12.	Git project setup
 
https://github.com/NavnathGit/demo-devops-project-1.git
 
 
 
Select 
 
 
 
13.	Setup root user password on Jenkins server
 
14.	Enable password authentication on ansible server
 
passwd root
set password - and save it somewhere
 
vim /etc/ssh/sshd_config
PermitRootLogin yes
PasswordAuthentication yes
systemctl restart sshd
 
15.	Add ssh server through Jenkins configuration , use password 
16.	Update job to execute ssh command while build.
 
rsync -avh /var/lib/jenkins/workspace/demo-project/*.html root@172.31.34.250:/opt/index.html
 
 
 
17.	Index.html file will get copied to /opt/ folder on ansible server 
 
 
 
18.	Add ansible server in ssh - using manage Jenkins- configure like Jenkins server
19.	Add post build action - send build artifact over ssh
20.	Select ansible server and run below command to execute ansible playbook
 
ansible-playbook /root/sourcecode/playbook.yml
 
21.	On successful build - below page will be displayed on successful build using web-server ip
 
Please make sure to add rule to enable http traffic
 
 
 
22.	Install git on web server 
 
yum install git
 
23.	Clone repo on web server
 
git clone https://github.com/NavnathGit/demo-devops-project-1.git
 
 
25.	Modify index.html file and commit the changes
 
It will trigger automated build and deploy the changes on webserver using ansible playbook.
 
 

