
# Kubernetes-project-using-EKS

## Deploying an application on AWS EKS

#### Introduction

Amazon Elastic Kubernetes Service (Amazon EKS) is a fully managed Kubernetes service provided by Amazon Web Services (AWS). Kubernetes is an open-source container orchestration platform that automates the deployment, scaling, and management of containerized applications.

#### Difference between EKS and Self-managed kubernetes service
Amazon EKS offers a fully managed Kubernetes service that simplifies the operational aspects of running a Kubernetes cluster, while a self-managed Kubernetes service requires users to take on more responsibilities for cluster management and maintenance.

Managed Control Plane: EKS takes care of managing the Kubernetes control plane components, such as the API server, controller manager, and etcd. AWS handles upgrades, patches, and ensures high availability of the control plane.

Automated Updates: EKS automatically updates the Kubernetes version, eliminating the need for manual intervention and ensuring that the cluster stays up-to-date with the latest features and security patches.

Scalability: EKS can automatically scale the Kubernetes control plane based on demand, ensuring the cluster remains responsive as the workload increases.

AWS Integration: EKS seamlessly integrates with various AWS services, such as AWS IAM for authentication and authorization, Amazon VPC for networking, and AWS Load Balancers for service exposure.

Security and Compliance: EKS is designed to meet various security standards and compliance requirements, providing a secure and compliant environment for running containerized workloads.

Monitoring and Logging: EKS integrates with AWS CloudWatch for monitoring cluster health and performance metrics, making it easier to track and troubleshoot issues.

Ecosystem and Community: Being a managed service, EKS benefits from continuous improvement, support, and contributions from the broader Kubernetes community.

## Table of Contents:

