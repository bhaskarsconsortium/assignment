# Assignment

Please find below the solutions to the problems given part of the assignment

# Challenge 1!

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
  
# Challenge 2!

Write an Ansilbe script to install Apache Webserver, Tomcat Application Server, MySQL Database Server and Oracle JDK softwares.

I have written two playbooks to achieve this requirement treating it to be a two tier Architecture. First playbook will install Apache, Tomcat and JDK softwares and the Second playbook will install the MySQL software completing the stack. I have used ubuntu platform to run the playbooks by provisioning a Ubuntu AWS EC2 Instance to run and have run the playbooks locally on the EC2 instance. The AWS EC2 Instance was provisioned in a Public Subnet with SSH - Port 22 , HTTP - Port 80 allowing access from Internet - "0.0.0.0/0".

Once the EC2 instance was provisioned the first step was to install Ansible.

### Installation on Ansible

Check for updates available for the server

```sh
$ sudo apt upgrade -y
```
Install the necessary repository with the command 
```sh
sudo apt-add-repository ppa:ansible/ansible.
```
Update apt with the command 
```sh
sudo apt update
```
Install Ansible with the command
```sh
$ sudo apt install ansible -y
```
Once the playbook is installed create the YAML extension files to create the playbooks using the below command,
```sh
$ sudo vim file1.yml
```
Click on I for Insert and copy paste the contents of the Playbook -  into the file. Press esc and Wq to save the file.

Create a directory called Templates in the Root folder and similarly create another file - Tomcat Configurtion under it. This file will contain the required config for Tomcat Service. Make sure your file location is correct in the Copy Tomcat service from local to remote section of the playbook under SRC.

To run the playbook 

```sh
 $ ansible-playbook file1.yml
 ```
