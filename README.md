A cloudformation template that accepts user inputs as parameters where applicable ( for example, Admin password). This template should setup VPC, create subnets, launch a CM instance, pull the necessary code (modules, classes, recipes etc) from a GIT repo (or S3), and configure the web instance for basic Drupal or Wordpress setup..

gitrepo = github

Make sure the following are installed before proceeding
- git
- python-boto
- ansible


SETUP AWS CREDENTIALS  
-  login into AWS console
    -  click on IAM
    -  then, Users
    -  security credentials
    - lastly, generate security credentials
- copy the credentials  or download
- On your terminal , run `aws configure` .. Note you will be prompted to input credentials
    - AWS Access Key ID [****************JWDA]:
    - AWS Secret Access Key [****************CWnI]:
    -  Default region name [us-east-1]:
    - Default output format [json]:



NEXT STEP:
- clone the git repository
  -   In any directory of your choice , run
  git clone https://github.com/*******/aws-deploy-wordpress.git  on your terminal
- In order to run ansible to run locally, we need to setup the host
   -  Add  
   ```
   [local]
   localhost
   ```
   to file `/etc/ansible/hosts`


   ABOUT ANSIBLE SCRIPT
The Ansible directory layout :

-  Deployment.yml #inventory file
- group_vars
  - all.yml   # here we assign variables to EC2 instances
-  File  ## contains the cloudformation  script

The cloudformation  script will creates the following :
  - 1 VPC
      - Creates a default "Main Route Table", "Security Group", "Network ACL"
  - 1 Public Subnet inside VPC
  - 2 Private Subnets inside VPC  
  - 2 Security Groups (One for WebServer, One for DB Instance)
      - DBSecGroup : Allows traffic only on DB port
      - WebServerSecGroup : Allows traffic on SSH (22) and HTTP(80) ports   
  - 1 EC2 Instance for WebServer ( Public Subnet )  
  - 1 RDS Instance for Wordpress Database ( Private Subnet )
  - 1 Internet Gateway
  - 1 Route Table and 1 Route for the internet attached to the Public Subnet


RUN THE ANSIBLE SCRIPT :

I recommend a dry run first to simulate our deployment without actually deploying

ansible-playbook -i /etc/ansible/hosts deploy-wp.yml --check

next: actual deployment

ansible-playbook -i /etc/ansible/hosts deploy-wp.yml --verbose  
