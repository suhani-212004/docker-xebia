
The setup ensures Infrastructure as Code (IaC), reproducible builds, and automated deployment with manual approval gates and email notifications for failures.

📁 Project Structure



technova/

│

├── Dockerfile

├── main.tf

├── variables.tf

├

├── Jenkins file

│   

├──package.json           

├──index.js

│   

└─ README.md

└─ .gitignore



📊 Architecture Diagram



Developer --> GitHub --> Jenkins --> Terraform --> AWS EC2

                                   |

                              Docker Image

                                   ↓

                             Deployed Web App



🌐 GitHub Repository



🔗 https://github.com/reshavgovindam/technovaaa

🛠️ Tech Stack



ToolPurposeTerraformProvision AWS infrastructureAWS EC2Host Jenkins and your appJenkinsAutomate CI/CD pipelineDockerContainerize and deploy appGitHubVersion control & repo hosting

⚠️ Prerequisites



⚙️ Prerequisites Before running this project, ensure the following tools, software, and cloud infrastructure are ready and properly configured:

✅ AWS Setup AWS account with:

IAM user having EC2, VPC, S3 permissions

Access key and secret key stored in Jenkins credentials (aws_access, aws_secret)

Key Pair for EC2 SSH (technova_key)

✅ Accounts & Cloud Infrastructure

RequirementDescriptionAWS AccountMust have programmatic access enabled (IAM credentials for AWS CLI)Ubuntu EC2 InstanceDeployed via Terraform or manually on AWSInbound Rules- Port 5000: App access

- Port 8080: (Optional) Jenkins UI

- Port 22: SSH

✅ Required Software

Install the following tools either locally (for testing) or on the EC2 instance (for CI/CD pipeline operations):

ToolVersionNotesDocker>= 20.10Required to build and run containerized applicationsTerraform>= 1.0.0For provisioning AWS infrastructure via codeJenkinsAny LTSFor setting up CI/CD pipelines; install on EC2AWS CLILatestMust be configured using aws configure with IAM credentialsNode.jsOptionalRequired only if your app uses Node.js backend or frontend

🛠️ CI/CD Pipeline Overview



Jenkins Pipeline Stages:



Clone Repository

Clones project from GitHub

Build Docker Image

Builds app image using Dockerfile

Terraform Init/Plan/Apply

Provisions EC2 and networking resources

Deploy Docker Container

Runs container on EC2

🔠 Website Files



The website is placed under:

├── main

 ├── index.js

 ├── package-lock.json

 └── package.json



🔧 Setup Instructions



1. 📦 Terraform Infrastructure Provisioning



Initialize and apply Terraform:

INITIALIZE TERRAFORM

terraform init

CREATE AN EXECUTION PLAN(SAVES A FILE NAMED tfplan)

terraform plan -out tfplan

APPLY CHANGES TO THE FILE

terraform apply tfplan



⚖ Terraform Configuration



Terraform files are in main/main.tf/ and main/variable.tf

├── main

 ├── main.tf

 ├── variable.tf



Resources Created:



VPC

Subnet (Private and/or Public)

Internet Gateway

Route Table

Security Group (allowing port 22 and 5000)

EC2 Instance (Ubuntu-based)

Terraform Commands Used:



terraform init

terraform validate

terraform plan -out=tfplan

terraform apply tfplan



🚀 Docker Image Creation



To create the Docker image:

Write your Dockerfile (in main/Dockerfile).

main/

│

├── Dockerfile



Build the image:

docker build -t my-node-app



Run the container:

docker run -d -p 5000:5000 my-node-app



📈 Jenkins Pipeline Overview



The Jenkins pipeline is defined in main/Jenkinsfile.

main/

│

├── Jenkinsfile



Pipeline Stages:



Init - Initialize Terraform backend

Validate - Terraform validation

Plan - Infrastructure planning

Approval - Manual approval before applying

Apply - Provision EC2 via Terraform

SSH EC2 - SSH into instance to prepare environment

Docker Build - Build Docker image

Docker Run - Run image with exposed port 5000

📷 Jenkins Pipeline Screenshots

Init Stage:
<img width="901" height="635" alt="image" src="https://github.com/user-attachments/assets/09026f89-86c6-4a70-865d-46aea2217ac5" />
Validate Stage:
<img width="887" height="640" alt="image" src="https://github.com/user-attachments/assets/4a35702a-1858-41bb-b492-93ad75289241" />
Plan Stage:
<img width="853" height="596" alt="image" src="https://github.com/user-attachments/assets/e057b82a-70b5-433f-8744-91228e26c564" />
Apply Stage:
<img width="873" height="447" alt="image" src="https://github.com/user-attachments/assets/524e5568-207e-4588-8417-c9df937d30df" />
SSH & Docker Deployment:
<img width="851" height="422" alt="image" src="https://github.com/user-attachments/assets/aacff79c-3b5a-404e-b68b-c43a7a1bb591" />
2. 🔑 Connect to EC2 & Install Jenkins
SSH into your EC2 instance:

ssh -i technova_key.pem ec2-user@your-ec2-public-ip
Install Jenkins using the following:

sudo yum update -y
sudo yum install java-11-openjdk -y
wget -O /etc/yum.repos.d/jenkins.repo \
https://pkg.jenkins.io/redhat-stable/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
sudo yum install jenkins -y
sudo systemctl start jenkins
sudo systemctl enable jenkins
🚩 Deployed Output
Accessing the Deployed Application Once deployed, the application will be available at:

visit this deployed application

📸 Final Deployment Screenshot
<img width="875" height="579" alt="image" src="https://github.com/user-attachments/assets/e03e8441-24f7-47bd-8f1a-38d6f60f0946" />
🔮 Future Improvements
✅ Slack / Webhook Notifications
✅ Monitoring with Prometheus & Grafana
✅ Push Docker images to DockerHub or ECR
✅ Load Balancer & Auto Scaling via Terraform
✅ Add Unit Testing & Code Coverage






