# Assignment

Please find below the solutions to the problems given part of the assignment

# Challenge 1

Symbiosis is a health product manufacturing company and currently on-premises infrastructure. They have recognized the benefits of moving to a cloud infrastructure & would like to evaluate an AWS cloud solution.

Based on their priorities and internal discussions, they have provided you with some high-level requirements which they would like you to implement in the proposed solution. The high-level requirements are as follows:

  - A private isolated network which would best suit Symbiosis’s 2 tier architectural needs. In order to meet their internal SLA’s they   require a highly available solution as well.

  - Symbiosis being a B2C company, would typically like their web applications to be accessible over the internet and thus handle HTTP     traffic.

  - The database tier should have restricted access (not open to HTTP) and allow traffic only through the web tier. The database tier     should allow outbound requests via NAT.

  - They would like to reduce the administrative burden of managing their SQL database and requires a managed database for their SQL       engine in the proposed solution. They need the database to be highly available.

  - Currently they experience medium to high traffic on their network. The traffic to the web tier is managed by a load balancer which      diverts traffic to healthy instances. They would ideally like a Load Balancer with an ability to perform layer 4 (Transport Protocol)    and layer 7 (Application) checks while balancing the load. There is no requirement at this point to balance the load on the database    tier.

  - In their current setup, the traffic being inconsistent, requires over provisioning resulting in increased costs. In order to            overcome this issue, they would like the new system to allow automatic scaling in the event of a traffic spike.

## Solution - AWS Architecture:

