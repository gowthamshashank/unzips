# unzips

1) Installing Minikube:

      Minikube Start:-

       Minikube is local Kubernetes, focusing on making it easy to learn and develop for Kubernetes.
 
       All you need is Docker (or similarly compatible) container or a Virtual Machine environment, and Kubernetes is a single command away: 
       
       minikube start



      What you’ll need?
      
        2 CPUs or more
      
        2GB of free memory
       
        20GB of free disk space
      
        Internet connection
      
        Container or virtual machine manager, such as: Docker, Hyperkit, Hyper-V, KVM, Parallels, Podman, VirtualBox, or VMWare

     
      In Windows:


        To install chocolety:

           $ Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]   ::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))

        Install virtual box:

           PS C:\Windows\system32>  $ choco install virtualbox
    
        Install kubectl on Windows

           $ curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.18.0/bin/windows/amd64/kubectl.exe

        Add kubectl command in environment variable.
    
        Install minikube

           $ choco install minikube

           $ minikube start --driver=virtualbox

           $ minikube start --kubernete-version v1.12.0 -- to install specific version

           $ minikube status

           $ minikube ssh

           $ kubectl run myshell --rm -it --image busybox -- sh

           $ minikube config view

           $ minikue stop

           $ minikube delete

           $ delete .minikube directory

           $ sudo rm -rf /usr/loca/bin/minikube

        In Macos:
     
           $ brew install minikube

           Start your cluster:

           $ minikube start
    
           Install Kubectl:

           $ brew install kubectl

2) Create one container in docker with image nginx
    
    This tutorial shows how to deploy Nginx in a Docker container.

      Prerequisites:-

      To containerize Nginx, please complete the following:
    
      Set up an Ubuntu 18.04 or 20.04 server, preferably with SSH keys for security
    
      Switch to the root user by clinkin the command because docker will run on the root user 

      $ sudo su

    Follow these steps:--

    Step:-1  Download docker on your local system
             
             $ sudo apt-get install docker.io

    Step:-2  Login to docker Hub

             $ docker login
 
    Step:-3  Pull the docker image from the docker hub 

             $ docker pull nginx     

    Step:-4  Run the image on the port which u have given this will run the nigix on the given port

             $ docker run -p 8080:80 nginx

    Step:-  Go to browers and serach

            https://localhost:8080    


3)  Create a simple application in docker image and push it to the docker hub repo

    This tutorial shows how to deploy simple application on a  Docker container.

      Prerequisites:-

      To containerize , please complete the following Steps:
    
      Set up an Ubuntu 18.04 or 20.04 server, preferably with SSH keys for security
    
      Switch to the root user by clinkin the command because docker will run on the root user 

      $ sudo su

    
    Follow these steps:--

    Step:-1  Install docker on the system
     
             $ apt-get install docker.io

    Step:-2  run the command to the create a centos image and which will run on the bash or zsh or sh

             $ docker run -it --name centos centos /bin/sh

    Step:-3  Create an image from this running container:

             $ docker commit <container_id/container_name> <docker_id/image_name:tag> 

    Step:-4  login to docker hub account
     
             $ docker login

    Step:-5  Push the image to the docker hub

             $ docker push your_docker_id/ image_name:tag> 

    Step:-6  Delete the image in the local

             $ docker rm -f$(docker ps -aq)                 

