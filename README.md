# k8-super-mario-deployment-with-terraform
check out the blog on this project via
https://dev.to/adesokan_israel_109436759/super-mario-deployment-on-k8-with-terraform-gj4

#****Introduction****
In the ever-evolving world of DevOps and cloud infrastructure, automating deployments has become crucial for managing complex systems efficiently. Kubernetes, with its robust orchestration capabilities, has emerged as the go-to solution for containerized applications, while Terraform allows for infrastructure as code, enabling repeatable and consistent deployments.

Imagine combining these powerful tools to deploy something as iconic as Super Mario! In this guide, we’ll walk you through how to deploy the classic Super Mario game on a Kubernetes cluster using Terraform. This tutorial not only showcases the power of Kubernetes and Terraform but also adds a touch of nostalgia by bringing a beloved game into the world of cloud-native applications. Whether you’re a DevOps enthusiast, a cloud engineer, or a gaming aficionado, this deployment will take you on a fun and educational journey into the realms of modern infrastructure management. Let's dive in and bring Mario to life in the cloud!

Prerequisites:
1. AWS account 
2. An Ubuntu Instance
3. IAM role
4. Terraform should be installed on instance
5. AWS CLI and KUBECTL on Instance

## General project overview

- login and basic setup on AWS

- Setup Docker,Terraform,AWS CLI and kubectl

- Provision IAM role for the EC2

- Attach IAM role with the EC2

- Build the infrastucture using Terraform

- Create service and deployment for EKS

- Destroy the infrastructure
 
## STEP 1: Launch Ubuntu instance
a.**Sign in to AWS Console**: Log in to your AWS Management Console.

b.**Navigate to EC2 Dashboard**: Go to the EC2 Dashboard by selecting “Services” in the top menu and then choosing “EC2” under the Compute section.

c. **Launch Instance**: Click on the “Launch Instance” button to start the instance creation process.

d. **Choose an Amazon Machine Image (AMI)**: Select an appropriate AMI for your instance. For example, you can choose Ubuntu image.

e. **Choose an Instance Type**: In the “Choose Instance Type” step, select t2.micro as your instance type. Proceed by clicking “Next: Configure Instance Details.”

f. **Configure Instance Details**:
- For “Number of Instances,” set it to 1 (unless you need multiple instances).
- Configure additional settings like network, subnets, IAM role, etc., if necessary.
- For “Storage,” click “Add New Volume” and set the size to 8GB (or modify the existing storage to 8GB).
- Click “Next: Add Tags” when you’re done.

g. **Add Tags (Optional)**: Add any desired tags to your instance. This step is optional, but it helps in organizing instances.

h. **Configure Security Group**:
Choose an existing security group or create a new one.
Ensure the security group has the necessary inbound/outbound rules to allow access as required.

i. **Review and Launch**: Review the configuration details. Ensure everything is set as desired.

j.**Select Key Pair**:
- Select “Choose an existing key pair” and choose the key pair from the dropdown.
- Acknowledge that you have access to the selected private key file.
- Click “Launch Instances” to create the instance.

k. **Access the EC2 Instance**: Once the instance is launched, you can access it using the key pair and the instance’s public IP or DNS.
Ensure you have necessary permissions and follow best practices while configuring security groups and key pairs to maintain security for your EC2 instance.


## STEP 2: Create IAM role
Search for IAM in the search bar of AWS and click on roles.
![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/d0pqv27zforn4rrx4vup.PNG)

Create Role 
![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/hdlrv6jelxomm2emyves.PNG)

Select entity type as AWS service

Use case as EC2 and click on Next.
![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/gollbwba4s8gbfg6e251.PNG)

Select Administrator Access for permision policy
![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/jdlxwzfvx5lxaepi1tuk.PNG)

Name the Role 
![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/rnurby4wafz8vd4zpako.PNG)

Role creation successful
![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/w9slxr6j9p0yzxq4yug1.PNG)

Then attach this role to Ec2 instance that we created earlier, so we can provision cluster from that instance.

Go to EC2 Dashboard and select the instance.

Click on Actions –> Security –> Modify IAM role.
![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/d782hwktkj4v3e785k6t.PNG)

Select the Role that created earlier and click on Update IAM role.
![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/7jqll3qf31ffppb3fpxj.PNG)


From this point, you can connect to the server via any means you prefer either: AWS instance connect or putty

STEP 3: Cluster provision
Now clone this Repo.
```sh
git clone https://github.com/Aymogul/k8-super-mario-deployment-with-terraform.git
```
change directory
```sh
cd k8-super-mario-deployment-with-terraform 
```

Update the server and Provide the executable permission to script.sh file, and run it.
```sh
sudo su
sudo apt update -y
```
```sh
sudo chmod +x script.sh
./script.sh
```
The execution of this script will install AWS CLI, Terraform, Kubectl and Docker.

check the versions of installed tools
```sh
docker --version
aws --version
kubectl version --client
terraform --version
```

Now change directory into the EKS-TF

Run Terraform init

NOTE: Don’t forgot to change the s3 bucket name in the backend.tf file
```sh
cd EKS-TF
terraform init
```
![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/dxvvm1p4lzaisxqdnsso.PNG)

The next step is to run validate and plan the terraform deployment
```sh
terraform validate
terraform plan
```
![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/fsiakssu6jmo56qyum6n.PNG)

Provision the cluster by running this command
```sh
terraform apply --auto-approve
```
![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/57u4tqmabqcte5s4pt08.PNG)

Update the Kubernetes configuration

Make sure change your desired region
```sh
aws eks update-kubeconfig --name EKS_CLOUD --region us-west-1
```

Now change directory back to k8-super-mario-deployment-with-terraform 
```sh
cd ..
```
let's now move to the most exciting part of the project as we deploy the game on EKS cluster and enjoy the thrill of super-mario.

## Deployment
```sh
kubectl apply -f deployment.yaml
#to check the deployment 
kubectl get all
```
![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/wde4p7bx37b0imequy68.PNG)

Now let’s apply the service

## Service
```sh
kubectl apply -f service.yaml
kubectl get all
```
Now let’s describe the service and copy the LoadBalancer Ingress
```sh
kubectl describe service mario-service
```
Paste the ingress link in a browser and you will see the Mario game.

# EUREKA
Enjoy the classic thrill of super-mario...
![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/w7q0zy63mi66wkz1pi0m.PNG)
