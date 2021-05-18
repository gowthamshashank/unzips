# unzips

1) Installing Minikube:
     
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

2)  Create one container in docker with image nginx

    Step:-1  Download docker on your local system
             
             $ sudo apt-get install docker.io

    Step:-2  Login to docker Hub

             $ docker login
 
    Step:-3  Pull the docker image from the docker hub

             $ docker pull nginx     

    Step:-4  Run the image

             $ docker run -p 8080:80 nginx

    Step:-  Go to browers and serach

            https://localhost:8080    


3)  Create a simple application in docker image and push it to the docker hub repo
 
    Step:-1  Install docker on the system
     
             $ apt-get install docker.io

    Step:-2  run the command 

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
 
    Step:-1 Deploy a Spring Boot application on Kubernetes and expose actuator endpoints
        
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

   Step:-2  Install Helm, The package manager for Kubernetes 

            $ curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3

            $ chmod 700 get_helm.sh

            $ ./get_helm.sh                      
    
   Step:-3  Create separate namespace for Monitoring
    
            $ kubectl create namespace monitoring


    Step:-4  Deploy Prometheus using Helm Chart

             $ helm install stable/prometheus --namespace monitoring


             --->> check if prometheus is running or not using following command :

             $ kubectl get pods -n monitoring

    Step:-5  Deploy Grafana using Helm Chart

             Create a config.yaml file with the following content:-

             config.yml:

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

             Create a values.yaml file with the following content:-

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

            To know the password run the following command :-
            
            $ kubectl get secret –namespace monitoring grafana -o jsonpath=”{.data.admin-password}” | base64 –decode ; echo

            Now let’s do port-forward for accessing grafana:-

            $ export POD_NAME=$(kubectl get pods –namespace monitoring -l “app=grafana,release=grafana” -o jsonpath=”{.items[0].metadata.name}”)

            $ kubectl –namespace monitoring port-forward $POD_NAME 3000

            Go to https://localhost:3000 in your browser. You should see the Grafana login screen:

                        (or)
                  https://ipaddress:3000

          ----->>>>  Login with the username (admin) and password you have from the previous command.                 