4)  How  to monitoring spring boot application with prometheus and grafana on kubernets cluster   

     
     ![Screenshot-from-2020-10-07-12-00-04](https://user-images.githubusercontent.com/52821412/118772682-a573b080-b8a1-11eb-86c3-fbf8ccb7fc92.png)

     
    Step:-1 Deploy a Spring Boot application on Kubernetes and expose actuator endpoints
        
            In Kubernetes environment , we can configure annotations which will be used by prometheus to scrap data.Below is the complete deployment.yaml fileI

            Docker image which i have spring boot application "shashank3656/spring-boot-prometheus" 

            spring-boot-prometheus-deployment.yaml:-
            
            apiVersion: apps/v1
            kind: Deployment
            metadata:
              name: spring-boot-prometheus
            spec:
              selector:
                matchLabels:
                   app: spring-boot-prometheus
               replicas: 1
               template:
                 metadata:
                   labels:
                      app: spring-boot-prometheus
                  annotations:
                    prometheus.io/scrape: "true"
                    prometheus.io/port: "8080"
                    prometheus.io/path: "/actuator/prometheus"
                 spec:
                   containers:
                     - name: spring-boot-prometheus
                       image: brainupgrade.in/spring-boot-prometheus:0.0.1-SNAPSHOT
                       imagePullPolicy: Always
                  ports:
                    - containerPort: 8080
                  resources:
                    limits:
                      memory: 294Mi

    Step:-2  Install Helm, The package manager for Kubernetes :

            Helm makes it extremely easy to make sure you use up to date versions of Prometheus & Grafana & also makes it a-lot easier to deploy and delete.

            Helm now has an installer script that will automatically grab the latest version of Helm and 
            "https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3"

            You can fetch that script, and then execute it locally. It’s well documented so that you can read through it and understand what it is doing before you run it.

            $ curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3

            $ chmod 700 get_helm.sh

            $ ./get_helm.sh                      
    
    Step:-3  Create separate namespace for Monitoring:

             Time to monitor

             it’s always good idea to keep related things together, We will create separate namespace in Kubernetes for monitoring and will deploy all monitoring related application under that namespace.

             Create monitoring namespace with following command
    
            $ kubectl create namespace monitoring


    Step:-4  Deploy Prometheus using Helm Chart:
             
             ---->>> With the help of Helm, We can deploy prometheus using single command.
             
             $ helm install stable/prometheus --namespace monitoring


             --->> check if prometheus is running or not using following command :

             $ kubectl get pods -n monitoring

    Step:-5  Deploy Grafana using Helm Chart:-
             
             When deploying grafana, we need to configure it to read metrics from the right data sources.

             There are two ways of achieving this.

             Deploy Grafana & add the data source afterwards through the UI.

             Add the data source as yaml configs & deploy Grafana. Grafana will use these to automatically configure the data sources when it is provisioned. We are going to take this path as we want everything to be replicate-able without too much manual intervention.

             Grafana takes data sources through yaml configs when it starts up. For more information see here: http://docs.grafana.org/administration/provisioning/#datasources

             When the Grafana Helm chart gets deployed, it will search for any config maps that contain a grafana_datasource label. So we will add one in our config. That is as follow 

             Create a config.yaml file with the following content:-

             config.yml:-

              apiVersion: v1
              kind: ConfigMap
              metadata:
                name: prometheus-grafana-datasource
                namespace: monitoring
                labels:
                  grafana_datasource: '1'
              data:
                datasource.yaml: |-
                apiVersion: 1
                datasources:
                 - name: Prometheus
                   type: prometheus
                   access: proxy
                   orgId: 1
                   url: http://prometheus-server.monitoring.svc.cluster.local

             Override Grafana value:-

             When Grafana gets deployed and the provisioner runs, the data source provisioner is deactivated. We need to activate it so it searches for our config maps.

             We need to create our own values.yml file to override the datasources search value, so when Grafana is deployed it will search our datasource.yml definition and inject it.

             Create a file called values.yml with the following contents:

             values.yaml:-

             sidecar:
               image: xuxinkun/k8s-sidecar:0.0.7
               imagePullPolicy: IfNotPresent
               datasources:
                 enabled: true
                 label: grafana_datasource  

          --->>>Apply Config map & Grafana deployment:-

          $ kubectl apply -f config.yml                 

          $ helm install stable/grafana -f values.yml –namespace monitoring –name grafana 
            (If your having any issue with this command remove value.yaml and simply install grafana we will add dashboard manually in grafana.)

          $  helm install stable/grafana –namespace monitoring

             And Check that it is running using command :

          $ kubectl get pods -n monitoring 

    Step:-6 Get the Grafana Password

            Grafana is deployed with a password. This is good news. But whats the password ?

            To know the password run the following command :-
            
            $ kubectl get secret –namespace monitoring grafana -o jsonpath=”{.data.admin-password}” | base64 –decode ; echo

            Now let’s do port-forward for accessing grafana:-

            $ export POD_NAME=$(kubectl get pods –namespace monitoring -l “app=grafana,release=grafana” -o jsonpath=”{.items[0].metadata.name}”)

            $ kubectl –namespace monitoring port-forward $POD_NAME 3000

            Go to https://localhost:3000 in your browser. You should see the Grafana login screen:

                        (or)
                  https://ipaddress:3000

          ----->>>>  Login with the username (admin) and password you have from the previous command.                 


5) How to setup scalable jenkins on top of a kubernetes cluster and pipeline for automated ci cd (jenkins - github integration)

  Introduction

    DevOps encourages collaboration, cooperation, and communication between developers and operations teams to improve the speed and quality of software development. One of the key principles of DevOps is automation, which reduces human error, provides consistent results, and even mitigates risks. With the help of automation, you and your team can build, test, and deploy software quickly and efficiently.

    In this tutorial, you’ll learn how to achieve a fully automated environment with Jenkins on Kubernetes.

    Learning objectives

   In this tutorial, you will:

   ->Set up a Jenkins environment on Kubernetes

   ->Configure a CI/CD Jenkins pipeline

   ->Build Docker images using Jenkins

   ->Push Docker images to a Docker registry

   ->Deploy Docker images to a Kubernetes environment

   ->Integrate Jenkins

   ->Integrate GitHub and Jenkins using GitHub Tokens 


  https://github.com/shashank3656/scaling-jenkins/tree/master/scalling-jenkins/master

  Step:-1 Download this is folder from github

  Step:-2 $ git clone https://github.com/shashank3656/scaling-jenkins/tree/master/scalling-jenkins/

  Step:-3 build a docker image for jenkins

            $ cd scalling-jenkins

            $ docker build -t shashank3656/jenkins .

  Step:- 4 push the docker image to docker hub repo

             $ docker push shashank3656/jenkins


  Step:-5 First lets create namespace with name jenkins

            $ kubectl create ns jenkins

  Step:-6 Now lets set namespace to jenkins by below command

            $ kubectl config view | grep namespace
            
            $ kubectl config set-context --current --namespace=jenkins

  Step:-7 Then apply deploymet and service which are present in master folder, if you want persistent jenkins use files present in master-Persistent

            $ kubectl create -f .

  Step:-8 check jenkins-master pod is up and running or not

             $ kubectl get pods -o wide

  Step:-9 as you can see jenkins-master pod is running on kube-node-1 so to access jenkins get node ip and service port 

            https://kube-node-1_ip:service_port

  Step:-10 Once after jenkins up and running, navigate to Manage Jenkins --> Configure System ( make sure usage set to 
             "only build jobs with label expresions    matching this node" ) --> cloud        
              
              ---->>>>  In Configure cloud page select kubernetes

              ---->>>>  Configure Kubernetes details and Pod templates by clicking on each one of them

              --->>>>>  get kubernetes url by running kubectl cluster-info get url and click on test connection.  
                        after clicking on it, you should see Connected   Kubernetes

              --->>>>   if you face any problem in this step checkout https://issues.jenkins.io/browse/JENKINS-55788 or
                        https://stackoverflow.com/questions/47973570/kubernetes-log-user-systemserviceaccountdefaultdefault-cannot-get-services

                        Next get service url and provide the same in jenkins. Save it

              --->>>>  Next configure pod template and container details as show in below picture

  Step:-11 Set Up Global Credentials

             To test the docker commands especially login, first set up the global credentials        

  Step:-12 After create jenkins pipeline:

             Create Build Job

             Create a docker-test job as a pipeline, click OK, and insert the below text in the pipeline block:   

             pipeline {

                  agent {

                      kubernetes{

                          label 'jenkins-slave'

                      }

                   }

                   environment{

                       DOCKER_USERNAME = ''

                       DOCKER_PASSWORD = credentials('')

                   }
                   stages {

                       stage('docker login') {

                            steps{

                                sh(script: """

                                    docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD

                               """, returnStdout: true) 

                              }

                         }

                      }

                   }      
         

  Step:-13  Test the Pipeline

              Run the docker-test job and you'll see that a pod will be launched by Jenkins master to run the docker-test build job; the pod will be terminated immediately once the build completes.     

              Once the build job completes, the build pod is terminated.


  Step:-14 Install Plugin
              Also, install the below plugin.

              GitHub pull request builder

              Github Setup

              Now generate an access token using https://github.com/settings/tokens/new (select repo:status)


              Create a webhook using your Github repo settings

              Insert https://<Your_Jenkins_Public_URL>/ghprbhook/

              Event trigger—Select individual events (Pull requests, Issue comments) 

              so that whenever any pull request is created, Github can notify your Jenkins URL         

  Step:-15 Jenkins Setup
         
             Create Jenkins Credentials (type: Secret text) using the above access token say github-bu-token

             Configure the GitHub Server here http://localhost:8080/configure.


