 AWS Static Website Hosting Lab
This hands-on project demonstrates how to set up a public static website on Amazon S3 using EC2, IAM, AWS CLI, and scripting for repeatable deployments. Below is a step-by-step breakdown of the lab process.

                 ===========# Task 1: Connect to EC2 via SSM
Connected to an Amazon Linux EC2 instance using AWS Systems Manager Session Manager.

Launched the EC2 console session in-browser as ssm-user.

Switched to the ec2-user using:

bash
Copy
Edit
sudo su -l ec2-user

             ===========================# Task 2: Configure AWS CLI
             
Configured AWS CLI on EC2 (pre-installed on Amazon Linux):

bash
Copy
Edit
aws configure
Provided:

Access Key ID

Secret Access Key

Region: us-west-2

Output format: json

          ==================# Task 3: Create an S3 Bucket via CLI
Created a new S3 bucket using:

bash
Copy
Edit
aws s3api create-bucket --bucket <your-bucket-name> \
  --region us-west-2 \
  --create-bucket-configuration LocationConstraint=us-west-2
Verified bucket creation from JSON response.

            ======================# Task 4: Create IAM User with S3 Access
Created an IAM user:

bash
Copy
Edit
aws iam create-user --user-name awsS3user
Set a login profile:

bash
Copy
Edit
aws iam create-login-profile --user-name awsS3user --password Training123!
Attached full S3 access policy:

bash
Copy
Edit
aws iam list-policies --query "Policies[?contains(PolicyName,'S3')]"
aws iam attach-user-policy --policy-arn arn:aws:iam::aws:policy/AmazonS3FullAccess \
  --user-name awsS3user
  
              ===================#  Task 5: Adjust S3 Bucket Permissions
Disabled Block all public access in S3 bucket settings.

Enabled ACLs under Object Ownership.

                ================== #  Task 6: Extract Website Files
Extracted website content archive:

bash
Copy
Edit
cd ~/sysops-activity-files
tar xvzf static-website-v2.tar.gz
cd static-website
ls
Files included:

index.html

css/

images/

    ========================== #  Task 7: Upload Files to S3
Enabled static website hosting:

bash
Copy
Edit
aws s3 website s3://<your-bucket-name>/ --index-document index.html
Uploaded content with public-read ACL:

bash
Copy
Edit
aws s3 cp /home/ec2-user/sysops-activity-files/static-website/ \
  s3://<your-bucket-name>/ --recursive --acl public-read
Verified upload:

bash
Copy
Edit
aws s3 ls <your-bucket-name>
Accessed public static site via bucket website endpoint.

                   ======================#  Task 8: Automate Deployment with a Script
Created batch script:

bash
Copy
Edit
cd ~
touch update-website.sh
vi update-website.sh
Script content:

bash
Copy
Edit
#!/bin/bash
aws s3 cp /home/ec2-user/sysops-activity-files/static-website/ \
  s3://<your-bucket-name>/ --recursive --acl public-read
Made script executable:

bash
Copy
Edit
chmod +x update-website.sh
Edited HTML and ran:

bash
Copy
Edit
./update-website.sh

                      =============== # Optional: Optimize with sync Command
Modified update-website.sh to use sync:

bash
Copy
Edit
#!/bin/bash
aws s3 sync /home/ec2-user/sysops-activity-files/static-website/ \
  s3://<your-bucket-name>/ --acl public-read
Benefits:

Only uploads changed files

Reduces upload time and bandwidth

                            ========================# Outcome
Deployed a public static website to S3.

Implemented user access control using IAM.

                                =======================#Created a repeatable deployment script with optional optimization via sync.

 Project Structure
sql
Copy
Edit
.
├── update-website.sh
├── sysops-activity-files/
│   └── static-website/
│       ├── index.html
│       ├── css/
│       └── images/


