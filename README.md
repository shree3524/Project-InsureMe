 #  $$\color{blue}  \textbf{Project} \ \  \textbf{InsureMe}$$
 

InsureMe was having trouble managing their software because it was all one big piece. </br>
As they grew bigger, it became even harder to manage. <br>

### $\color{orange} \textbf{Requirements}$


#### 1. CI/CD Pipeline: </br>
Jenkins should: </br>

* Check out the latest code from the master branch.
* Compile and test the code to ensure it works correctly
* Package the application into a container using Docker.
* Deploy the containerized application to a preconfigured test server on AWS

With DevOps Approch I used several devops tools such as  <br>

- Git: Managed code changes with version control. </br>
- Jenkins: Automated integration, testing, and deployment processes. </br>
- Docker: Containerized applications for consistency and scalability. </br>
- Ansible: Automated server configuration and infrastructure management. </br>
- AWS: Provided infrastructure for hosting and deploying the application. </br>
 Together, these tools streamlined development, testing, and deployment, ensuring efficient management of the InsureMe project. </br>


# $\color{red} \ \textbf{Steps}$
## $\color{green} \textbf {Tech Stack}$
✓ AWS - For creating ec2 machines as servers and deploy the web application. </br>
✓ Git - For version control for tracking changes in the code files </br>
✓ Jenkins - For continuous integration and continuous deployment  </br>
✓ Docker - For deploying containerized applications </br>
✓ Ansible - Configuration management tools  </br>

## $\color{bluew} \textbf{step 1: Create Infrastructure}$
Create an instance 
1. jenkins-server

### Set Up Master Node
1. ami = ubuntu
2. instance type = t2.medium
3. ports =  22, 8080

![INSTANCE](https://github.com/kajol2699/Project-InsureMe/assets/130952932/f7320304-f0a4-4475-84ee-420e16cc6aca)


![SG](https://github.com/kajol2699/Project-InsureMe/assets/130952932/ec2bbe1f-6464-49af-8cdd-cccd6ccca89e)




   
Take SSH and Connect to Instance
## $\color{bluew} \textbf{step 2: CI/CD Setup}$

### Install Jenkins for Automation:
### Install Jenkins on the EC2 instance to automate deployment: Install Java
```` 	
sudo apt update 

sudo apt install  openjdk-11-jdk
````


````
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \  
https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key 
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
/etc/apt/sources.list.d/jenkins.list > /dev/null   
sudo apt-get update 
sudo apt-get install jenkins   
sudo systemctl start jenkins   
sudo systemctl enable jenkins   
````

Access Jenkins in a web browser using the public IP of your EC2 instance.
$\color{pink}{publicIp:8080}$

![JENKINS-HOME](https://github.com/kajol2699/Project-InsureMe/assets/130952932/eee7b43a-bdc6-44d4-b8ee-d66e101cea21)

### Install Necessary Plugins in Jenkins:

#### Goto Manage Jenkins →Plugins → Available Plugins →

##### Install below plugins:
1.Maven Integration plugin  </br>
2.Docker  </br>
3.Docker Commons  </br>
4.Docker Pipeline  </br>
5.Docker API  </br>
6.docker-build-step  </br>

##### Add tools in  Dashboard->Manage Jenkins-> Tools

![JENKINS-TOOLS](https://github.com/kajol2699/Project-InsureMe/assets/130952932/9e92b0b3-e7db-4257-9287-7d49006d130f)

#### Create Job in Jenkins : go to Dashboard->Item Name

![JOB](https://github.com/kajol2699/Project-InsureMe/assets/130952932/0485ed2e-3795-477a-ac64-508ec8bef2d1)

As soon as the developer pushes the updated code on the GIT master branch, the Jenkins job should be triggered using a GitHub Webhook and Jenkins job should be triggered
In Jenkins Job ->Configuration->choose GitHub hook trigger for GITScm polling





## $\color{bluew} \textbf{step 3: Install  Docker}$
````
sudo apt install docker.io -y
sudo usermod -aG docker jenkins
sudo systemctl restart jenkins
````
Add DockerHub Credentials:

To securely handle DockerHub credentials in your Jenkins pipeline, follow these steps: </br>
Go to "Dashboard" → "Manage Jenkins" → "Manage Credentials." </br>
Click on "System" and then "Global credentials (unrestricted)."  </br>
Click on "Add Credentials" on the left side.    </br>
Choose "Secret text" as the kind of credentials.  </br>
Enter your DockerHub credentials (Username and Password) and give the credentials an ID (e.g., "docker"). </br>
Click "OK" to save your DockerHub credentials. </br>

```groovy
pipeline {
    agent any

    stages {
        stage('code-checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/shree3524/Project-InsureMe.git'
            }
        }
        stage('code-build') {
            steps {
            sh 'mvn clean package'
            }
        }
        stage('docker') {
            steps {
            sh 'docker build -t shree3524/project:p1 .'
            
            }
        }
        stage('Docker-Push') {
            steps {
       	       withCredentials([usernamePassword(credentialsId: 'docker-cred', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
            	sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPassword}"
                sh 'docker push shree0904/project:p1'
           }
        }
      }
      stage('Code-Deploy') {
        steps {
           sh 'docker run -d -p 8089:8081 shree0904/project:p1'
        }
      }
    }
}

```

Build Pipeline:

![image](https://github.com/user-attachments/assets/f1f5a361-2f4b-464e-916f-e7df9dc6d5a8)



**Final Output: node ip:container port**

![Screenshot (268)](https://github.com/user-attachments/assets/d83c5b65-7917-4f4a-80f7-b11636714425)

