pipeline {
    agent any

    environment {
        AWS_ACCESS_KEY_ID     = credentials('aws_access')
        AWS_SECRET_ACCESS_KEY = credentials('aws_secret')

        TF_VAR_aws_access_key = "${AWS_ACCESS_KEY_ID}"
        TF_VAR_aws_secret_key = "${AWS_SECRET_ACCESS_KEY}"
    }

    options {
        timestamps()
    }

    stages {

        stage('Terraform Init') {
            steps {
                bat 'terraform init'
            }
            post {
                failure {
                    emailext(
                        subject: "❌ FAILED: Terraform Init Stage - Build #${BUILD_NUMBER}",
                        body: """
                        <p>The <b>Terraform Init</b> stage failed.</p>
                        <p>🔗 <a href="${env.BUILD_URL}">View Build Logs</a></p>
                        """,
                        mimeType: 'text/html',
                        to: 'r89510562@gmail.com'
                    )
                }
            }
        }

        stage('Terraform Validate') {
            steps {
                bat 'terraform validate'
            }
            post {
                failure {
                    emailext(
                        subject: "❌ FAILED: Terraform Validate Stage - Build #${BUILD_NUMBER}",
                        body: """
                        <p>The <b>Terraform Validate</b> stage failed.</p>
                        <p>🔗 <a href="${env.BUILD_URL}">View Build Logs</a></p>
                        """,
                        mimeType: 'text/html',
                        to: 'r89510562@gmail.com'
                    )
                }
            }
        }

        stage('Terraform Plan') {
            steps {
                bat 'terraform plan -out=tfplan'
            }
            post {
                failure {
                    emailext(
                        subject: "❌ FAILED: Terraform Plan Stage - Build #${BUILD_NUMBER}",
                        body: """
                        <p>The <b>Terraform Plan</b> stage failed.</p>
                        <p>🔗 <a href="${env.BUILD_URL}">View Build Logs</a></p>
                        """,
                        mimeType: 'text/html',
                        to: 'r89510562@gmail.com'
                    )
                }
            }
        }

        stage('Manual Approval') {
            steps {
                input(message: "✅ Approve to apply Terraform changes?")
            }
        }

        stage('Terraform Apply') {
            steps {
                bat 'terraform apply -auto-approve tfplan'
            }
            post {
                failure {
                    emailext(
                        subject: "❌ FAILED: Terraform Apply Stage - Build #${BUILD_NUMBER}",
                        body: """
                        <p>The <b>Terraform Apply</b> stage failed.</p>
                        <p>🔗 <a href="${env.BUILD_URL}">View Build Logs</a></p>
                        """,
                        mimeType: 'text/html',
                        to: 'r89510562@gmail.com'
                    )
                }
            }
        }

        stage('Fetch EC2 Public IP') {
            steps {
                script {
                    def rawOutput = bat(
                        script: 'terraform output -raw instance_public_ip',
                        returnStdout: true
                    ).trim()

                    env.EC2_PUBLIC_IP = rawOutput
                    echo "Fetched EC2 IP: ${env.EC2_PUBLIC_IP}"
                }
            }
            post {
                failure {
                    emailext(
                        subject: "❌ FAILED: Fetch EC2 IP - Build #${BUILD_NUMBER}",
                        body: """
                        <p>Failed to fetch EC2 public IP.</p>
                        <p>🔗 <a href="${env.BUILD_URL}">View Build Logs</a></p>
                        """,
                        mimeType: 'text/html',
                        to: 'r89510562@gmail.com'
                    )
                }
            }
        }

        stage('Create Deployment Script') {
            steps {
                writeFile file: 'setup.sh', text: '''
#!/bin/bash
sudo yum install -y docker
sudo systemctl start docker
sudo docker pull sakshi1285/my-node-app:latest
sudo docker stop app || true
sudo docker rm app || true
sudo docker run -d --name app -p 5000:5000 sakshi1285/my-node-app:latest
'''
            }
        }

        stage('Deploy Docker App via SSH') {
            steps {
                powershell """
                    \$ip = '${env.EC2_PUBLIC_IP}'

                    scp -i "C:/Users/Suhani/.ssh/technova_key" -o StrictHostKeyChecking=no setup.sh ec2-user@\${ip}:/home/ec2-user/

                    ssh -i "C:/Users/Suhani/.ssh/technova_key" -o StrictHostKeyChecking=no ec2-user@\${ip} "chmod +x setup.sh && ./setup.sh"
                """
            }
            post {
                failure {
                    emailext(
                        subject: "❌ FAILED: Docker Deploy via SSH - Build #${BUILD_NUMBER}",
                        body: """
                        <p>Docker deployment via SSH failed.</p>
                        <p>🔗 <a href="${env.BUILD_URL}">View Build Logs</a></p>
                        """,
                        mimeType: 'text/html',
                        to: 'r89510562@gmail.com'
                    )
                }
            }
        }
    }

    post {
        failure {
            emailext(
                subject: "❌ FAILED: TechNova Pipeline Build #${BUILD_NUMBER}",
                body: """
                <p><b>TechNova</b> pipeline failed at some stage.</p>
                <p>❗ Build #: ${BUILD_NUMBER}</p>
                <p>🔗 <a href="${env.BUILD_URL}">View Logs and Details</a></p>
                """,
                mimeType: 'text/html',
                to: 'r89510562@gmail.com'
            )
        }
    }
}
