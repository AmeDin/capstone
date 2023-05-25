## Project Overview

In this project, you will apply the skills you have acquired in this course. I have implemented all the knowledge that I have learnt from the Udacity Cloud DevOps Engineer Nanodegree program. In this project I have

Created My infrastructure & Server using the AWS CloudFormation.
Automatated the Server Configurations with Ansible
Simulated a blue/green deployment strategy to deploy the application using K8s pods with port forwarded switch upon green successful deployment

https://github.com/AmeDin/capstone

### Project Files

#### Root

- Dockerfile: Copy from NGINX Hello world and replace it with my index.html
- Makefile: For make instructions. Only updated install for installing hadolint and lint instruction to lint dockerfile

#### Shell Scripts

- create.bat: For triggering aws cloudformation create-stack to install my infrastructure & server

#### Cloud formation

- submissioninfra.yml: Create server modules which includes VPC, InternetGateway, InternetGatewayAttachment, PublicSubnet, PublicRouteTable, DefaultPublicRoute, PublicSubnet1RouteTableAssociation
- submissionserver.yml: Creates EC2 which configures its LBSecGroup, WebServerSecGroup, WebAppLaunchConfig, WebAppGroup
- s-params: Store EnvironmentName=CapstoneProject

#### CircleCI

- config.yml: Stores the instructions for Automation of Server configurations as the pipeline for deployment

#### Ansible

- inventory.txt: Stores the web IP server to access and automate server configurations and deployment instruction
- configure-server.yml: configure-server role Upgrade and Update apt packages as well as installing some dependencies. configure-minikube role install all necessary components to run minikube and simulate own Kubernetes Cluster. This installs Docker, kubectl & minikube
- configure-deploy-blue.yml: Starts minikube and create blue pod by pulling nginx hello image with port 80. It thens port forward to 30000 so that the website can be access via http://<Public IP>:30000
- configure-buffer-time.yml: Just a buffer time of 90s to "Simulate" blue green deployment. This allows me to take screenshot of the blue deployment app before we trigger green pod deployment that will switch the app to the updated version it it triggers.
- configure-deploy-green.yml: Create green pod and switch the port forward to this pod instead.

**The final implementation of the capstone details.**

---

### Lint Check & Build

At first step upon pushing new updates, circle ci will start running "build" step. Since i dont have my own application, i only downloaded the index.html for nginx hello and made some updates to include my name in some of the text. The "build" will first perform lint to check if the Docker file is ok, if it failed the pipeline will terminate.
If lint is ok it will perform a docker build to pull image from nginx hello and replace with my index htm. After that it will upload this image as green to my Docker hub which will be use later to simulate the green deployment.

### Get IP

get-server-ip will be done next. This is to obtain the ec2 IP address and update inventory.txt so Ansible playbook can do its thing

### Configure EC2 as Own Kubernetes Cluster

configure-server will be done next. This step is where the ec2 is being configured to setup Own Kubernetes Cluster with Minikube. It installs all the neccesary dependencies and modules to create Own Kubernetes Cluster. Refer to ansible roles configure-server & configure-minikube for the underlying commands for playbook.

### Blue App

deploy-blue will be done next. Here we start the blue green deployment simulation where we start our minikube and create blue pod. Upon creation of the blue port, we perform port forward for the pod to be handling toward port 30000. This allows us to access the app via http://<Public IP>:30000

### Buffer time

To allow simulation of blue green deployment. buffer-time allow us to capture the blue app version before running green. This is not neccessary in production environment.

### Green App

Finally deploy-green will deploy our build application which was the docker image we push holding our index.html. It will switch the port forwarding into this pod abd access again http://<Public IP>:30000 will be our green application. The title and the footer if the application is changed

We can also add commands to remove the blue pods to delete the all app.

That is all. This Udacity devops program was a great learning experience
