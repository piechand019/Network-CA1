# Network-CA1
Assingment CA1 2024

**AWS Terraform Infrastructure & CI/CD Pipeline for Flask App**
________________________________________

**Project Overview**
This repository automates the provisioning of AWS infrastructure and deployment of a Flask application using Terraform, Docker, and GitHub Actions. It demonstrates a robust CI/CD pipeline that builds, pushes, and deploys a Dockerized Flask app on AWS EC2 instances.
________________________________________
**Features**
**1.	AWS Infrastructure:**
o	Provisioned using Terraform.
o	Includes VPC, Subnets, EC2 instances, Security Groups, and more.
**2.	Flask Application:**
o	A simple web app hosted in a Docker container.
**3.	CI/CD Pipeline:**
o	GitHub Actions automates Docker image building, pushing, and deployment.
**4.	Configuration Management:**
o	Ansible playbook for Docker installation on EC2 instances.
________________________________________

**Repository Structure**
├── app.py               # Flask application code
├── main.tf              # Terraform configuration file
├── requirements.txt     # Python dependencies for Flask
├── Dockerfile           # Docker image configuration
├── pipeline.yml         # GitHub Actions workflow
├── install_docker.yml   # Ansible playbook to install Docker on EC2
└── README.md            # Project documentation
________________________________________
**File Details**
**Application Files**
•	**app.py:** A Flask application serving a simple HTML page.
#using python
from flask import Flask
app = Flask(__name__)
@app.route('/')
def home():
    return '''<html>
                <head>
                <title>CI/CD</title>
                </head>
                <body>
                <h1>Hello World, We have successfully completed the task. From Chandni, Avi and Himanshu.</h1>
                </body>
                </html>'''

if __name__ == '__main__':
    app.run(debug=True, host='0.0.0.0', port=5000)

•	**requirements.txt**: Specifies Flask as a dependency:
Flask==2.3.3
________________________________________

**Infrastructure as Code**
•	main.tf: Contains Terraform configurations to provision AWS resources:
o	VPC, Subnet, Internet Gateway, Security Groups.
o	EC2 instances for hosting the Docker containers.
o	SSH key pair for secure access.
________________________________________

**Containerization**
•	Dockerfile: Describes the steps to containerize the Flask application:
# dockerfile
FROM python:3.9-slim
WORKDIR /app
COPY . .
RUN pip install --no-cache-dir -r requirements.txt
EXPOSE 5000
ENV FLASK_APP=app.py
CMD ["flask", "run", "--host=0.0.0.0", "--port=5000"]

________________________________________
**CI/CD Pipeline**
•	pipeline.yml: GitHub Actions workflow for CI/CD:
o	Build: Dockerizes the Flask app and pushes the image to Docker Hub.
o	Deploy: Pulls the image to an AWS EC2 instance and runs the container.
________________________________________

Configuration Management
•	install_docker.yml: An Ansible playbook to install Docker on EC2 instances:
---
- name: Install Docker and configure it to start on boot
  hosts: all
  become: yes
  tasks:
    - name: Install required dependencies for Docker
      apt:
        name:
          - apt-transport-https
          - ca-certificates
          - curl
          - software-properties-common
        state: present
        update_cache: yes
    - name: Add Docker GPG key
      apt_key:
        url: https://download.docker.com/linux/ubuntu/gpg
        state: present
    - name: Add Docker APT repository
      apt_repository:
        repo: deb [arch=amd64] https://download.docker.com/linux/ubuntu jammy stable
        state: present
    - name: Install Docker
      apt:
        name: docker-ce
        state: latest
        update_cache: yes
    - name: Enable Docker to start at boot
      systemd:
        name: docker
        enabled: yes
        state: started
________________________________________
**Setup Instructions**

**Prerequisites**
1.	AWS CLI installed and configured with appropriate IAM credentials.
2.	Terraform installed on your machine.
3.	Docker installed locally for testing.
4.	Ansible installed for configuration management.
5.	A GitHub repository with secrets:
o	DOCKER_USERNAME: Your Docker Hub username.
o	DOCKER_PASSWORD: Your Docker Hub password.
o	EC2_PRIVATE_KEY: SSH private key content for EC2.
o	EC2_HOST: Your Host Details for EC2.
________________________________________
**Steps to Run**
**1. Provision AWS Infrastructure**
•	Initialize Terraform: terraform init
•	Validate configuration: terraform validate
•	Apply configuration: terraform apply
•	Note the public IP of the EC2 instance from the Terraform output.
**2. Build and Test Docker Locally**
•	Build the Docker image:
docker build -t <docker_username>/app:latest .
•	Run the container locally:
docker run -p 5000:5000 <docker_username>/app:latest
**3. Set Up CI/CD Pipeline**
•	Push the code to GitHub.
•	Ensure the pipeline.yml file is in .github/workflows/.
•	The pipeline will:
o	Build and push the Docker image to Docker Hub.
o	Deploy the container to the EC2 instance.
**4. Access the Flask App**
•	Open the browser and navigate to:
http://<EC2_Public_IP>:5000

________________________________________
**Expected Output**
When you access the app, the page will display:
# html code
Hello World, We have successfully completed the task. From Chandni, Avi and Himanshu.

________________________________________
**Future Improvements**
1.	Restrict Security Group rules for better security.
2.	Add automated monitoring using AWS CloudWatch.
3.	Implement auto-scaling for the EC2 instances.
________________________________________

**Conclusion**
This project showcases the integration of Terraform, Ansible, Docker, and GitHub Actions to create a scalable and automated cloud-based deployment pipeline. By using Infrastructure as Code (IaC), containerization, and CI/CD practices, we ensure a robust, repeatable, and efficient workflow for deploying applications on AWS.
With this setup, one can:
•	Easily provision cloud infrastructure.
•	Seamlessly deploy containerized applications.
•	Maintain a consistent and secure deployment pipeline.
The project serves as a foundation for building more complex applications and pipelines, making it an excellent starting point for anyone interested in DevOps, cloud computing, and modern software development practices.