6) create a r base container on docker and run any r script on it.
   
   Docker and R:-

    There are many different R and RStudio Docker images.

    Official R images can be found here: https://hub.docker.com/_/r-base

    RStudio and Shiny server images can be found here: https://hub.docker.com/u/rocker

    An image can be downloaded using the docker pull command. Alternatively, when docker run is used if the image is not found on your local system it will be downloaded
    

  Step:-1  To install the r server  using docker image 
     
             $  docker run -detach -p 8002:8787 -e PASSWORD=YOUR_PASSWORD –name my-rstudio  YOUR_DOCKER/IMAGE_NAME .

  There are a few things to note, namely the flags used in the run command:

                -detach or -d for short, runs the container in the background of your terminal.

                --publish or -p forwards the port on your machine to the container’s port. This is important for running RStudio (port 8787) and/or a Shiny server 
                (port 3838) as a container. It is also important when running multiple containers.

                --name gives a name to your container. This is not necessary but it’s useful for managing containers (stopping, removing, etc..).

                -e is used for environment variables. In this case the variable PASSWORD needs to be provided, any string can be chosen as the password.

  Step:-2  To check the containers currently running on your machine use docker ps --all, you should see similar output to below.

             $ docker ps -all

  Step:-3  https://localhost:port_id 

  Step:-4  Push to docker hub repo

             $ docker push  YOUR_DOCKER/IMAGE_NAME

