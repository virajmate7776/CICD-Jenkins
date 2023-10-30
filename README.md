
# End to End CICD Pipeline for Java Application on Kubernetes Cluster

<br>
<br>
<br>
<br>
<br>

![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/32a17fff-6111-4aa1-a760-e3ef53f5cca9)

 

### Pre-requisites :

•	Git

•	Jenkins

•	Docker

•	Docker Hub Account

•	Ansible 

•	Maven

•	Sonarqube

•	Kubernetes 






### Create 3 EC2 instances 
<br>
•	Jenkins 

•	Ansible 

•	Kubernetes 
<br>
We need to create 2 instances with instance type t2.micro and 1 with t2.medium for Kubernetes cluster.

<br>
<br>

 ![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/7a42f156-a647-4d0e-bda5-b3efb13d9d2b)

<br>

Now Connect to Jenkins server via ssh using putty.

 <br>

![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/0ec64f24-641c-45c1-b901-7050dc3c64bd)


<br>

![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/0dab92ff-f52e-4242-95d7-fc0e91e128a2)

 
<br>

### Install Jenkins using following commands.
 
  	wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add –

  	sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'

  	sudo apt update

        sudo apt install Jenkins
 <br>     
 Start the Jenkins and check the status 

        sudo systemctl start Jenkins
        sudo systemctl status Jenkins



 

![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/7a923f18-2cb2-4988-9f20-f99c460005c5)

 

<br>




Run cat command with the file location to see the password in the file 
<br>
 ![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/6201511b-637e-4520-908d-b7b017fc0629)

<br>
Copy and paste the password in the Jenkins page and click on continue.

![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/605e9498-0914-48d1-b5c3-11b109b19ce1)

 
<br>
![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/e41783c9-0bbe-4430-8b72-88652aa639ed)

<br>
 
![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/a49cff1c-64a5-4006-b939-d436295a000c)


 <br>

### Now install Ansible on same server.
<br>
Add Ansible repository
     
    sudo apt-add-repository ppa:ansible/ansible

Now fetch latest update & install Ansible

    sudo apt update
   
    sudo apt-get install ansible -y

Now check Ansible version
   
    ansible --version
    

![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/ea41c678-f01a-4aec-9f6a-df92f7caab44)

 

### Now install maven on same server.
<br>
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

<br>

 ![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/d404cad0-0df6-4f26-93c8-f0dfc2621b17)

<br>

Now connect to Sonarqube server via ssh using putty.
   Pre-requisites for sonarqube

•	Jenkins 

•	Java

  Install Java
     
     apt-get install openjdk-17-jdk -y       
     
     apt-get install openjdk-11-jdk -y       

      java -version         

<br>

### Install Sonarqube
  <br>
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
<br>
    useradd -d /opt/sonarqube sonar
    cat /etc/passwd | grep sonar
    ls -ld /opt/sonarqube
    chown -R sonar:sonar /opt/sonarqube
    ls -ld /opt/sonarqube


Create custom service for sonar
   <br>
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
    <br> 
    systemctl start sonarqube.service
    Service enable & check status
    systemctl enable sonarqube.service
    systemctl status sonarqube.service

Check 9000 port is used or not
<br>
    apt install net-tools
    netstat -plant | grep 9000

Open sonarqube on browser
  <br>
    URL:   http://<sonarqube_ip>:9000

U: admin
P: admin
New Pass: admin@123



 

  
![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/94ed8954-c3de-4140-bf7c-b28863c7cb80)

<br>
 
![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/e879661f-f412-470c-8ca6-6c23a00d4401)


<br>

![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/ea85609b-21a1-4ae2-97df-d41068678ccb)

 <br>
 
![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/bfdb81e3-8e62-474f-b187-fa1f236c9e21)

<br>
 Now connect the Kubernetes server via ssh using putty.


<br>

### Install Docker with the command 
<br>
    sudo apt-get install -y \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common

Add Dockers official GPG key
  <br>
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add –

Add Docker Repo	
  <br>
    sudo add-apt-repository \
    "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
    $(lsb_release -cs) \
    stable"
<br>
Install the latest version of Docker Engine and containerd
   <br>
    sudo apt-get install -y docker-ce docker-ce-cli containerd.io
<br>
Check the installation (and version) by entering following command
   <br>
    docker --version
<br>
The product_uuid can be checked using following command
<br>
    
    sudo cat /sys/class/dmi/id/product_uuid

Set Docker to launch at boot by entering the command 
    <br>
    sudo systemctl enable docker

Verify Docker is running
  <br>
    sudo systemctl status docker

Add Kubernetes Repo
<br>
    {
     curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
     echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" >                 /etc/apt/sources.list.d/kubernetes.list
    }
