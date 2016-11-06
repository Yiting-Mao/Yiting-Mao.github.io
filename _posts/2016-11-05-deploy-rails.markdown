---
layout: post
title:  "Deploy rails app on Elastic Beanstalk from scratch"
date:   2016-11-05 21:36:28 -0700
categories: rails beanstalk deploy CLI
---
During the study of rails development, at some point, you may need to deploy it onto AWS Elastic Beanstalk(EB). This is what I am recently doing. Together with three other members, we are devolopping a website [Bidder][bidder-github] to bid items. This is what I did to deploy our website on EB. I used Command Line Interface(CLI) instead of console for enviroment setup.

Reference: [Bryce's instruction][bryce-instruction], AWS documents

* * *

### 1. Launch an EC2 instance ###


1. Go to AWS console, click EC2, click launch instance
2. Select Amazon Linux AMI 2016.09.0 (HVM), SSD Volume Type - ami-b04e92d0
3. Select t2.micro(free tier eligible), click “review and launch"
4. Click “edit security group” 
5. Choose “create a new security group”, use the default name
6. Choose type “ssh”, restrict source with “myip”.
7. Click launch
8. Choose an existing key pair or create a new key pair

Note: instance type, security group, ssh source can be modified according to the needs

* * *

### 2. SSH to your instance with your key pair ###


1. view your instance from AWS console, get its public DNS
2. ` chmod 400 your_key_pair_name.pem`
3. `ssh -i yourkeypair.pem ec2-user@your_public_DNS`

* * *

### 3. Set up environment ###

#### 1. Install the Elastic Beanstalk Command Line Interface (EB CLI) ####
1. `pip install —upgrade —user awsebcli`
2. add the path to the executable file to your PATH (add below to ~/.bash_profile and source it)
  `export PATH=~/.local/bin:$PATH`
3. verify that eb cli has been installed using `eb —version`

#### 2. Make changes to your rails app, clone your git repo ####
1. make changes following [Bryce's instruction][bryce-instruction]
2. install git in your EC2 instance `sudo yum install git`
3. clone your git repo to your EC2 instance


#### 3. init(configure) EB ####
1. cd to your git repo
2. run `eb init`
3. select region(use default)
4. provide your credentials
    * to get a credential
        - go to AWS console, click your name on the right side of the header, click security credentials
        - click Get Started with IAM Users( or Users on the left side bar)
        - click create new users, enter name, check generate an access key for each user
        - download the credential
    * give user permission
        - back to Users main page
        - click the user name
        - click permissions
        - click attach policy, add amazon EC2 full access, AWSElasticBeanstalkFullAccess
5. select an application to use: choose default
6. It seems you are using Ruby, is it correct?: choose y
7. select platform version: 1) Ruby 2.3
8. Do you want to set up SSH for your instances?:y
9. Select a key pair.: choose your key pair


#### 4. create a deployment for demo ####
`eb create -db.engine postgres -db.i db.t2.micro -db.user u --envvars SECRET_KEY_BASE=RANDOM_SECRET --single DEPLOYMENT_NAME`

Replace DEPLOYMENT_NAME with a unique name for your deployment. Consider teamname-demo.

Replace RANDOM_SECRET with some semi-random large string of alphanumeric characters. Note, for real sites you want this to be really random as your cookies are signed/encrypted using a key derived from this string.

Hit enter and type in password(at least 8 letters' long)


[bidder-github]: https://github.com/scalableinternetservices/Bidder
[bryce-instruction]: https://github.com/scalableinternetservices/demo_rails5_beanstalk
