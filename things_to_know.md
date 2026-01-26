# Things about K8S you must know!

## SSL/TLS Configuration in Ingress:  
You need to create a secret of type tls and provide the tls.crt and tls.key in the data block.  
<img width="429" height="310" alt="image" src="https://github.com/user-attachments/assets/85d497de-fdee-430c-9b4f-9a76f0163929" />  

Configure it in the Ingress.yml file as below:  
<img width="695" height="790" alt="image" src="https://github.com/user-attachments/assets/783e86b3-bed4-4a03-9c27-4b5c220c95e8" />

**Flow of the HTTPS Request**  
<img width="607" height="343" alt="image" src="https://github.com/user-attachments/assets/5f8d04d5-21f3-4269-bcb0-f47c95c6520c" />


## Deployment Strategies in K8S  

### Rolling Update  
--> Apply in the deployment.yml file under strategy. Create new pods with updated code, wait till its ready and then remove the old pod. makes sure at least N pods are always running.   
![alt text](image.png)

### Blue Green Deployment  
--> Create two deployment.yml files.  
1. app-blue  (current deployment)
2. app-green (new deployment)

Initiall the service will be having app-blue under selector, so the traffic will be routed to the current live server. Meanwhile deploy the new deployment app-green and validate it internally (without sending the actual traffic)  
<img width="1028" height="521" alt="image" src="https://github.com/user-attachments/assets/190c3e7b-6842-4507-b20b-6f27dc17bd3c" />  

Point the service to new deployment app-green using selector. Test it correctly and if something goes wrong, change the service pointing back to app-blue. 
<img width="1041" height="272" alt="image" src="https://github.com/user-attachments/assets/e869f402-67f8-4b4e-8fe2-68e85c1b0f4f" />  
<img width="1038" height="448" alt="image" src="https://github.com/user-attachments/assets/7e300811-caf2-4a92-b16b-b4a639fbb982" />  


### Canary Deployment  
--> The traffic is moved to the new updated deployment gradually, not all at once.  
Assuming the stable version is deployed and running.  
<img width="697" height="679" alt="image" src="https://github.com/user-attachments/assets/1d1e836b-6e45-4e96-8bec-31522c80cffc" />  

Service of this version is below  
<img width="505" height="371" alt="image" src="https://github.com/user-attachments/assets/011bf45e-a5e2-444b-a38c-bbde30173974" />  

Now to deploy new version via canary deployment, we define a new deployment.yml file with version as v2 and new service as well pointing to the new v2 version deployment.  
<img width="705" height="806" alt="image" src="https://github.com/user-attachments/assets/04062124-1796-4c7f-9013-763c88ac02c9" />  

<img width="567" height="491" alt="image" src="https://github.com/user-attachments/assets/935a715d-26a0-4a39-b7d4-fd6d5f771cb2" />  

Now the main ingress should look like the below which is pointing to stable version as of now and sends 100 % traffic
<img width="644" height="602" alt="image" src="https://github.com/user-attachments/assets/a274e2e3-0fe3-490b-8881-1b4e10f08715" />  

Now create a new Canary Ingress with annotations having the canary weight to send 10 % of traffic to the new update version v2 deployment.  
<img width="741" height="693" alt="image" src="https://github.com/user-attachments/assets/4b5583f6-033a-4237-ba5d-032f1160bf79" />

Increase the traffic gradually and test accordingly and once 100% traffic is comming from new Canary deployment, the new version deployment is being served fully.

### Key Config for Zero Downtime  
Configure readiness probe in the deployment. By doing this traffic is sent to only healthy pods.  
<img width="372" height="190" alt="image" src="https://github.com/user-attachments/assets/e8f3e89b-0ed6-4ff1-bc38-872166ad5652" />  

## Rollback strategies in Kubernetes  

### Using Deployment  
In real Kubernetes clusters, rollback is mainly handled by Deployments. Kubernetes keeps old versions (ReplicaSets) so you can go back if a rollout fails.
**Every time you update a Deployment:**  
Kubernetes creates a new ReplicaSet  
Old ReplicaSets are kept (by default last 10)  
Each ReplicaSet = one version of your app 

<img width="1045" height="790" alt="image" src="https://github.com/user-attachments/assets/257586de-5b2d-4b74-a031-6feee5f399d9" />  
<img width="1031" height="425" alt="image" src="https://github.com/user-attachments/assets/1b07ced1-3f6e-4cfa-ae89-009ae5850668" />

### Using Helm  
When you deploy using Helm, rollback is handled at the Helm release level.
Helm tracks every install/upgrade as a release revision and lets you go back to any previous revision.  

We can check the history via  
helm history myapp  

<img width="1025" height="461" alt="image" src="https://github.com/user-attachments/assets/aab8f0ab-8574-45cc-9433-e3e1c8ae746d" />  

<img width="1044" height="493" alt="image" src="https://github.com/user-attachments/assets/f2661a46-d440-4e0c-83ca-c8180801801e" />  


## Network Security and access control  
--> We can configure RBAC  
<img width="1037" height="657" alt="image" src="https://github.com/user-attachments/assets/e1672eaa-a0f3-4a3f-85a3-819600bfad20" />  

--> We can use serviceaccount  
<img width="1043" height="363" alt="image" src="https://github.com/user-attachments/assets/ddaa5f5c-a119-49a9-971e-5c27073add37" />  
<img width="1042" height="453" alt="image" src="https://github.com/user-attachments/assets/3d6fe179-d18e-4da6-a1ae-44aca9a0f803" />  

Now all pods in this Deployment use identity = app-sa.  
Post this, we can create a Role with required access and bind it with the serviceaccount.  
<img width="1081" height="874" alt="image" src="https://github.com/user-attachments/assets/05d33c17-9ec2-4bcc-8b4e-1421a83995dc" />  
<img width="1043" height="245" alt="image" src="https://github.com/user-attachments/assets/518c6598-833d-4230-8682-c5bec6095f7a" />  
<img width="622" height="508" alt="image" src="https://github.com/user-attachments/assets/a8fda223-693e-470b-9c17-386fe47f8018" />


--> We can define Network Policies  
<img width="1082" height="727" alt="image" src="https://github.com/user-attachments/assets/0557287d-cfa8-4652-b95d-89b94c612178" />  

--> We can enable security at Ingress level  
<img width="773" height="209" alt="image" src="https://github.com/user-attachments/assets/d714e344-e32c-4c09-85d1-47cb6f7c192c" />  


## kubectl commands 
![alt text](image-1.png)  

![alt text](image-2.png)  

The above describe command shows  
--Events  
--Scheduling issues  
--Image errors  
--Probe failures  

![alt text](image-3.png)  

![alt text](image-4.png)  

![alt text](image-5.png) 

![alt text](image-6.png)  

![alt text](image-7.png)


## Common K8S Errors  
![alt text](image-8.png)  

![alt text](image-9.png)  

![alt text](image-10.png)  

![alt text](image-11.png)  

![alt text](image-12.png)  

![alt text](image-13.png)  

![alt text](image-14.png)  

**--> Quick Debug Flow**  
![alt text](image-15.png)  


## Taints and Tolerations  
![alt text](image-16.png)  


## Node Affenity  
Using Node affenity, we can tell k8s to place a pod on a specific node if it matched a perticular lable or a pattern  
![alt text](image-17.png)  

## Readiness and Liveness Probe  
### Rediness
It allows to let k8s now if a pod is allowed and ready to handle traffic or not.  
![alt text](image-18.png)  

### Liveness  
![alt text](image-19.png)  













