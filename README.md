# unzips

Installing Minikube:
     
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