# unzips

1) Installing Minikube:
     
      In Windows:

        To install chocolety:

           Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))

        Install virtual box:

           PS C:\Windows\system32> choco install virtualbox
    
        Install kubectl on Windows

           curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.18.0/bin/windows/amd64/kubectl.exe

        Add kubectl command in environment variable.
    
        Install minikube

           choco install minikube

           minikube start --driver=virtualbox

           minikube start --kubernete-version v1.12.0 -- to install specific version

           minikube status

           minikube ssh

           kubectl run myshell --rm -it --image busybox -- sh

           minikube config view

           minikue stop

           minikube delete

           delete .minikube directory

           sudo rm -rf /usr/loca/bin/minikube

        In Mac:
     
           brew install minikube

           Start your cluster:

           minikube start
    
           Install Kubectl:

           brew install kubectl

2)  Create one container in docker with image nginx

    Step:-1  Download docker on your local system
             
             CMD:- sudo apt-get install docker

    Step:-2  Login to docker Hub

             CMD:- docker login
 
    Step:-3  Pull the docker image from the docker hub

             CMD:- docker pull nginx     

    Step:-4  Run the image

            CMD:- docker run -p 8080:80 nginx

    Step:-  Go to browers and serach

            https://localhost:8080                               
