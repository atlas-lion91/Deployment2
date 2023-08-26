<p align="center">

</p>
<h1 align="center">

# Deploying URL Shortener Application using Jenkins and Elastic Beanstalk

August 26, 2023

By: Khalil Elkharbibi - Kura Labs

## Introduction

This documentation guides you through edeploying a URL shortener application using Jenkins pipelines and Elastic Beanstalk. The aim is to offer a comprehensive and easy-to-follow guide for deploying the application.

## Overview

The purpose of this guide is to outline the steps required to build, test, and deploy a URL shortener application. The deployment process involves utilizing Jenkins pipelines and leveraging the capabilities of Elastic Beanstalk for smooth deployment.

The project focuses on implementing continuous integration and delivery (CI/CD) for the Python URL shortener app using Jenkins pipelines. This approach brings multiple advantages:

- Faster feature development due to automated builds and tests.
- Early bug detection and code quality maintenance.
- Reproducible pipeline as the app evolves.
- Seamless integration with GitHub for automatic builds on code changes.
- The deployment takes place on AWS Elastic Beanstalk, offering auto-scaling, load balancing, and managed infrastructure.

## Deployment Steps

### Step 1: Visualizing the Deployment

![Deployment 2 Plan drawio](https://github.com/atlas-lion91/Deployment2/assets/140761974/e78c05f7-0d33-47f1-803d-89ad4e378ca0)

### Step 2: Setting Up the Virtual Environment

1. Create or open an AWS account
2. Launch an EC2 instance using Ubuntu Server OS via AWS Console.
3. SSH into the EC2 instance for configuration.

Jenkins fetches application files from GitHub for building and testing. To enable access from the EC2 server, a GitHub token is generated and provided to the server for authentication.

**Generate GitHub Token**

1. Access GitHub Profile Settings.
2. Navigate to Developer Settings, then Personal Access Tokens.
3. Generate a new token, selecting "Repo" and "Admin:Repo_hook" permissions.

### Steps 3 and 4: Jenkins Builds and Tests

Jenkins automates building, testing, and packaging of the application for deployment. Ensure that the necessary software, including Jenkins, Java, Python, and the "Pipeline Utility Steps" plugin, is installed on a new EC2 instance.

1. Create a Jenkins build for the URL Shortener application from the GitHub repository: [GitHub Repository Link](https://github.com/atlas-lion91/Deployment2.git).
2. Run the build.

**Installation on Ubuntu OS (Staging Server in AWS)**

- Install JAVA package:
  ```bash
  $ sudo apt update
  $ sudo apt-get install fontconfig openjdk-17-jre
  ```

- Install JENKINS package:
  ```bash
  $ curl -fsSL https://pkg.jenkins.io/debian/jenkins.io-2023.key | sudo tee /usr/share/keyrings/jenkins-keyring.asc > /dev/null
  $ echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
  $ sudo apt update
  $ sudo apt-get install jenkins
  $ sudo systemctl start jenkins
  $ sudo systemctl status jenkins
  ```

- Install PYTHON package:
  ```bash
  $ sudo apt update
  $ sudo apt install python3.10-venv
  ```

### Step 5: Secure Copy Files

Securely copy URL Shortener Application Files from Jenkins Server to your local desktop. To establish an SSH tunnel:

1. Generate the public key on your local machine:
   ```bash
   $ ssh-keygen
   $ cd path_to_id_rsa.pub
   $ cat id_rsa.pub
   ```

2. Pass the key to the Virtual Machine's authorized_keys file:
   ```bash
   $ cd ~
   $ cd .ssh
   $ sudo nano authorized_keys
   ```

### Step 6: Deploy to AWS Elastic Beanstalk

1. Create EC2 Role:
   - AWS/IAM/Roles/Create Role/AWS Service/Use Case: EC2/Permissions: "AWSElasticBeanstalkWebTier" & "AWSElasticBeanstalkWorkerTier"/Role Name: Elastic-EC2/Create Role.

2. Create EBS Role:
   - AWS/IAM/Roles/Create Role/AWS Service/Use Case: Elastic Beanstalk/Elastic Beanstalk - Customizable/Role Name: aws-elasticbeanstalk-service-role/Create Role.
     
3. Download source code ZIP from GitHub.
  
4. Create an Elastic Beanstalk environment.
 - AWS/Elastic Beanstalk/Environments/Create Environment/Application Name: Python/Platform: Python 3.9 running on 64bit Amazon Linux 2023/Upload Your code/Version Label: v#/Local File: {downloaded files}/Instance Subnets: us-east-1a/Instance Types: t2.micro.

5. Upload and deploy the ZIP file to the environment.
   
6. Access the running application.


![Screenshot 2023-08-26 001527](https://github.com/atlas-lion91/Deployment2/assets/140761974/ba7f84b4-5182-476f-ba9e-3a0a930c1dd6)

![Screenshot 2023-08-26 001502](https://github.com/atlas-lion91/Deployment2/assets/140761974/08955ce6-1ff1-48cb-ad54-07923e523e53)

  
## Troubleshooting & Errors

While creating the Jenkins pipeline, certain issues were encountered that hindered the seamless progress of the deployment process. Here's a breakdown of the encountered problems and their respective solutions:

### Stalled Build at Packaging Stage

Initially, a challenge arose where the build process became stuck during the packaging of output files. The process failed to complete as expected, impacting the overall workflow.


### Error Log Analysis

Upon investigating the Jenkins logs, an error message surfaced, indicating the following:

```
$ ERROR: Failed to bind to server socket: tcp://0.0.0.0:18050 due to: java.net.BindException: The socket name is already in use
```

### Root Cause Identification

The root cause of this error was traced back to the Jenkinsfile configuration. Specifically, an input step was implemented within the pipeline, designed to pause the process and await manual input. This step, while potentially useful, introduced a roadblock that prevented the automated progression of the deployment.

### Solution: Removing Manual Input Step

The solution to this challenge was to eliminate the unnecessary input step that prompted for manual confirmation. The input step was replaced with an automated mechanism, either removing the step entirely or introducing a time-based waiting mechanism. This modification allowed the packaging stage to complete automatically, without requiring manual intervention.

![Screenshot 2023-08-26 071801](https://github.com/atlas-lion91/Deployment2/assets/140761974/e348fc6b-bd90-49dd-9ab8-2edf90b4a186)


### Resuming Build Progress

With the removal of the manual input step, the build process regained its automated flow. The pipeline was able to proceed smoothly through the stages, including packaging, without encountering any obstructions.

This troubleshooting experience serves as a testament to the importance of streamlining the pipeline and ensuring that each step contributes to a seamless and automated workflow. By identifying and addressing such issues, the overall deployment process becomes more efficient and reliable.


## Improving Deployment Efficiency

To enhance the deployment process further, consider implementing the following optimization strategies:

- **Utilize Docker Containers for Jenkins**: Employing Docker containers for Jenkins offers increased portability and consistency. Containers encapsulate the required dependencies and configurations, ensuring a standardized environment for the pipeline processes.

- **Implement Automated Tests Before Packaging**: Incorporating automated tests within the pipeline prior to packaging the application enhances quality control. This step identifies potential issues early, ensuring that only thoroughly tested code is included in the deployment.

  - **Eliminate Manual Input Steps**: The presence of manual input steps within the pipeline introduces potential delays and human errors. Automating these steps by removing them or replacing them with time-based waiting mechanisms can streamline the deployment process.

- **Harness Infrastructure-as-Code (IaC) with Terraform**: Leveraging Infrastructure-as-Code tools like Terraform simplifies and streamlines the provisioning of AWS resources. With IaC, you can define infrastructure configurations as code, allowing for consistent and repeatable deployments.

- **Integrate AWS CLI for Automated Deployments**: Instead of manually deploying through the AWS console, consider integrating the AWS Command Line Interface (CLI) with Jenkins. This integration enables fully automated deployments to Elastic Beanstalk.

  - **Benefits of AWS CLI Integration**:
    - **Repeatability**: Ensures consistent and repeatable deployments, eliminating human error.
    - **Auditing**: Facilitates tracking of changes made during deployments for improved accountability.
    - **Consistency**: Guarantees that deployments adhere to predefined configurations.
    
  - **Incorporate AWS CLI into Jenkins Pipeline**:
    - Embed AWS CLI commands directly within the Jenkinsfile stages.
    - Integrate the CLI commands into scripts for seamless execution during the pipeline.
    
By embracing these optimization strategies, you can significantly improve the efficiency and reliability of your deployment process. This approach results in streamlined workflows, reduced manual intervention, and enhanced control over the entire deployment lifecycle.

## Conclusion

Following these steps, you can efficiently deploy the URL shortener application using Jenkins and Elastic Beanstalk. The process enhances automation and ensures the accurate deployment of the built and tested application.
