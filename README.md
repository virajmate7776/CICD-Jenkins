
# End to End CICD Pipeline for Java Application on Kubernetes Cluster

![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/32a17fff-6111-4aa1-a760-e3ef53f5cca9)

 

Pre-requisites :

•	Git

•	Jenkins

•	Docker

•	Docker Hub Account

•	Ansible 

•	Maven

•	Sonarqube

•	Kubernetes 






Step 1: Create 3 EC2 instances 

•	Jenkins 

•	Ansible 

•	Kubernetes 

We need to create 2 instances with instance type t2.micro and 1 with t2.medium for Kubernetes cluster.



 ![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/7a42f156-a647-4d0e-bda5-b3efb13d9d2b)



Now Connect to Jenkins server via ssh using putty.

 

![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/0ec64f24-641c-45c1-b901-7050dc3c64bd)





 


 Install Jenkins using following commands.
 
  	wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add –

  	sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'

  	sudo apt update

    sudo apt install Jenkins

Start the Jenkins and check the status 

	  sudo systemctl start Jenkins

	  sudo systemctl status Jenkins



 


 






Run cat command with the file location to see the password in the file 

 

Copy and paste the password in the Jenkins page and click on continue.


 



 


 

Now install Ansible on same server.

Add Ansible repository
     
     sudo apt-add-repository ppa:ansible/ansible

Now fetch latest update & install Ansible

    sudo apt update
   
    sudo apt-get install ansible -y

Now check Ansible version
   
    ansible --version


 

Now install maven on same server.

Check version before install
   
    mvn --version

Change dir to /opt and download maven
    
    cd /opt/
    ls

wget https://dlcdn.apache.org/maven/maven-3/3.9.1/binaries/apache-maven-3.9.1-bin.zip
  
    apt-get install unzip -y
   
    unzip apache-maven-3.9.1-bin.zip
    ls
    rm -rf apache-maven-3.9.1-bin.zip
    ls

Configure maven home path
    
    vim ~/.bashrc

Add end of the file & save it.
    export M2_HOME=/opt/apache-maven-3.9.1
    export PATH=$PATH:$M2_HOME/bin

    source ~/.bashrc

Check version again now
   
    mvn --version
   
    mvn --help


 


Now connect to Sonarqube server via ssh using putty.
   Pre-requisites for sonarqube

•	Jenkins 

•	Java

  Install Java
     
     apt-get install openjdk-17-jdk -y       
     
     apt-get install openjdk-11-jdk -y       

      java -version         



Install Sonarqube
  
    cd /opt/
    wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-   10.0.0.68432.zip
   
   OR
    
    wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-8.9.2.46101.zip

    apt install unzip -y
 
    unzip sonarqube-10.0.0.68432.zip
   
   
    ls
    rm -rf sonarqube-10.0.0.68432.zip
    mv sonarqube-10.0.0.68432 sonarqube
    ls

Create sonar user

    useradd -d /opt/sonarqube sonar
    cat /etc/passwd | grep sonar
    ls -ld /opt/sonarqube
    chown -R sonar:sonar /opt/sonarqube
    ls -ld /opt/sonarqube


Create custom service for sonar
   
    cat >> /etc/systemd/system/sonarqube.service <<EOL
   [Unit]
   Description=SonarQube service
   After=syslog.target network.target

   [Service]
   Type=forking
   User=sonar
   Group=sonar
    PermissionsStartOnly=true
     ExecStart=/opt/sonarqube/bin/linux-x86-64/sonar.sh start 
    ExecStop=/opt/sonarqube/bin/linux-x86-64/sonar.sh stop
    StandardOutput=syslog
    LimitNOFILE=65536
     LimitNPROC=4096
     TimeoutStartSec=5
     Restart=always

[Install]
WantedBy=multi-user.target
EOL

    ls -l /etc/systemd/system/sonarqube.service

Open port 9000 from firewalld OR security group
9000
Service start
     
    systemctl start sonarqube.service
    Service enable & check status
    systemctl enable sonarqube.service
    systemctl status sonarqube.service

Check 9000 port is used or not

    apt install net-tools
    netstat -plant | grep 9000

Open sonarqube on browser
  
    URL:   http://<sonarqube_ip>:9000

U: admin
P: admin
New Pass: admin@123



 

  


 


 


 Now connect the Kubernetes server via ssh using putty.




Install Docker with the command 

    sudo apt-get install -y \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common

Add Dockers official GPG key
  
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add –

Add Docker Repo	
  
    sudo add-apt-repository \
    "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
    $(lsb_release -cs) \
    stable"

Install the latest version of Docker Engine and containerd
   
    sudo apt-get install -y docker-ce docker-ce-cli containerd.io

Check the installation (and version) by entering following command
   
    docker --version

The product_uuid can be checked using following command
    
    sudo cat /sys/class/dmi/id/product_uuid

Set Docker to launch at boot by entering the command 
    
    sudo systemctl enable docker

Verify Docker is running
  
    sudo systemctl status docker

Add Kubernetes Repo

    {
     curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
     echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" >                 /etc/apt/sources.list.d/kubernetes.list
    }

Install kubeadm kubelet kubectl 

    apt update && apt-get install -y kubelet=1.21* kubeadm=1.21* kubectl=1.21*  
               
    sudo apt-mark hold kubelet kubeadm kubectl

