# DevOps-Project-with-Jenkins-Maven-SonaQube-Docker-and-EKS
This is a simple CICD project that deploys to kubernetes using jenkins

## Here is the project work flow of how I will build the app

![Screenshot 2024-02-22 135751](https://github.com/mariamo23/Register-app/assets/124802455/3cb5fa71-b5d2-40b1-89d1-c77d8868c139)

# Steps to Follow

## Step 1: Launch an Ubuntu 22.04 t2 large instance 

![Screenshot 2024-02-26 150639](https://github.com/mariamo23/Register-app/assets/124802455/d9201b73-377b-4b60-a4c9-c71c224d25f0)


## Step 2: Go to your Security Group and open Inbound Port 8080, since Jenkins works on Port 8080

![Screenshot 2024-02-22 153607](https://github.com/mariamo23/Register-app/assets/124802455/518d1338-fa04-4cd0-b78a-5ed5c29473c4)


## Step 3: Connect to your console and create a shell script to install Java and Jenkins

```
sudo vi /etc/hostname  # change hostname
sudo init 6 # reboot the system
```

Refer--https://www.jenkins.io/doc/book/installing/linux/

### Install Java and Jenkins

```
vi Jenkins.sh
```

```
#!/bin/bash
sudo apt update 
sudo apt install fontconfig openjdk-17-jre -y # install Java 17
java -version  # check Java version
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian/jenkins.io-2023.key
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update 
sudo apt-get install jenkins -y
sudo systemctl enable jenkins  # enable the Jenkins service to start at boot
sudo systemctl start jenkins # start the Jenkins service
sudo systemctl status jenkins # check the status of the Jenkins service
```

```
sudo chmod 777 Jenkins.sh # this will give Jenkins.sh executable permission
./Jenkins.sh    # this will installl java and jenkins
```

## Step 4:

Copy the public IP address and open on the browser using port 8080.

![Screenshot 2024-02-26 151357](https://github.com/mariamo23/Register-app/assets/124802455/572231bf-053e-48d5-be43-f685f60132ce)

Grab the password using below command

```
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```
![Screenshot 2024-02-26 151440](https://github.com/mariamo23/Register-app/assets/124802455/8c8c2459-15eb-4f11-aa44-4eaa61c11f55)

Install suggested plugins

![Screenshot 2024-02-26 151601](https://github.com/mariamo23/Register-app/assets/124802455/60e0f24e-997f-4fe2-abdf-a4b6ba9d7ade)

Create a user and then save and continue

## Step 5: Install Docker on the server

```
vi Docker.sh
```

```
sudo apt-get update
sudo apt-get install docker.io -y
sudo usermod -aG docker $USER   #my case is ubuntu
newgrp docker
sudo chmod 777 /var/run/docker.sock
```
```
sudo chmod 777 Docker.sh # this will give Docker.sh executable permission
./Docker.sh    
```

## Step 6: Install plugins like Maven, JDK

Go to Manage Jenkins, plugins, available plugins and ...

Install below plugins:

1. Maven integration
2. Pipleline maven integration
3. Eclipse Temurin Installer

![Screenshot 2024-02-26 162615](https://github.com/mariamo23/Register-app/assets/124802455/7c153e41-fb45-4c57-995e-6c654ebeb14f)
![Screenshot 2024-02-26 162926](https://github.com/mariamo23/Register-app/assets/124802455/98739ed1-5692-4e22-af1c-f421140fdf5a)

Go to Manage Jenkins, Tools, maven installation and name it and select install authomatically. Also, go to JDK installation and give it a name and select install authomatically. Click add installer and select install from adoptium.net. Select JDK-17.05+8. Apply and Save

![Screenshot 2024-02-26 163643](https://github.com/mariamo23/Register-app/assets/124802455/9f3bfd63-e756-4bf5-bfd8-bf502af5a7e6)

![image](https://github.com/mariamo23/Register-app/assets/124802455/d9d16313-b898-478e-be5c-c18568937870)



## Step 7: Github integration with Jenkins

Add github credentials to Jenkins. Go to Manage Jenkins --> Credentials --> under stores scoped to jenkins, select global and add credentials.

![image](https://github.com/mariamo23/Register-app/assets/124802455/3a2be149-ff0a-4198-8b30-01ac851eca7f)

![image](https://github.com/mariamo23/Register-app/assets/124802455/808adcd7-c262-4302-9225-ae07a67e9844)


Kind is Username with password

Username: Github username

Password: Token generated from github ( Settings --> Develpers settings --> Personal access token --> Token(classic) --> Generate new token )

ID and description: github

Then click create

## Step 8: Create a sonarqube container (Remember to add 9000 ports in the security group).

'''
docker run -d --name sonar -p 9000:9000 sonarqube:lts-community
'''

![image](https://github.com/mariamo23/Register-app/assets/124802455/4ead278a-03cb-4557-8bcc-7df3f32f5b21)

![image](https://github.com/mariamo23/Register-app/assets/124802455/84394744-ea08-4cbc-a074-ab8b08ab0440)

**8A:** Enter username: admin and password: admin, click on login and change password

![image](https://github.com/mariamo23/Register-app/assets/124802455/25196ad5-f29a-4edb-9331-04c3e1659300)

![image](https://github.com/mariamo23/Register-app/assets/124802455/7c3b84f6-8362-4a61-a507-f3b9303ac5ea)

**8B:** Click on Administration --> Security --> Users --> Click on the 3 dashes under Tokens and Update Token --> Give it a name --> and click on Generate Token

![image](https://github.com/mariamo23/Register-app/assets/124802455/3379e4b5-5031-478f-b828-a2ff12b7684f)

![image](https://github.com/mariamo23/Register-app/assets/124802455/48a905ae-4e87-4be0-bf7d-86d146c7ece2)

![image](https://github.com/mariamo23/Register-app/assets/124802455/3f901209-f4cd-43eb-928f-a0ece44340be)

![image](https://github.com/mariamo23/Register-app/assets/124802455/cb128dd4-a821-4243-baf7-51db888e19a0)

**8C:** Copy Token and goto Jenkins Dashboard --> Manage Jenkins --> Credentials --> add credentials.

![image](https://github.com/mariamo23/Register-app/assets/124802455/2387f03a-c592-4c20-ac74-dcf9a1fc8fc5)

Kind is Secret Text

Secret: Sonarqube token

ID and Description: Sonarqube 

Click on create

![image](https://github.com/mariamo23/Register-app/assets/124802455/9e9fabfa-a96a-43cf-9218-d8c9074458cc)

**8D:** Go to Manage Jenkins --> plugins --> available plugins and ...

Install below plugins:

1. SonarQube Scanner
2. Sonar Quality Gates
3. Quality Gates

**8E:** Add SonarQube server to Jenkins

Go to Manage Jenkins --> System --> search for SonarQube Servers

Click on Add SonarQube

![image](https://github.com/mariamo23/Register-app/assets/124802455/b3b3b8d8-c3c5-42fe-85ac-c59b15cdb76d)

Server URL: Sonarqube server url

Server authentication token: the saved secret token

Apply and save

Go to Manage Jenkins --> tools --> search for SonarQube Scanner installations

![image](https://github.com/mariamo23/Register-app/assets/124802455/94bb6d79-bc9f-47a2-83fc-81e179603c1f)

Select add SonarQube scanner

![image](https://github.com/mariamo23/Register-app/assets/124802455/56ff512d-f2e7-496a-ba97-3430dd27e24c)

Name: SonarQube Scanner

Select install authomatically and then apply and save

**8F:** Add quality gate 

On Sonarqube dashboard, go to Administration --> configuration --> webhooks

![image](https://github.com/mariamo23/Register-app/assets/124802455/247e85d0-f039-452a-9548-79b9b75f85c0)

click create

![image](https://github.com/mariamo23/Register-app/assets/124802455/e9e9da98-1581-4844-ba66-ee5fea0f8bca)

Name: Anything

under the URL section: 

``` 
http://jenkins-public-ip:8080/sonarqube-webhook/
```

![image](https://github.com/mariamo23/Register-app/assets/124802455/46a28803-d71a-4c18-b7d3-3576837ccb32)

then click create

## Step 9: Docker Integration with Jenkins

Go to Manage Jenkins --> plugins --> available plugins and ...

Install below plugins:

1. Docker
2. Docker Commons
3. Docker Pipeline
4. Docker API
5. Docker build step
6. CloudBees DOcker build and publish

Go to Manage Jenkins --> Credentials --> System --> add credentials

Kind: username with password

Username: Docker username

Password: Docker token ( my account --> security --> new access token)

Create

![image](https://github.com/mariamo23/Register-app/assets/124802455/f67e808e-6e7c-4aca-aac3-9a41995f0f1c)

![image](https://github.com/mariamo23/Register-app/assets/124802455/e36d6c2d-e893-4e1d-b60b-1321e94182ee)

Go to your pipeline script and define the variable




