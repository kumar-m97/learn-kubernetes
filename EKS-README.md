# All about managing EKS on AWS!  

**Objective:** Created an EKS cluster from AWS console with two worker nodes and tried accessing it via kubectl command from an ec2-instance using an IAM user by providing the cluster access to that user.   
**Tip:** Never create an EKS cluster with a root account. Always use an IAM user  

**Steps Taken:**  
> Created EKS cluster from AWS Console while creating Cluster IAM role and Node IAM role parallelly, keeping all other vpc, subnets as default. (Use Custom mode, not Auto)  
- Cluster IAM Role – IAM role created for master node through which it will access all the AWS resources.   
_Select EKSClusterPolicy_ 
- Node IAM Role – IAM role created for worker nodes through which it will access all the required resources.  
_Select EKSWorkernodePolicy,AmazonEC2ContainerRegistryReadOnly,AmazonEKS_CNI_Policy_  

> Created node group for the cluster from the aws console, gave the same Node IAM role, instance type as t2.micro keeping everything else as default.  

> To provide EKS access to an IAM user, create access entries and assign the EKSClusterAdminPolicy. Once done, make sure the Node group status is active with 0 health issues.  

> On the client server, use the same IAM user to set new context in kubectl to connect to the EKS endpoint API.  
**Note:** If the EKS cluster was created with a Private cluster endpoint, The user should be able to interact with EKS cluster only from a Bastion host on the public subnet. SSH into the host in the Public subnet and execute the below command from there after doing aws configure and providing IAM user creds.  
_aws eks update-kubeconfig --region us-east-1 --name master-blaster1_    
Check with any kubectl command to see if we are able to access the cluster.  
**Tip:** You might need to allow 443 Port from the Bastion host sg in the EKS sg.

### In general there are two ways to give EKS cluster access to any new user.  
**1. Via Access Entries from aws console or cli (mentioned above) - New method in latest K8s versions  
2. Via kubeconfig mapping (Older approach, mostly done in lower K8s versions)**
– Cluster admin can edit the aws-auth configmap and add the required user to give cluster access to it.
_kubectl edit configmap aws-auth -n kube-system_  

– Under mapRoles or mapUsers, add entries:  
_mapRoles: |  
  	- rolearn: arn:aws:iam::<account-id>:role/<RoleName>  
	  username: <k8s-username>  
   	    groups:_  

EKS uses OIDC to map Kubernetes service account to IAM Roles.This is crucial for granting fine-grained permissions to pods (e.g., allowing a pod to access S3 or DynamoDB without exposing AWS keys). In simple words, if any service inside a K8S cluster wants to communicate with any AWS resource, it needs OIDC provider which connects the K8S service account to appropriate IAM roles or policies.  

**Here’s the step-by-step process to configure IAM OIDC Provider in EKS**    
1.Export Cluster Name and assign oidc_id  
_oidc_id=$(aws eks describe-cluster --name $cluster_name --query "cluster.identity.oidc.issuer" --output text | cut -d '/' -f 5)_  

2. Check if there is an IAM OIDC provider configured already  
_aws iam list-open-id-connect-providers | grep $oidc_id | cut -d "/" -f4_  

3. If not, run the below command.  
_eksctl utils associate-iam-oidc-provider --cluster $cluster_name --approve_