1. [Setting up your AWS Environment for EKS](#setting-up-your-aws-environment-for-eks)
   - 1.1 [Creating an AWS Account and Setting up IAM Users](#creating-an-aws-account-and-setting-up-iam-users)
   - 1.2 [Configuring the AWS CLI, kubectl and EKSCTL](#configuring-the-aws-cli,-kubectl-and-eksctl)

2. [Launching your First EKS Cluster](#launching-your-first-eks-cluster)
   - 2.1 [Creating EKS Cluster via AWS CLI using Fargate](#creating-eks-cluster-via-aws-cli-using-fargate)
   - 2.2 [Creating Fargate Profile on EKS Cluster](#creating-fargate-profile-on-eks-cluster)
   - 2.3 [Creating Deployment, Service, Ingress, Namespace](#creating-deployment,-service,-ingress,-namespace)

3. [Creating Ingress Controller](#creating-ingress-controller)
   - 3.1 [Configuring IAM OIDC Provider](#configuring-iam-oidc-provider)
   - 3.2 [Setup ALB Add On and Create IAM Policy, Role](#setup-alb-add-on-and-create-iam-policy,-role)
   - 3.3 [Install Helm And Deploy Using Helm](#install-helm-and-deploy-using-helm)
## Setting up your AWS Environment for EKS

Sure! Let's go into detail for each subsection:

## 1.1 Creating an AWS Account and Setting up IAM Users

Creating an AWS account is the first step to access and utilize AWS services, including Amazon Elastic Kubernetes Service (EKS). Here's a step-by-step guide to creating an AWS account and setting up IAM users:

1. **Create an AWS Account**:
   - Go to the AWS website (https://aws.amazon.com/) and click on the "Create an AWS Account" button.
   - Follow the on-screen instructions to provide your email address, password, and required account details.
   - Enter your payment information to verify your identity and set up billing.

2. **Access AWS Management Console**:
   - After creating the account, you will receive a verification email. Follow the link in the email to verify your account.
   - Log in to the AWS Management Console using your email address and password.

3. **Set up Multi-Factor Authentication (MFA)** (Optional but recommended):
   - Once you are logged in, set up MFA to add an extra layer of security to your AWS account. You can use MFA with a virtual MFA device or a hardware MFA device.

4. **Create IAM Users**:
   - Go to the IAM (Identity and Access Management) service in the AWS Management Console.
   - Click on "Users" in the left-hand navigation pane and then click on "Add user."
   - Enter a username for the new IAM user and select the access type (Programmatic access, AWS Management Console access, or both).
   - Choose the permissions for the IAM user by adding them to one or more IAM groups or attaching policies directly.
   - Optionally, set permissions boundary, tags, and enable MFA for the IAM user.

5. **Access Keys (for Programmatic Access)**:
   - If you selected "Programmatic access" during user creation, you will receive access keys (Access Key ID and Secret Access Key).
   - Store these access keys securely, as they will be used to authenticate API requests made to AWS services.

## 1.2 Configuring the AWS CLI, kubectl and EKSCTL

With IAM users set up, you can now configure the AWS CLI and kubectl on your local machine to interact with AWS services and EKS clusters:

1. **Installing the AWS CLI**:
   - Download and install the AWS CLI on your local machine. You can find installation instructions for various operating systems [here](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-quickstart.html).

2. **Configuring AWS CLI Credentials**:
   - Open a terminal or command prompt and run the following command:
     ```
     aws configure
     ```
   - Enter the access key ID and secret access key of the IAM user you created earlier.
   - Choose a default region and output format for AWS CLI commands.

3. **Installing kubectl**:
   - Install kubectl on your local machine. Instructions can be found [here](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

4. **Configuring kubectl**:
   - Open a terminal or command prompt and run the following command:
     ```
     kubectl
     ```
   - Check kubectl installed or not.

5. **Installing EKSCTL**:
   - Install eksctl on your local machine from aws eks official page.
   - Open a terminal or command prompt and run the following command:
     ```
     eksctl
     ```
   - Check eksctl installed or not.

kubectl – A command line tool for working with Kubernetes clusters. For more information, see Installing or updating kubectl.

AWS CLI – A command line tool for working with AWS services, including Amazon EKS. For more information, see Installing, updating, and uninstalling the AWS CLI in the AWS Command Line Interface User Guide. After installing the AWS CLI, we recommend that you also configure it.

eksctl – A command line tool for working with EKS clusters that automates many individual tasks. For more information, see Installing or updating.

## Launching your First EKS Cluster

### 2.1 Creating EKS Cluster via AWS CLI using Fargate
When you create an EKS cluster, you don't have to worry about the underlying infrastructure of the control plane. AWS EKS handles tasks like patching, updating, and securing the control plane for you. Your responsibility is to manage the worker nodes, which are the instances where your containerized applications run.

So, for Data plane or worker nodes we have to maintain the infrastructure which is AWS EC2 instances or Fargate.

EC2 instances means we have to maintain infrasturcture which is difficult. So, we can use Fargate which is serverless like lambda but lambda works for small environments where as Fargate is for big environments. With Fargate, you can deploy and scale containerized applications directly, without the need to provision or manage EC2 instances. It abstracts the server infrastructure, enabling a more streamlined and simplified container orchestration experience.

Here, we can create fargate worker nodes using AWS CLI commands.
- Enter the following command to create EKS cluster using fargate:
     ```
     eksctl create cluster --name demo-cluster --region us-east-1 --fargate
     ```
It will take some time and will create cluster. You can check in AWS console > AWS EKS > Cluster. All the details will be available in cluster. But, if you have create without EKS we have to create everything manually even to check details.

![pic 1](https://github.com/afrid0315/kubernetes-project-on-EKS/assets/126462435/66e37ba2-5fb6-4c96-bfd5-f22a53fbc5db)

- And Enter the following command to update the kubeconfig file for accessing an Amazon EKS cluster:
     ```
     aws eks update-kubeconfig --name demo-cluster --region us-east-1
     ```

By running this command, you simplify the process of interacting with your EKS cluster using standard Kubernetes command-line tools like 'kubectl'.

![pic 2](https://github.com/afrid0315/kubernetes-project-on-EKS/assets/126462435/99c1d2e9-05a7-4840-8811-b017561e1594)

### 2.2 Creating Fargate Profile on EKS Cluster

In AWS EKS console, if we go to created clusters section > compute > go to bottom > you will find Fargate Profiles > there you can see default, kube-system of namespaces.

We have to add our custom fargate profile without using default profiles.
- Enter the following command to add our custom fargate profile:
     ```
     eksctl create fargateprofile --cluster demo-cluster --region us-east-1 --name alb-sample-app --namespace game-2048
     ```
Reload the AWS EKS Console and check fargate profile, alb-sample-app named namespace will be created.

### 2.3 Creating Deployment, Service, Ingress, Namespace

- Enter the following command:
     ```
     kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.5.4/docs/examples/2048/2048_full.yaml
     ```
This github path taken from official EKS examples of game-2048 and by using 'kubectl apply -f' we are going to create deployment, service and ingress.

- Enter the following command to check pods:
     ```
     kubectl get pods -n game-2048
     ```
- Enter the following command to check service:
     ```
     kubectl get svc -n game-2048
     ```
- Enter the following command to check ingress:
     ```
     kubectl get ingress -n game-2048
     ```
Here, it is having ingress without address in output because ingress controller not at created. For external world we use ingress. We need ALB (Application Load Balancer) ingress controller.

![pic 3](https://github.com/afrid0315/kubernetes-project-on-EKS/assets/126462435/999009d0-6f1b-49b8-bb12-7f8f37b97396)

## Creating Ingress Controller

### 3.1 Configuring IAM OIDC Provider

To create ingress controller we need prerequisite which is IAM OIDC Provider. Kubernetes pods to talk with AWS Services we need to have IAM to be integrated.

So, we need to create OIDC IAM provider
- Enter the following command to check ingress:
     ```
     eksctl utils associate-iam-oidc-provider --cluster $cluster_name --approve
     ```
IAM Authentication to access AWS services will be approved
### 3.2 Setup ALB Add On and Create IAM Policy, Role

- Enter the following command:
     ```
     curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.5.4/docs/install/iam_policy.json
     ```

Create IAM Policy

- Enter the following command:
     ```
     aws iam create-policy \
    --policy-name AWSLoadBalancerControllerIAMPolicy \
    --policy-document file://iam_policy.json
     ```

Create IAM Role

- Enter the following command:
     ```
     eksctl create iamserviceaccount \
  --cluster=<your-cluster-name> \
  --namespace=kube-system \
  --name=aws-load-balancer-controller \
  --role-name AmazonEKSLoadBalancerControllerRole \
  --attach-policy-arn=arn:aws:iam::<your-aws-account-id>:policy/AWSLoadBalancerControllerIAMPolicy \
  --approve
     ```
![pic 4](https://github.com/afrid0315/kubernetes-project-on-EKS/assets/126462435/07129e32-87de-4d0e-abb3-0c856d62a631)

### 3.3 Install Helm And Deploy Using Helm

**Download and Installing Helm**:
   - Before installing helm we need to install chocolatey prerequisite for helm. it may vary to other flavours.
   - Download and Install Helm on your local machine from Helm official page.
   - Open a terminal or command prompt and run the following command:
     ```
     helm
     ```
   - Check helm installed or not.

Helm – Helm is a Kubernetes package manager that facilitates easy deployment and management of applications using pre-configured charts.

**Deploy ALB controller**:

Add helm repo
- Enter the following command:
     ```
     helm repo add eks https://aws.github.io/eks-charts
     ```
Update the repo
- Enter the following command:
     ```
     helm repo update eks
     ```
Install
- Enter the following command:
     ```
     helm install aws-load-balancer-controller eks/aws-load-balancer-controller \            
  -n kube-system \
  --set clusterName=<your-cluster-name> \
  --set serviceAccount.create=false \
  --set serviceAccount.name=aws-load-balancer-controller \
  --set region=<region> \
  --set vpcId=<your-vpc-id>
     ```
![pic 5](https://github.com/afrid0315/kubernetes-project-on-EKS/assets/126462435/eef0e8a1-82a4-4cfb-a2af-3ad319b1c676)

Verify that the deployments are running.
- Enter the following command:
     ```
     kubectl get deployment -n kube-system aws-load-balancer-controller
     ```
## Finally!! You are able to see below output:

![output](https://github.com/afrid0315/kubernetes-project-on-EKS/assets/126462435/23632d2a-40e0-426c-8fcd-2a6168f6644a)

- Enter the following command to delete the cluster:
     ```
     eksctl delete cluster --name demo-cluster --region us-east-1
     ```