7) Study the tutum-hello-world and push docker hub and change the image with your logo.


   Step:-1  Create Docker for tutum-helloworld
            
              $ docker run -t Your_dockername/image_name .

   Step:-2   After creating the image exec the image and run the command:

              $ cd /var/www/html

              $ ls

              $ nano index.html

              $ <a href=”index.html”><img src=”img/logo.png” alt=”logo” />

 
   Step:-3  After that run the apache2  command  :

             $ systemctl start apache2

   Step:-4  https:ip_address:8080


   Step:-5  Push the image to docker hub

             $ docker push docker_hub_name/image_name


8) Connect from your local machine to a postgresql database in docker

   Docker makes it very easy to spin up a PostgreSQL database management system. With the following command it is possible to start your PostgreSQL Docker container on your server or local machine:


  Step:-1  Pull the postgresql image from docker hub
     
             $ docker pull postgresql


  Step:-2  U have given the host and port numbers username password

              $ docker run -d –name dev-postgres -e  POSTGRES_PASSWORD=Pass2021! -v $
                {HOME}/postgres-data/:var/lib/postgresql/data/ -p 5432:5432     postgres      

      This command will start a PostgreSQL database and map ports using the following pattern: -p <host_port>:<container_port>. 
      Port 5432 of our container will be mapped on port 5432 of our host or server.
                

  Step:-3  Now exec the postgressql with docker cmd which we have created the contaired

             $  docker exec -it dev-postgres bash
             $  psql -h localhost -U postgres

  Step:-4  Now start the pgadmin  instance

             $ docker pull dpage/pgadmin4

  Step:-5 now run the created container

            $ docker run -p 80:80 -e ‘PGADMIN_DEFAULT_EMAIL=user@domain.local’ -e ‘PGADMIN_DEFAULT_PASSWORD=SuperSecret’ --name  dev-pgadmin -d dpage/  pgadmin4               

  Step:-6 check the container is created or not 
     
            $ docker ps

  Step:-7 we will need to look the ipaddress of postgres using docker inspect command

            $ docker inspect dev-postgres -f “{{json .NetworkSettings.Networks}}”

  Step:-8  https://ipaddress:port


9) Try to run docker in docker container and push to docker hub?

    Step:-1  pull the image from docker hub

             $ docker pull docker

    Step:-2  change the name of the image of docker using tag

            $ docker tag imageid –name docker_hubname/image_name
             
            $ docker run -it docker_hubname/image_name  

    Step:-4 push the image to the dokcer hub

            $ docker push docker_hub_name/image_name        
         
     




         

