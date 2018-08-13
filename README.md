#Account Lookup Lab!

##1) Create EC2 dev and qa instances

  t2.micro size
  use your default VPC, or a VPC with a public subnet

development Userdata
```
#!/bin/bash
sudo yum update -y
sudo yum install wget -y
sudo yum install git -y
```
QA Userdata
```
#!/bin/bash
sudo yum update -y
sudo yum install git -y
sudo yum install ruby -y
sudo yum install wget -y
```

Tags
  - name = development
  - name = QA

Create a security group for 22 and 8080 available to the internet

Use a new keypair or a keypair that you have access to

##2) Install node on both development and QA instances
```
ssh into the instance
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.32.0/install.sh | bash
. ~/.nvm/nvm.sh
nvm install node
nvm --version
```

##3) Download the source and put it in ~ on the Dev instance
```
wget https://s3.us-east-2.amazonaws.com/awstb-website/AccountLookup.zip
```

##4) Create an IAM user role for working with the instances and services
  a) EC2 service
     - AWSCodeCommitFullAccess policy
       - AmazonDynamoDBFullAccess policy
       - Role Name: EC2MeetupLabCommit
       - attach the role to your EC2 development instance

  b) EC2 service
     - AWSCodeDeployRole
       - AmazonDynamoDBFullAccess
       - AmazonS3FullAccess
       - Role Name: EC2MeetupLabDeploy
       - attach the role to your EC2 QA instance

  c) CodeDeploy Service Role
     - AWSCodeDeployRole
       - Name: CDMeetupLab

##4) setup your code commit repo
Go to CodeCommit
Name: {yourname}AccountLookup
Description: Simple account lookup tool

##5) clone your Code Commit repo to your EC2 Development instance
```
git config --global credential.helper '!aws codecommit credential-helper $@'
git config --global credential.UseHttpPath true
git clone https://git-codecommit.us-west-1.amazonaws.com/v1/repos/{yourname}AccountLookup
cd ~/{yourname}AccountLookup
unzip ~/AccountLookup.zip
```
nano server.js and update your region for dynamo

##6) create a dynamo table
Table Name: Accounts
PartitionKey: AccountNumber {Number}

Add a record:  AccountNumber:1, String: Name:{YourName}, Number: Amount:{somevalue}

##7) Setup your package.json file on your Development Instance
```
npm init
accountlookup
1.0.0
Account lookup tool
server.js
```

##8) Install node.js libraries on your Development instance
```
npm install aws-sdk
npm install express
npm install body-parser
npm install ejs
```

##9) Test!
start the app by typing "npm start"
go to a browser and type in your development instanceip address:8080
control+c will end the program

##10) update your code commit repo on your Development instance
```
git add *
git commit -m "Initial Checkin"
git push origin master
```
Everything is now in CodeCommit!

##11) Install the code deployment agent on your QA instance
```
wget https://aws-codedeploy-us-west-1.s3.amazonaws.com/latest/install
chmod +x ./install
sudo ./install auto
```

##12) Create the Application in CodeDeploy
Go to CodeDeploy in the Console and click Get Started
  - Custom Deployment
  - Application Name: AcctLookup
  - Deployment Group Name: QA
  - Amazon EC2 Instances: Key: Name, Value: QA
  - Role - CDMeetupLab
  - Create Application

##12) build the CodePipeline
Go to CodePipeline in the Console and click Get Started
  - Name: AccountLookup
  - Source Provider: "AWS CodeCommit"
  - Repository Name: {yourname}AccountLookup
  - Branch: Master
  - Change Detection: CloudWatch Event
  - Build Provider: No build
  - Deploy Provider: AWS CodeDeploy
  - Application Name: AcctLookup
  - Deployment Group: QA  
  - Create Role -> Click Allow
  - Click Next Step
  - Click Create Pipeline

##13) On the CodePipeline Page
Watch the steps go from "No executions yet" to "In Progress"
Wait until your staging deployment is complete

##14) Once complete, login to the qa instance and from ~ type "npm start"

##15) go to the QA instance ip address:8080 in a browser and test that you can hit the application
