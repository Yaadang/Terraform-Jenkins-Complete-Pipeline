# CI/CD with Terraform, Jenkins, and Docker

This repository demonstrates a robust CI/CD pipeline setup using Terraform for infrastructure management, Jenkins for continuous integration, and Docker for containerization.

## Project Structure and Functionality:

### Jenkins Pipeline (`Jenkinsfile`)

The pipeline encompasses both CI and CD.

- **CI Steps**:
  1. **Build App**: Constructs the Java Maven application, resulting in a JAR artifact.
  2. **Build Image**: Creates a Docker image from the JAR artifact and pushes it to DockerHub.

- **CD Steps**:
  1. **Provision Server**: Uses Terraform to set up an AWS EC2 instance. The required AWS credentials are securely stored and accessed within Jenkins using global credentials.
  2. **Deploy**: Waits briefly for the EC2 instance to initialize, then deploys the Docker container onto it. We pass the dockerhub credentials from Jenkins to the EC2 server and pull the image.

### Terraform Configuration (`main.tf`)

Terraform is used to describe and provide data center infrastructure using declarative configuration files.

- **Infrastructure Provisioning**:
  1. **VPC & Subnet**: Sets up a Virtual Private Cloud (VPC) and associated subnets in AWS for resource isolation.
  2. **Routing**: Establishes internet gateway and routing tables to enable communication between the subnet and external networks.
  3. **Security**: Defines security group rules to manage inbound and outbound traffic. SSH access is permitted from Jenkins and your IP.
  4. **EC2 Instance**: Provisions an EC2 instance with Amazon's latest Linux AMI. Uses an entry script (`entry-script.sh`) to initialize Docker-related configurations and installations.

### Docker Configuration (`Dockerfile`)

- **Base Image**: Uses `openjdk:8-jre-alpine` for a lightweight Java runtime.
- **Exposure**: The app inside the container listens on port 8080.
- **Application**: The Java Maven application's JAR file is copied to the container and set as the entry point, ensuring it runs when the container starts.

### Deployment Script (`server-cmds.sh`)

This Bash script streamlines the deployment process:

1. **Docker Login**: Authenticates with Docker using provided credentials from Jenkins
2. **Run Containers**: Uses Docker Compose to initiate and manage multi-container Docker applications, effectively spinning up our application in a containerized environment at once.

## Usage:

1. **Jenkins Setup**:
    - Generate SSH key pairs for securing the EC2 instance.
    - Configure necessary credentials in Jenkins.
    - Ensure Terraform is installed inside the Jenkins container for infrastructure provisioning.

2. **Repository Configuration**:
    - Integrate Terraform configurations to outline the desired infrastructure.
    - Modify the Jenkinsfile to handle both provisioning and deployment stages seamlessly.

3. **Pipeline Execution**:
    - On triggering the pipeline, the Java Maven application is constructed, dockerized, and the resultant image is stored on DockerHub.
    - An AWS EC2 instance is then provisioned using Terraform, where the application is deployed and made accessible.