<br>
### Install kubeadm kubelet kubectl 
<br>
    apt update && apt-get install -y kubelet=1.21* kubeadm=1.21* kubectl=1.21*  
               
    sudo apt-mark hold kubelet kubeadm kubectl

Verify the installation with kubeadm
<br>
    kubeadm version

    kubectl version --short
<br>
Initialize the Kubernetes on Master Node
  <br>
    sudo kubeadm init --pod-network-cidr=10.244.0.0/16

Enter the following to create a directory for the cluster: To start using your cluster, you need to run the following as a regular user

    sudo mkdir -p $HOME/.kube
    sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
    sudo chown $(id -u):$(id -g) $HOME/.kube/config
<br>
Now check to see if the kubectl command is activated
<br>
    kubectl get nodes

<br>
Deploy Pod Network to cluster
  <br>
    sudo kubectl apply -f 
    
    https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml

Verify that everything is running and communicating
   <br>
    kubectl get pod --all-namespaces
<br>
Cross check your cluster is running or not 
     
     kubectl get nodes

Remove taints from K8-master node
<br>
    kubectl taint nodes k8-master node-role.kubernetes.io/control-plane:NoSchedule-            

OR

    kubectl taint nodes k8-master node-role.kubernetes.io/master:NoSchedule-                   
<br>

### KUBERNETES CLUSTER TESTING
<br>
 Check pod status
    
    kubectl get pod


 ![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/aa912475-f60b-44a9-8c4a-dc39cb2afc51)

<br>

 ![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/f5464d23-437f-494b-bf6a-9001e7d235bf)


### Configure Jenkins Pipeline job.
   <br>
    Login Jenkins > New Item > project-1 > Pipeline > OK
          Pipeline:
              Definition: Pipeline script from SCM
          SCM: Git
          Repositories:
             Repository URL: https://github.com/virajmate7776/CICD-Jenkins.git
           Script Path: Jenkinsfile
           Click on Save.

 
<br>
 


 
![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/aec0cc10-8807-4292-b4bb-f7d80d38bb3f)


 
<br>


![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/e87012ae-982f-4cd8-8d71-4d5a5dfc4893)

<br>

![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/f9fe4815-c48d-46fc-95d7-13689841be37)


<br>

![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/803dd350-181a-4fc8-87b6-cdd15453be3d)

<br>
### Jenkinsfile stage for creating workspace in Jenkins.

 ![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/fb9e3462-da19-47c1-a477-c1dd994f2315)

<br>
 Click on Build Now to create a job.

 <br>
![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/3c2794b3-68c4-4970-89d5-c241158d2e7d)

<br>


Now it will create a folder called project-1 in the Jenkins workspace

<br>
 ![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/66f8c822-181c-4e61-a535-b16b9ca2f97a)

<br>
Add stage ‘Modified Image Tag’ in jenkinsfile and commit the changes. 
<br>
 ![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/06cfb1cf-80e7-4f41-b28a-1a2c79a29a5b)

<br>
Click on Build Now it will run the build and it will show the build on stage view.

 <br>

![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/cb98abdc-67cd-4df9-88c9-5fe1b43849fd)

<br>
Add another stage to the Jenkinsfile it will create build and add .war file

 
<br>

![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/f53a3d2a-24c4-4786-a5fb-ba0771205c0f)


<br>

### Jenkins integration with Sonarqube server.
    
    Login to Sonarqube server
        Sonarqube > My Account > Security > Generate Tokens
          Name: porject-1
          Type: Global Analysis Token
          Expires: 30 Days
          Generate
       After that copy token & save it.


 <br>

![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/4f909980-e066-4d05-816b-6aad4d5591ff)


<br>

![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/43b8a881-589a-4bdc-a991-a26d9897ef9c)

 <br>

### Go to Jenkins and create credential for sonar token.
        
         Dashboard > Manage Jenkins > Credentials > System Global credentials (unrestricted) > Add credentials > 
                 kind: Secret text
                 Scope: Global
                 Secret: ******
                 ID: SONAR_TOKEN
                 Des: SONAR_TOKEN
         Create

<br>

 ![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/5267589f-ccdf-42a7-94c1-f7d1e2a387c1)


Add a Stage Sonarqube for security check in the Jenkinsfile.

 
<br>


![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/17b3b5f4-2970-416e-b0de-9a1fdec0b90e)


<br>


The sonar stage is built successfully.

<br>

 ![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/77a721a5-0a9a-431e-857c-95b5a84bd95f)


Now it will show passed status in front of project-1. It means there is no duplications, vulnerabilities in the code. 

<br>

 ![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/ec8e2122-d9c5-483f-8d92-f0a7ada9347c)

<br>

