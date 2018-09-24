Integrate and Use Cloud9 for WildRydes

Notes:  This process requires GIT on your local machine as well as AWS Credentials

##1) Copy the WildRydes Website to your local machine

###a) mkdir WildRydes
###b) cd WildRydes
###c) aws s3 sync s3://wildrydes-us-east-1/WebApplication/1_StaticWebHosting/website . --region us-east-1

##2) Create the CodeCommit Repo

###a) In the console, select CodeCommit
###b) Click on "Create Repository"
###c) Name it "WildRydesWebsite"
###d) Skip Email notification
###e) use connection type https

##3) Upload the WildRydes Website

###a) Go to your WildRydes local directory
###b) type "git init ."
###c) type "git add ."
###d) type "git commit . -m Initial"
###e) type "git config --global credential.helper '!aws --profile <yourprofile> codecommit credential-helper $@'"
###f) type "git config --global credential.UseHttpPath true"
###g) type "git remote add origin https://git-codecommit.us-east-1.amazonaws.com/v1/repos/WildRydesWebsite"
###h) git push -u origin master

##4) Create the Code9 Environment and Clone Your Repo

###a) In the console, select Cloud9
###b) Click Create Environment
###c) Name it "WildRydes"
###d) Leave the defaults and click Next Step
###e) Click "Create Environment"
###f) When the environment is up, check for git by typing "git --version"
####1) if you do not have git, type "sudo yum update"
####2) "sudo yum install git"
####3) check for git by typing "git --version"
###g) Check for the AWS CLI by typing "aws --version"
####1) if you do not have aws cli, install it by typing "sudo yum install aws-cli"
###h) type "git config --global credential.helper '!aws codecommit credential-helper $@'"
###i) type "git config --global credential.UseHttpPath true"
###j) type "git clone https://git-codecommit.us-east-1.amazonaws.com/v1/repos/WildRydesWebsite"
###k) type "cd WildRydesWebsite" and do an "ls" to check for the files

*****WIP to create a CI/CD for WildRydes

##5) Create CodeBuild for Website

###a) Select CodeBuild from the Console
###b) Project Name "WildRydesWebsite"
###c) Source Provider "AWS Code Commit"
###d) Repository "WildRydesWebsite"
###e) Environment Image "Use an image managed by AWS CodeBuild"
###f) OS "None"
###g) 