Verify the installation with kubeadm

    kubeadm version

    kubectl version --short

Initialize the Kubernetes on Master Node
  
    sudo kubeadm init --pod-network-cidr=10.244.0.0/16

Enter the following to create a directory for the cluster: To start using your cluster, you need to run the following as a regular user

    sudo mkdir -p $HOME/.kube
    sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
    sudo chown $(id -u):$(id -g) $HOME/.kube/config

Now check to see if the kubectl command is activated

    kubectl get nodes


Deploy Pod Network to cluster
  
    sudo kubectl apply -f 
    
    https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml

Verify that everything is running and communicating
   
    kubectl get pod --all-namespaces

Cross check your cluster is running or not 
     
     kubectl get nodes

Remove taints from K8-master node

    kubectl taint nodes k8-master node-role.kubernetes.io/control-plane:NoSchedule-            

OR

    kubectl taint nodes k8-master node-role.kubernetes.io/master:NoSchedule-                   


KUBERNETES CLUSTER TESTING

 Check pod status
    
    kubectl get pod


 


 

Configure Jenkins Pipeline job.
   
    Login Jenkins > New Item > project-1 > Pipeline > OK
          Pipeline:
              Definition: Pipeline script from SCM
          SCM: Git
          Repositories:
             Repository URL: https://github.com/virajmate7776/CICD-Jenkins.git
           Script Path: Jenkinsfile
           Click on Save.

 

 


 


 








Jenkinsfile stage for creating workspace in Jenkins.

 

 Click on Build Now to create a job.

 




Now it will create a folder called project-1 in the Jenkins workspace

 

Add stage ‘Modified Image Tag’ in jenkinsfile and commit the changes. 

 

Click on Build Now it will run the build and it will show the build on stage view.

 


Add another stage to the Jenkinsfile it will create build and add .war file

 





Jenkins integration with Sonarqube server.
    
    Login to Sonarqube server
        Sonarqube > My Account > Security > Generate Tokens
          Name: porject-1
          Type: Global Analysis Token
          Expires: 30 Days
          Generate
       After that copy token & save it.


 




 

Go to Jenkins and create credential for sonar token.
        
         Dashboard > Manage Jenkins > Credentials > System Global credentials (unrestricted) > Add credentials > 
                 kind: Secret text
                 Scope: Global
                 Secret: ******
                 ID: SONAR_TOKEN
                 Des: SONAR_TOKEN
         Create


 

Add a Stage Sonarqube for security check in the Jenkinsfile.

 







The sonar stage is built successfully.

 

Now it will show passed status in front of project-1. It means there is no duplications, vulnerabilities in the code. 


 


Configure inventory file & Password less authentication with Kubernetes server.
   
   On Kubernetes server execute following commands.
      
      passwd root
      cp -r /etc/ssh/sshd_config /etc/ssh/sshd_config_orig
      sed -i "s/#PermitRootLogin prohibit-password/PermitRootLogin yes/g"   /etc/ssh/sshd_config
      sed -i "s/PasswordAuthentication no/PasswordAuthentication yes/g" /etc/ssh/sshd_config
       systemctl restart sshd.service


 


On Ansible server execute this commands.

     cat /etc/ansible/hosts
     > /etc/ansible/hosts
    cat /etc/ansible/hosts

     vim /etc/ansible/hosts

    [kubernetes]
    <kubernetes_ip>

    cat /etc/ansible/hosts
    su - jenkins
    ansible -m ping kubernetes -u root
    ssh root@<kubernetes_ip>
    ssh-keygen
    ssh-copy-id root@<kubernetes_ip>
    ssh root@<kubernetes_ip>

     ansible -m ping kubernetes -u root

 

 

 

 

 

Now create ssh connection with Kubernetes server without password using command 
    
      ssh root@<kubernetes_ip>

 

Now check whether the servers are responding or not on ansible server using command 
     
     ansible -m ping kubernetes -u root

 





Create credential for Dockerhub server login.

         Dashboard > Manage Jenkins > Credentials > System Global credentials (unrestricted) > Add credentials > 
           kind: Secret text
           Scope: Global
           Secret: ******
           ID: DOCKERHUB_USER
           Des: DOCKERHUB_USER
      Create


 



 

Now add new stage for coping Jar & Dockerfile in the Jenkinsfile.

 

Click on Build Now
     It will create build and we can see the stage view.

 

After successfully completing build it will copy a webapp.war and Dockerfile on kubernetes server
In the directory  /opt/docker

 





Now add new stage to push image on Dockerhub

 




 






We can see the image is pushed on Dockerhub

 


 





Now add new stage to deploy on Kubernetes cluster 


 



 



Now the latest version of image is pushed on Dockerhub with version no


 


We can see our deployment is successful and pods are running on Kubernetes server


 

 

 








Now copy and paste the public ip address of the Kubernetes cluster on browser and append the port no 30000/webapp/ 
  We can see the webapp is running. 

 

Github integration with Jenkins.


    Github > Repository > Settings > Webhooks > Add Webhooks > 
    Payload UR   : http://<jenkins_ip>:8080/github-webhook/
    Content type : application/json

 

On Jenkins server					

   
     Jenkins > project-1 > Configure > Build Triggers > 
          GitHub hook trigger for GITScm polling


 

 


When developer make the changes in the code repository, the changes will get reflected immediately.
The changes has been made in the code repository and we can see the result.



