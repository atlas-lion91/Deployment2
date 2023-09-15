# Purpose:

The purpose of our app is to transform long URLs into concise, user-friendly links. This encourages sharing and usability. We use Jenkins to enforce deployments with no errors, while AWS Beanstalk makes sure our application is always available by managing and auto-scaling our infastructure. These tools give us the potential to solve a user engagement related business problems and optimize our workflow.

  #### *URL Shortener:*
  ##### URLs can be too long and not user-friendly sometimes. A URL shortener solves this by converting long URLs into shorter, more manageable URLs. This creates more user engagement because smaller URLs are more usable and more likely to be shared. Our tradeoff is that shortened URLs are associated with viruses and bad websites but users are becoming more accustomed to seeing shortened URLs and we'll get more engagement.

  #### *Jenkins:*
  ##### Manual build, test and deployment processes have more human-error and cost more time. We can use Jenkins to automate the building, testing and deployment of our application. Jenkins will reduce our risk of human-error and speed up our ability to continuiously intergrate and deploy our application. The tradeoff is that Jenkins could have a learneing curve but that investmnent of time to learn it will lower the cost of, and time to, build and deploy our app.

  #### *AWS Beanstalk:*
  ##### Managing the infrastructure for continuous application deployment can time consuming and complex. And the more complex it is, the more it will cost. AWS Beanstalk handles our applications infrastructure management. This allows us to focus on coding and implementing features while still ensuring our application will scale and be available for our users.

# Issues:
### *Saving Jenkins username and password:*

##### Be sure to save you Jenkins login information somewhere because you’ll need to log back into your Jenkins account later on. You can bypass having to login by navigating to the  Jenkins directory by using 
````
cd /var/lib/jenkins/
````

##### In here, it should be a config.xml file. Open that file using 
````
sudo nano config.xml
````

##### When the file is open look for *<useSecurity>true</useSecurity>*. Change the word 'true' to 'false'. This will temporarily bypass the use of a login page to use Jenkins

### *Installing the Pipeline Utility Steps plug-in to Jenkins*

##### Be sure to install the Pipeline Utility Steps plug-in right after we’ve finished installing Jenkins. We won’t be able to successfully build out application in Jenkins because this plugin because this plugin is what allows our Jenkins to work with zip files

### *Sending Application Zip File From EC2 to Local Machine*

##### Make sure port 22 on you local machinr is open and functional for SSH traffic. This will allow you to send files from your EC2 to you local machine simply using the "scp" command. If that cant be done, we can simply clone the repo onto our EC2, copy the file into the cloned repo directory from its Jenkins console output log location and push the changes to update our repo. We can then clone the repo again, but this time, onto our local machine. Now, we will have a the zip file available on our local machine to be uploaded for our AWS Beanstalk deployment This is all outlined in step 4 below.

# Steps:
## 1. [Create a new GitHub repo and download the application files from the source repo](https://github.com/djtoler/URL-Shortener-Deployment2/blob/main/Deployment2DownloadUploadFiles.md)
## 2. [Upload files to the new GitHub you created](https://github.com/djtoler/URL-Shortener-Deployment2/blob/main/UploadFilesToGitHub.md)
## 3. [Set Up Jenkins with Python on an EC2 instance & run the Jenkins Build](https://github.com/djtoler/URL-Shortener-Deployment2/blob/main/Deployment2JenkinsMarkdown.md)
## 4. [Extract the zip file that Jenkins will create](https://github.com/djtoler/URL-Shortener-Deployment2/blob/main/ExtractZipFromJenkins.md)
## 5. [Deploy the application on AWS Beanstalk](https://scribehow.com/shared/How_to_Create_and_Deploy_a_Python_URL_Shortener_on_AWS_Elastic_Beanstalk__MS9pB8lfRaGFiKAq2FU-cw) 

## Your successful deployment in the AWS Beanstalk console should look like this...
| GitHub Version                      | 1.0.0.1 Zip File Version                               |
| ----------------------------------- | ----------------------------------- |
| ![aaaaaa.png](https://github.com/djtoler/URL-Shortener-Deployment2/blob/main/Success%20Screenshots/BeanstalkSuccessHealthOk.PNG) | ![aaaaaa.png](https://github.com/djtoler/URL-Shortener-Deployment2/blob/main/Success%20Screenshots/BeanstalkSuccessHealthOk1.0.0.1.PNG) |

# System Diagram:
![a.png](https://github.com/djtoler/URL-Shortener-Deployment2/blob/main/Deployment2Diagram2.drawio.png)

# Optimization:
<aside>
✅ To optimize this deployment, I would do several things. 

1. I would automate the creation or our GirHub repo and the downloading of our application files using a bash script using [these commands I've tried](https://github.com/djtoler/firstDeploymentAutomated)
2. I would automate the build set up of our Jenkins pipeline and the installation of Jenkins and Python on our EC2 instance. I would use a bash script for this too.
3. I would use a bash script to extact the appocation zip file that Jenkins makes after a successful build. I've tested these commands and they work
   ````
   #store the output of out command in a variable named "zip_file_path". Use the "grep" command to find the zip file if its stored in its usual location of "var/lib/jenkins/workspace/" and search for it in "/var/lib/jenkins/jobs/Deployment2/builds/1/log"
   
   zip_file_path=$(grep -o '/var/lib/jenkins/workspace/[^ ]*.zip' /var/lib/jenkins/jobs/Deployment2/builds/1/log)
   
   #copy the file into our URL shortner app directory using the "cp" command.
   
   cp "$zip_file_path" /home/ubuntu/URL-Shortener-Deployment2/
   
   #navigate into the folder where the file should have been copied to
   
   cd /home/ubuntu/URL-Shortener-Deployment2/
   
   #check and make sure the file has been copied
   
   ls
   ````
4. Finally, I would automate the enviornment configuration and deployment of our AWS Beanstalk application using the AWS CLI

</aside>