### Configure inventory file & Password less authentication with Kubernetes server.
   
   On Kubernetes server execute following commands.
      
      passwd root
      cp -r /etc/ssh/sshd_config /etc/ssh/sshd_config_orig
      sed -i "s/#PermitRootLogin prohibit-password/PermitRootLogin yes/g"   /etc/ssh/sshd_config
      sed -i "s/PasswordAuthentication no/PasswordAuthentication yes/g" /etc/ssh/sshd_config
       systemctl restart sshd.service



 ![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/38e0f1e6-63e2-4704-bf59-e22923b8080b)

<br>

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

 
<br>
 

![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/560cfd30-cc03-406f-86b5-a0a5b5f39185)

 <br>


 ![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/11be3f12-82ec-4b40-9398-fa5218f95621)

<br>

 ![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/7cf717f3-a684-43ad-b455-567fddd700b4)

<br>

![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/29b338ab-3014-4686-aa30-ec7fe517e407)

<br>

![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/c454eb77-bbff-4e50-b03d-9c49cf88fcfe)

<br>

 Now create ssh connection with Kubernetes server without password using command 
    
      ssh root@<kubernetes_ip>
<br>

 ![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/94c2c146-031a-4357-88ed-f02d6aa456ee)
<br>

Now check whether the servers are responding or not on ansible server using command 
     
     ansible -m ping kubernetes -u root

 
<br>

![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/1ff3eb34-a239-4d8f-b5cf-c5ed6e734aec)

<br>


### Create credential for Dockerhub server login.

<br>
         Dashboard > Manage Jenkins > Credentials > System Global credentials (unrestricted) > Add credentials > 
           kind: Secret text
           Scope: Global
           Secret: ******
           ID: DOCKERHUB_USER
           Des: DOCKERHUB_USER
      Create

<br>
 

![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/f733d5e7-4a66-4083-aff5-ae4cf6827972)

<br>

![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/f99d6a27-3921-45a3-a9ed-f1479caf7faa)

 <br>

Now add new stage for coping Jar & Dockerfile in the Jenkinsfile.
<br>

 ![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/57b5b96b-9323-438a-8755-f62ece52f6e9)

<br>
Click on Build Now
     It will create build and we can see the stage view.

<br>

 ![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/abe3f205-2197-4007-9e96-a6e4ff934289)


After successfully completing build it will copy a webapp.war and Dockerfile on kubernetes server
In the directory  /opt/docker

 <br>


![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/d512466c-a06d-4fe8-a6ef-9373c1b38e25)


<br>

Now add new stage to push image on Dockerhub

 
<br>

![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/4aac9a2c-5586-4a5c-91a6-6128ddaca876)

<br>

![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/ca6bd7fd-ac07-455f-85a1-4b137f763e1f)

 

<br>




We can see the image is pushed on Dockerhub

 

<br>
 

![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/63b8f805-701e-4333-a952-b3a21edb339a)


<br>


Now add new stage to deploy on Kubernetes cluster 


 
<br>


![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/0dd04e3a-350a-4b3b-a34e-bacbd9ef252e)

 <br>


![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/3643ab0a-33f9-4f99-957e-df268d165ce3)

<br>
Now the latest version of image is pushed on Dockerhub with version no

<br>

 ![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/04547b27-3352-4164-8730-fe6f2a91dcc3)

<br>

We can see our deployment is successful and pods are running on Kubernetes server


 <br>


 ![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/7eddc4ae-2d8d-4738-9fa0-dabeb83c5d0a)

<br>

 ![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/aec15b3e-173a-449b-b572-cc866b061ee2)


<br>


![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/aa9afde3-0ee2-4497-b727-88014864727f)


<br>


Now copy and paste the public ip address of the Kubernetes cluster on browser and append the port no 30000/webapp/

<br>

  We can see the webapp is running. 
  
<br>

 ![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/91c537ce-f063-4daa-ba41-5963412b4881)

<br>

### Github integration with Jenkins.
<br>

    Github > Repository > Settings > Webhooks > Add Webhooks > 
    Payload UR   : http://<jenkins_ip>:8080/github-webhook/
    Content type : application/json


 ![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/ff8744f0-3857-44d1-83c5-33421753935d)
 
<br>

On Jenkins server					

   
     Jenkins > project-1 > Configure > Build Triggers > 
          GitHub hook trigger for GITScm polling



 ![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/1aab9d0d-bf89-42fa-bb79-f5f896cbeb8a)

<br>

 ![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/b6fcaec4-32ac-42ab-ac55-b513e190077c)



When developer make the changes in the code repository, the changes will get reflected immediately.
The changes has been made in the code repository and we can see the result.

<br>

![image](https://github.com/virajmate7776/CICD-Jenkins/assets/117629972/7cf3436d-7f83-497b-a3cc-64b69d396a55)
<br>