![picture](https://github.com/bhaskarsconsortium/assignment/blob/master/AWS%20Architecture%20-%20Final.jpg)

The Above architecture encapsulates all the needs that were required by Symbiosis. While architecting the solution, giving emphasis to the cost, have desinged a bare minimum deployement which would suffice the requirements of the cusotmer and have called out a few elements as optional. These services in the architecture will improve the customer experience largely and will also help us with the cost optmization of the larger deployment. A brief explanation of the architecture given below.

  - A simple VPC configuration of 4 Subnets - 2 in one availablity Zone(1 Public and 1 Private Subnets) and 2 in another availablity      Zone((1 Public and 1 Private Subnets) to provide high availablity of the resources created in them. 
  
  - The routing is designed in such a way that the databases receive the traffic only from the Web Applications using the commbo of route tables and security gorups. A NAT Gateway could be used to route the outbound trafiic from the Databases deployed in the Private Subnets
  
  - The web Applications is to be deployed in EC2 Instances part of an Auto Scaling Group attached to a application load balancer with the Auto scaling Group serving as the target group for the load Balancer. The Load balancer health checks are tied to this Target group so that the traffic is routed to the healthy instances only.
  
  - The MySQL databases is to be deployed using the Relational Databases(RDS) Service in a Mutli AZ Configuration. The service being a managed service will bring down the administrative efforts required for the maintanance of the database by many fold. Symbosis being a health product manufacturer could also look at the RDS read replicas to route their read only content so that it does not affect the performance of the database for other operations. This is called out in the architecture as an optional service for the customer to consider based on requirement
  
  - The Identity and Access Management(IAM) service is to be used to manage the Access to this entire environment.
  
  - The Loadbalancer is to be configured to receive traffic based on the Public Hosted zone configuration using the Route 53 service.
  
  - Other Optional services and their uses :
  
    - A Cloud Front distribution network could be used to make use of the edge locations and deliver the web applications to the customers with near zero lattency. The cloud Front distribution could also be used to deliver digital and dynamic content also seamlessly
    
    - The Glacier service of Simple storage service(S3) could be used to backup and archive all the data that are required for long term storage. The S3 could also be used to host static web sites that could be utilized during the maintanance of the Web Applications.
    
    - The Cloud watch Service could be used to create metics for this entire deployment to not only understand the health of it but also to understand the utilization of the different services. This would help the customer make capacity calls.
    
    - The Cloud formation service could be used to automate the creation of the resources required for the deployment.
  
# Challenge 2

Write an Ansilbe script to install Apache Webserver, Tomcat Application Server, MySQL Database Server and Oracle JDK softwares.

I have written two playbooks to achieve this requirement treating it to be a two tier Architecture. First playbook will install Apache, Tomcat and JDK softwares and the Second playbook will install the MySQL software completing the stack. I have used ubuntu platform to run the playbooks by provisioning a Ubuntu AWS EC2 Instance to run and have run the playbooks locally on the EC2 instance. The AWS EC2 Instance was provisioned in a Public Subnet with SSH - Port 22 , HTTP - Port 80 allowing access from Internet - "0.0.0.0/0".

Once the EC2 instance was provisioned the first step was to install Ansible.

### Installation on Ansible

Check for updates available for the server

```sh
$ sudo apt update
```
Set the Software Properties
```sh
$ sudo apt install software-properties-common
```
Add Ansible repository 
```sh
$ sudo apt-add-repository --yes --update ppa:ansible/ansible
```
Install Ansible with the command
```sh
$ sudo apt install ansible -y
```
I have explicity allowed the Ports - 80, 8080 and 3306 in the Security Group configuration of the instance. You could also disable the firewall in the Ec2 Instance before the execution of the playbooks. If not even though the Playbook execution might be successful the desired result may not be achieved.

Once Ansible is installed create the YAML extension file to create the playbooks using the below command,
```sh
$ sudo vim file1.yml
```
Click on I for Insert and copy paste the contents of the Playbook - [installation-withoutmysql.yml]  into the file. Press esc and Wq to save the file.

Dependency:
Create a directory called Templates in the Root folder and similarly create another file - Tomcat Service under it. This should be a text file and not a YAML file and will contain the required config for Tomcat Service. Copy the contents of the file Tomcat Services into the fileMake sure your file location is correct in the Copy Tomcat service from local to remote section of the playbook under SRC.

To run the playbook 

```sh
 $ ansible-playbook file1.yml
 ```
 The output of the playbook execution will be like below :
 
  ![Picture](https://github.com/bhaskarsconsortium/assignment/blob/master/Screenshots/Ansible%20-%20File%201%20playbok%20output.JPG)
  
  
 Take the Public IP of the instance and verify the Apache Webserver Homepage as below : "public-ip":80
 
  ![Picture](https://github.com/bhaskarsconsortium/assignment/blob/master/Screenshots/Apache%20Home%20Page%20-%20Ansible%20Playbook.JPG)
  
  
 Take the Public IP of the instance and verify the Tomcat application server Homepage as below : "public-ip":8080
 
 ![Picture](https://github.com/bhaskarsconsortium/assignment/blob/master/Screenshots/Tomcat%20Home%20Page%20-%20Ansible%20Playbook.JPG)
 
 
Similar to the above steps for installing the MySQL Database on to server, please create another file by the name of your choice in YAML and copy the contents of MySQL Ansible Playbook into it. Please run the playbook.

```sh
 $ ansible-playbook file5.yml
 ```

 The output of the playbook execution will be like below :
 
 ![Picture](https://github.com/bhaskarsconsortium/assignment/blob/master/Screenshots/MySQL%20Playbook%20Ouput.JPG)
 
 
 Verify the MySQL installation by logining into the MySQL Database Service as below:
 
 ![Picture](https://github.com/bhaskarsconsortium/assignment/blob/master/Screenshots/MySQL%20Login%20page.JPG)
  
 Screen grabs of the outputs for the Other Tasks/commands provided in the Challange :
 
 Nslookup www.gooogle.com,  telnet localhost 80,  telnet localhost 8080
 
 ![Picture](https://github.com/bhaskarsconsortium/assignment/blob/master/Screenshots/Challange%202%20Commands%20-%20Output.JPG)
  
 Curl https://www.google.com
 
 ![Picture](https://github.com/bhaskarsconsortium/assignment/blob/master/Screenshots/Challange%202%20Commands%20-%20Output%202.JPG)

 telnet localhost 3306
 
 ![Picture](https://github.com/bhaskarsconsortium/assignment/blob/master/Screenshots/Challange%202%20Commands%20-%20Output%203.JPG)
 
 
 # Challange 3
 
 Write the k8 config required to deploy a basic nginx webserver with this image nginxdemos/hello, using minikube to provision your cluster control plane.
 
 Install and Setup of minikube in Windows Laptop with Oracle Virtual Box :
 
 Before you could begin, make sure your virtualization is enabled in your Laptop. To verify that given systeminfo in the Command Prompt. If the Output says A hypervisor has been detected you coul safely move on to the next step. If the desired ouput is not received, enable Hardware Virtualizaiton in the BIOS Settings and try again.
 
 1) Installation of Oracle Virtual Box :
 
 Download and install the Oracle Virutal Box software by selecting Windows hosts in the address - https://www.virtualbox.org/wiki/Downloads
 
 2) Download of Kubectl :
 
Download the latest release v1.17.0 from this link - https://storage.googleapis.com/kubernetes-release/release/v1.17.0/bin/windows/amd64/kubectl.exe

Or if you have curl installed, use this command:

```sh
curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.17.0/bin/windows/amd64/kubectl.exe
```

To find out the latest stable version (for example, for scripting), take a look at https://storage.googleapis.com/kubernetes-release/release/stable.txt.

Save the .exe file by creating a Folder by name Kubernets in C:/ Drive.

3) Download of Minikube : 

We will manually download minikube-windows-amd64 from the link https://github.com/kubernetes/minikube/releases/tag/v1.7.2 and rename it to minikube.exe. We will add this exe also to the Kubernets in C:/ Drive.

Now that we have all the components we will begin the minikube configuration. Please execute the below command in the command prompt,

```sh
C:/Kubernetes> minikube.exe start --vm-driver=virtualbox
```

 This would automatically start the minikube VM creation in Virtual Box and will also initialize the Kubectl for you. You could start using Minikube.
 
 Deploying nginxdemos/hello app in the Kubernetes cluster created : 
 
 ```sh
  kubectl create deployment hello-kube --image=nginxdemos/hello
  ```
  Output will be something similar to this :
  
  ```sh
  deployment.apps/hello-kube created
  ```
  
 To access the hello-kube Deployment, expose it as a Service:

```sh
kubectl expose deployment hello-minikube --type=NodePort --port=8080
```

The option --type=NodePort specifies the type of the Service.

The output is similar to this:

```sh
service/hello-minikube exposed
```

The hello-minikube Pod is now launched but you have to wait until the Pod is up before accessing it via the exposed Service.

Check if the Pod is up and running:

```sh
kubectl get pod
```
If the output shows the STATUS as Running, the Pod is now up and running. Get the URL of the exposed Service to view the Service details:

```sh
minikube service hello-minikube --url
```
Copy and paste the URL you got as the output, on your browser to view the Application Deployed.

For Installing and Setup of minikube in Linux Distro's please refer to the links below. Once minikube is setup please use the same commands as mentioned above for App deployment.

https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl-on-linux
https://kubernetes.io/docs/tasks/tools/install-minikube/

Constraint : Since my Laptop had less disk space which did not allow me to go beyond a point, created a Ubuntu Distro AWS EC2 instance to complete this assignment. Also used the image nginxdemos/hello:plain text image since it is the Linux Distro. Attaching the screen shots for the App deployment below:

Create Kub8 Deployment 

![Picture](https://github.com/bhaskarsconsortium/assignment/blob/master/Kubernetes%20Screenshots/Minikube%20-%20Create%20deployment.JPG)

Kubectl get all

![Picture](https://github.com/bhaskarsconsortium/assignment/blob/master/Kubernetes%20Screenshots/Minikube%20-%20Kubectl%20get%20all.JPG)

Minikube Service Expose

![Picture](https://github.com/bhaskarsconsortium/assignment/blob/master/Kubernetes%20Screenshots/Minikube%20-%20Service%20Expose.JPG)

Minikube URL Output

![Picture](https://github.com/bhaskarsconsortium/assignment/blob/master/Kubernetes%20Screenshots/Minikube%20-%20URL%20Output.JPG)

